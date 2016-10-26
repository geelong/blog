---
title: adb am start uri丢参数的问题
date: 2016-10-26 23:14:32
tags:
---



adb shell am start uri会丢掉第二个后面的所有参数，需要在&前加\转义，并使用下面的方式打开

<!--more-->

```bash
echo 'am start abc://host?url=a\&b=c; exit' | adb shell
```

shell 脚本

```bash
app_start.sh

cmd='am start '$@' ;exit'
echo $cmd | adb shell
```

用法

```bash
app_start "abc://host?url=a\&b=c"
```