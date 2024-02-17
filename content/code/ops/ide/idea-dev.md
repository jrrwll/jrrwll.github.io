
---
title: "IDEA Dev"
---

## plugin dev

https://plugins.jetbrains.com/plugin/22851-plugin-devkit

### doc

https://www.ideaplugin.com/idea-docs/startingAlsonEnding.html

**扩展**

https://plugins.jetbrains.com/intellij-platform-explorer/extensions

https://plugins.jetbrains.com/docs/intellij/oss-plugins-extension-point-list.html

**界面设计**

https://jetbrains.design/intellij/

https://jetbrains.design/intellij/resources/icons_list/

### cache-redirector

```shell
wget https://cache-redirector.jetbrains.com/www.jetbrains.com/intellij-repository/releases/com/jetbrains/intellij/idea/ideaIC/2023.2/ideaIC-2023.2.zip
mkdir -p ~/.gradle/caches/modules-2/files-2.1/com.jetbrains.intellij.idea/ideaIC/2023.2/

shasum ideaIC-2023.2.zip
sha1sum.exe ideaIC-2023.2.zip
```

## PSI Viewer

https://www.jetbrains.com/help/idea/psi-viewer.html

From the Tools menu, select View PSI Structure.

```properties
# https://github.com/JetBrains/psiviewer
# idea.properties
idea.is.internal=true
```
