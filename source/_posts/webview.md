---
title: webview
date: 2017-02-14 16:14:29
tags:
---

Alert无法弹出（myWebView.loadUrl("javascript:alert('a');");）

- myWebView.setWebChromeClient(new WebChromeClient() {});