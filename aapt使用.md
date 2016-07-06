###使用aapt生成资源包文件

%aapt% package -f -M %ANDROID_MANIFEST_XML% -S %RES% -A %ASSETS% -I %ANDROID_JAR% -F %RESOURCE%

%GEN%:存放的R.java文件夹路径。
%RES%:res文件夹路径。
%ANDROID_JAR%:引用的android.jar路径。
%ANDROID_MANIFEST_XML%:工程AndroidManifest.xml绝对路径。
%ASSETS%:asset文件夹路径。
%RESOURCE%:生成的resouces.arsc存放路径。


###demo
aapt package  -v -f -M ./AndroidManifest.xml -S res -I ~/developer/android-sdk-macosx/platforms/android-23/android.jar -F resources.arsc
