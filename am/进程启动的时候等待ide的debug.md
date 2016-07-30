##把某个APP进程启动的时候设置为debug

adb shell am set-debug-app -w --persistent $packageName

##某包下的某进程
adb shell am set-debug-app -w --persistent $packageName:$processName

##取消debug
am clear-debug-app