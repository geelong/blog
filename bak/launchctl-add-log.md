---
title: launchctl给shadowsocks添加日志信息
date: 2016-07-31 15:32:43
tags:
-launchctl
-shadowsocks

---

# launchctl简介

`launchctl`是mac下类似`crontab`的自启工具，`crontab`只能精确到分钟，`launchctl`
能精确到秒

<!-- more -->

# launchctl配置日志的文件

```bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>homebrew.mxcl.shadowsocks-libev</string>
    <key>ProgramArguments</key>
    <array>
      <string>/usr/local/opt/shadowsocks-libev/bin/ss-local</string>
      <string>-c</string>
      <string>/usr/local/etc/shadowsocks-libev.json</string>
      <string>-v</string>
    </array>
    <!-- 标准输出文件 -->
    <key>StandardOutPath</key>
    <string>/Users/geelong/logs/shadowsocks-out.log</string>
    <!-- 标准错误输出文件 -->
    <key>StandardErrorPath</key>
    <string>/Users/geelong/logs/shadowsocks-err.log</string>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <dict>
      <key>SuccessfulExit</key>
      <false/>
    </dict>
  </dict>
</plist>
 ```

# 重启服务
查看日志是否已经有了，最开始服务并没有启动，需要设置日志logs文件夹的权限后，才能启动  

```bash
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.shadowsocks-libev.plist
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.shadowsocks-libev.plist
ps -ef | grep shadow
```

