---
title: python解析html页面内容
date: 2016-09-11 11:53:56
tags:
- python
- 解析html

---

## 下面的代码是使用Python解析HTML里的内容

<!--more-->

```python
#! /usr/bin/python
# -*- coding:utf-8 -*-

from pyquery import PyQuery as pq

content = ''
with open('python_html.txt', 'r') as f:
    content = f.read()
# doc = pq(url='https://www.python.org/events/python-events/')
doc = pq(content)

# .list-recent-events 是查找 class="list-recent-events"的节点
# li 是查找标签为li的节点
for event in doc('.list-recent-events li'):
    event = pq(event)
    # find 是根据class或者标签来查找元素
    loc = event.find('.event-locåation').text()

    time = event.find('time').text()

    name = event.find('.event-title').text()

    print 'event:%s' % name

    print '\ttime:%s' % time

    print '\tlocation:%s' % loc

```

## pyquery的安装

```bash
sudo  pip install lxml
sudo pip install pyquery
```