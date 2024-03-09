
---
title: "Doxygen"
---

## install

```shell
brew install doxygen
touch Doxyfile
# doxygen Doxyfile
doxygen
```

## config

```conf
USE_MDFILE_AS_MAINPAGE = ./README.md
PROJECT_NUMBER = 1.0.0
#CREATE_SUBDIRS = YES

GENERATE_LATEX         = NO

DISABLE_INDEX          = YES
#GENERATE_TREEVIEW      = YES

EXTRACT_ALL            = YES

CALL_GRAPH             = YES
OUTPUT_LANGUAGE        = Chinese

# RECURSIVE              = YES
# https://www.doxygen.nl/manual/config.html#cfg_input
INPUT = some.java \
        some_dir \
        ./README.md
# FILE_PATTERNS = *.java
```

```markdown
[page](pages.html)

[namespaces](namespaces.html)

[annotated](annotated.html)

[classes](classes.html)

[funtions](funtions.html)

[files](files.html)
```
