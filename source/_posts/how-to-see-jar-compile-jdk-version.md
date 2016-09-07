---
title: 如何查看jar编译的jdk版本
date: 2016-08-10 10:37:46
tags:
---

### 提取class文件
`jar -xf plugin_translate.jar com/royll/varnamegodie/VarNameTranslate.class`


### 查看版本

`javap -verbose com/royll/varnamegodie/VarNameTranslate.class  | head -20`

```
  minor version: 0
  major version: 52
```

52代表jdk8