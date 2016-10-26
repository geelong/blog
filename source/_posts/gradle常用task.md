---
title: gradle常用task
date: 2016-09-21 11:44:16
tags:
---

查看具体task帮助
`
./gradlew help --task  app.home:dependencies`

查看某个task的依赖

`./gradlew help --task  app.home:dependencies --configuration compile`