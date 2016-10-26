---
title: ThreadPoolExecutor使用说明
date: 2016-09-13 16:46:12
tags:

- ThreadPoolExecutor
---


### 重点讲解： 
其中比较容易让人误解的是：corePoolSize，maximumPoolSize，workQueue之间关系。 

- 当线程池小于corePoolSize时，新提交任务将创建一个新线程执行任务，即使此时线程池中存在空闲线程。 
- 当线程池达到corePoolSize时，新提交任务将被放入workQueue中，等待线程池中任务调度执行 
- 当workQueue已满，且maximumPoolSize>corePoolSize时，新提交任务会创建新线程执行任务 
- 当提交任务数超过maximumPoolSize时，新提交任务由RejectedExecutionHandler处理 
- 当线程池中超过corePoolSize线程，空闲时间达到keepAliveTime时，关闭空闲线程 
- 当设置allowCoreThreadTimeOut(true)时，线程池中corePoolSize线程空闲时间达到keepAliveTime也将关闭 

参考文章：[JAVA进阶----ThreadPoolExecutor机制（转载）](http://langgufu.iteye.com/blog/2184915)
