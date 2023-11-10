
---
title: "Redis Type"
---

## redisObject

Redis 类型系统的核心，数据库中的每个键、值，以及Redis 本身处理的参数，都表示为这种数据类型

```c
// server.h
/* The actual Redis Object */
#define OBJ_STRING 0    /* String object. */
#define OBJ_LIST 1      /* List object. */
#define OBJ_SET 2       /* Set object. */
#define OBJ_ZSET 3      /* Sorted set object. */
#define OBJ_HASH 4      /* Hash object. */

/* Objects encoding. Some kind of objects like Strings and Hashes can be
 * internally represented in multiple ways. The 'encoding' field of the object
 * is set to one of this fields for this object. */
#define OBJ_ENCODING_RAW 0     /* Raw representation */ 						// 编码为字符串
#define OBJ_ENCODING_INT 1     /* Encoded as integer */ 						// 编码为整数
#define OBJ_ENCODING_HT 2      /* Encoded as hash table */					// 编码为哈希表
#define OBJ_ENCODING_ZIPMAP 3  /* Encoded as zipmap */
#define OBJ_ENCODING_LINKEDLIST 4 /* No longer used: old list encoding. */
#define OBJ_ENCODING_ZIPLIST 5 /* Encoded as ziplist */							// 编码为压缩列表
#define OBJ_ENCODING_INTSET 6  /* Encoded as intset */							// 编码为整数集合
#define OBJ_ENCODING_SKIPLIST 7  /* Encoded as skiplist */					// 编码为跳跃表
#define OBJ_ENCODING_EMBSTR 8  /* Embedded sds string encoding */
#define OBJ_ENCODING_QUICKLIST 9 /* Encoded as linked list of ziplists */
#define OBJ_ENCODING_STREAM 10 /* Encoded as a radix tree of listpacks */

typedef struct redisObject {
    // 数据类型，OBJ_STRING等
    unsigned type:4;
    // 编码方式，OBJ_ENCODING_RAW等
    unsigned encoding:4;
    // #define LRU_BITS 24
    // LRU 时间	
    unsigned lru:LRU_BITS; /* LRU time (relative to global lru_clock) or
                            * LFU data (least significant 8 bits frequency
                            * and most significant 16 bits access time). */
    // 引用计数 
    int refcount;
    // 指向对象的值
    void *ptr;
} robj;
```

## 类型自省

### `type keyname`

**type** REDIS_STRING, REDIS_LIST, REDIS_HASH, REDIS_SET or REDIS_ZSET, 

### `object encoding keyname`

**encoding** int, embstr or raw for REDIS_STRING, 

### `object idletime keyname (unit is second)`

**lru** access time

### `object refcount keyname`

only for number-format string, shared object ref count

## String

### Simple Dynamic String

```c
struct sdshdr {
    int len;
    int free;
    char buf[];
};
```

### REDIS_STRING

**int**, 8 bytes long

**embstr** <=39 bytes readonly string

**raw** > 39 bytes
