
---
title: "IDEA"
---

## config

### 乱码

#### .properties 配置文件中文乱码

File -> Setting -> Editor -> File encodings -> 勾选 Transparent native-to-ascll conversion

## issue

### 双屏双击idea项目窗口，该窗口可能缩小到看不见

**`~/Library/Application Support/JetBrains/IntelliJIdea2023.3/options/recentProjects.xml`**

frame 的 width 和 height，手动设置 height = 更大的值之和再次重新打开idea即可看到该项目了

```xml
<entry key="$USER_HOME$/repo/myproj">
    <value>
        <RecentProjectMetaInfo frameTitle="myproj" opened="true" projectWorkspaceId="xxx">
            <frame x="0" y="0" width="340" height="1" />
        </RecentProjectMetaInfo>
    </value>
</entry>
```

```shell
ln -s "$HOME/Library/Application Support/JetBrains/IntelliJIdea2023.3/options/recentProjects.xml" recentProjects.xml
```
