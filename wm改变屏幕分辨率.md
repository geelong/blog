改为720*1280的命令

adb shell wm size 720x1280

adb shell wm density 320

adb reboot



240x320 density=120 320dipx426.6dip
320x480 density=160 320dipx480dip 
480x800 density=240 320dipx533.3dip 
480x854 density=240 320dipx569.33dip


===
原来手机的

分辨率
1440x2560

density: 560

如果改为720x1280

新density=旧density/(原始分辨率宽/新分辨率宽)

         =560/（1440/720）
         
         =280

设置

adb shell wm size 720x1280

adb shell wm density 280

###记得重启手机
adb reboot


###经过和1280*720的对比
###density=320效果一致



px = (density/160)dp

density一般为3个常用固定值240/160/120


nexus6 density=560  scalDensity=560/160=3.5

红米2 density=320  scalDensity=320/160=2

density值表示每英寸有多少个显示点，与分辨率是两个概念。

不同density下屏幕分辨率信息：

480*800的WVGA(density=240)


