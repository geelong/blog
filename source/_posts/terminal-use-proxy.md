---
title: Mac终端设置代理
date: 2016-07-31 15:01:39
tags:
- proxychains

---

# 使用proxychains

[proxychains安装和设置参考文章](http://blog.fazero.cc/2015/08/31/%E5%88%A9%E7%94%A8proxychains%E5%9C%A8%E7%BB%88%E7%AB%AF%E4%BD%BF%E7%94%A8socks5%E4%BB%A3%E7%90%86/)


## 问题

`proxychains ping www.google.com`

还是没走代理

<!-- more -->

# git全局设置
设置后还是很快的

```bash
git config --global http.proxy 'socks5://127.0.0.1:1080'  

git config --global https.proxy 'socks5://127.0.0.1:1080'
```
  

shadowsocks日志输出需要加 -v参数	

`ss-local -c /usr/local/etc/shadowsocks-libev.json -v `