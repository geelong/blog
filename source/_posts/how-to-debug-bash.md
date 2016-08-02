---
title: 如何debug bash
date: 2016-08-01 21:53:02
tags:
- bash

---

下面是一个新建blog文章的脚本

<!--more-->

```bash
if [ $# -lt 1 ]; then
   echo "new file name"
   exit 1
fi

title=$1
blogPath='/Users/geelong/blog3_hexo'
cd $blogPath
hexoResult=`hexo new $title`
logFile=`echo $hexoResult |  awk -F "blog3_hexo/" '{print $NF}'`
echo $logFile
open  $logFile
```

debug命令

```bash
bogon:blog3_hexo geelong$ bash -x ~/bin/new_blog how-to-debug-bash
+ '[' 1 -lt 1 ']'
+ title=how-to-debug-bash
+ blogPath=/Users/geelong/blog3_hexo
+ cd /Users/geelong/blog3_hexo
++ hexo new how-to-debug-bash
+ hexoResult='INFO  Created: ~/developer/workspace_3rd/blog3_hexo/source/_posts/how-to-debug-bash.md'
++ echo INFO Created: '~/developer/workspace_3rd/blog3_hexo/source/_posts/how-to-debug-bash.md'
++ awk -F blog3_hexo/ '{print $NF}'
+ logFile=source/_posts/how-to-debug-bash.md
+ echo source/_posts/how-to-debug-bash.md
source/_posts/how-to-debug-bash.md
+ open source/_posts/how-to-debug-bash.md
```