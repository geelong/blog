HTTPS抓包
在 iOS 设备上打开这个网址 http://www.charlesproxy.com/getssl 安装 Charles SSL 证书
在Charles的工具栏上点击设置按钮，选择Proxy Settings…
切换到SSL选项卡，选中Enable SSL Proxying，别急，选完先别关掉，还有下一步
这一步跟Fiddler不同，Fiddler安装证书后就可以抓HTTPS网址的包了，Charles则麻烦一些，需要在上一步的SSL选项卡的Locations表单填写要抓包的域名和端口，点击Add按钮，在弹出的表单中Host填写域名，比如填api.instagram.com，Port填443