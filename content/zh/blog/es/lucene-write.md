---
title: "Lucene IndexWriter"
linkTitle: "Lucene IndexWriter"
date: 2022-04-29
weight: 9
---

## IndexWriterConfig

**IndexDeletionPolicy**：Lucene开放对commit point的管理，通过对commit point的管理可以实现例如snapshot等功能。Lucene默认配置的DeletionPolicy，只会保留最新的一个commit point

**Similarity**：搜索的核心是相关性，Similarity是相关性算法的抽象接口，Lucene默认实现了TF-IDF和BM25算法。相关性计算在数据写入和搜索时都会发生，数据写入时的相关性计算称为Index-time boosting，计算Normalizaiton并写入索引，搜索时的相关性计算称为query-time boosting

**MergePolicy**：Lucene内部数据写入会产生很多Segment，查询时会对多个Segment查询并合并结果。所以Segment的数量一定程度上会影响查询的效率，所以需要对Segment进行合并，合并的过程就称为Merge，而何时触发Merge由MergePolicy决定

**MergeScheduler**：当MergePolicy触发Merge后，执行Merge会由MergeScheduler来管理。Merge通常是比较耗CPU和IO的过程，MergeScheduler提供了对Merge过程定制管理的能力

**Codec**：Codec可以说是Lucene中最核心的部分，定义了Lucene内部所有类型索引的Encoder和Decoder。Lucene在Config这一层将Codec配置化，主要目的是提供对不同版本数据的处理能力。对于Lucene用户来说，这一层的定制需求通常较少，能玩Codec的通常都是顶级玩家了

**IndexerThreadPool**：管理IndexWriter内部索引线程（DocumentsWriterPerThread）池，这也是Lucene内部定制资源管理的一部分

**FlushPolicy**：FlushPolicy决定了In-memory buffer何时被flush，默认的实现会根据RAM大小和文档个数来判断Flush的时机，FlushPolicy会在每次文档add/update/delete时调用判定

**MaxBufferedDoc**：Lucene提供的默认FlushPolicy的实现FlushByRamOrCountsPolicy中允许DocumentsWriterPerThread使用的最大文档数上限，超过则触发Flush

**RAMBufferSizeMB**：Lucene提供的默认FlushPolicy的实现FlushByRamOrCountsPolicy中允许DocumentsWriterPerThread使用的最大内存上限，超过则触发flush

**RAMPerThreadHardLimitMB**：除了FlushPolicy能决定Flush外，Lucene还会有一个指标强制限制DocumentsWriterPerThread占用的内存大小，当超过阈值则强制flush

**Analyzer**：即分词器，这个通常是定制化最多的，特别是针对不同的语言

## 核心操作

**addDocument**：比较纯粹的一个API，就是向Lucene内新增一个文档。Lucene内部没有主键索引，所有新增文档都会被认为一个新的文档，分配一个独立的docId

**updateDocuments**：更新文档，但是和数据库的更新不太一样。数据库的更新是查询后更新，Lucene的更新是查询后删除再新增。流程是先delete by term，后add document。但是这个流程又和直接先调用delete后调用add效果不一样，只有update能够保证在Thread内部删除和新增保证原子性，详细流程在下一章节会细说

**deleteDocument**：删除文档，支持两种类型删除，by term和by query。在IndexWriter内部这两种删除的流程不太一样，在下一章节再细说

**flush**：触发强制flush，将所有Thread的In-memory buffer flush成segment文件，这个动作可以清理内存，强制对数据做持久化

**prepareCommit/commit/rollback**：commit后数据才可被搜索，commit是一个二阶段操作，prepareCommit是二阶段操作的第一个阶段，也可以通过调用commit一步完成，rollback提供了回滚到last commit的操作

**maybeMerge/forceMerge**：maybeMerge触发一次MergePolicy的判定，而forceMerge则触发一次强制merge

## 数据路径

![alt](/img/777b45faf3214fd5abb53baaacf8c210.png)

### 并发模型

空间隔离式的数据写入方式

1. 多线程并发调用IndexWriter的写接口，在IndexWriter内部具体请求会由DocumentsWriter来执行。DocumentsWriter内部在处理请求之前，会先根据当前执行操作的Thread来分配`DocumentsWriterPerThread`（DWPT）
2. 每个线程在其独立的DocumentsWriterPerThread空间内部进行数据处理，包括分词、相关性计算、索引构建等
3. 数据​处理完毕后，在DocumentsWriter层面执行一些后续动作，例如触发FlushPolicy的判定等

只需要对以上第一步和第三步进行加锁。每个DWPT内单独包含一个In-memory buffer，这个buffer最终会flush成不同的独立的segment文件

### add & update

`updateDocument`

1. 根据Thread分配DWPT
2. 在DWPT内执行delete
3. 在DWPT内执行add

### delete

在IndexWriter内部会有一个全局的Deletion Queue，称为Global Deletion Queue，而在每个DWPT内部，还会有一个独立的Deletion Queue，称为Pending Updates。DWPT Pending Updates会与Global Deletion Queue进行双向同步，因为文档删除是全局范围的，不应该只发生在DWPT范围内

Segment中有一个特殊的文件叫live docs，内部是一个位图的数据结构，记录了这个Segment内部哪些DocId是存活的，哪些DocId是被删除的。所以删除的过程就是构建live docs标记位图的过程，数据实际上不会被真正删除，只是在live docs里会被标记删除。live docs只影响倒排，所以在live docs里被标记删除的文档没有办法通过倒排索引检索出，但是还能够通过doc id查询到store fields。文档数据最终是会被真正物理删除，这个过程会发生在merge时

### flush

flush是将DWPT内In-memory buffer里的数据持久化到文件的过程，flush会在每次新增文档后由FlushPolicy判定自动触发，也可以通过IndexWriter的flush接口手动触发。每个DWPT会flush成一个segment文件，flush完成后这个segment文件是不可被搜索的，只有在commit之后，所有commit之前flush的文件才可被搜索

### commit

commit时会触发数据的一次强制flush，commit完成后再此之前flush的数据才可被搜索。commit动作会触发生成一个commit point，commit point是一个文件。Commit point会由IndexDeletionPolicy管理，lucene默认配置的策略只会保留last commit point

### merge

merge是对segment文件合并的动作，合并的好处是能够提高查询的效率以及回收一些被删除的文档。Merge会在segment文件flush时触发MergePolicy来判定自动触发，也可通过IndexWriter进行一次force merge

## IndexingChain

在IndexWriter内部，indexing chain上索引构建顺序是invert index、store fields、doc values和point values

### `Codec`，每种类型索引的Encoder和Decoder

- BlockTreeTermsWriter：倒排索引对应的Codec，其中倒排表部分使用Lucene50PostingsWriter(Block方式写入倒排链)和Lucene50SkipWriter(对Block的SkipList索引)，词典部分则是使用FST（针对倒排表Block级的词典索引）
- CompressingTermVectorsWriter：对应Term vector索引的Writer，底层是压缩Block格式
- CompressingStoredFieldsWriter：对应Store fields索引的Writer，底层是压缩Block格式
- Lucene70DocValuesConsumer：对应Doc values索引的Writer
- Lucene60PointsWriter：对应Point values索引的Writer