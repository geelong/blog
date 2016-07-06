###把Android代码的Eclipse工程配置文件复制到android源码根目录下

$ cp development/ide/eclipse/.classpath ./
修改eclipse程序的配置

修改eclipse缓存设置: 把eclipse.ini（在eclipse软件的安装目录下）的3个值改为下面的值
-Xms128m
-Xmx512m
-XX:MaxPermSize=256m

说明
JVM堆的设置是指java程序运行过程中JVM可以调配使用的内存空间的设置.
JVM在启动的时候会自动设置Heap size的值
其初始空间(即-Xms)是物理内存的1/64，最大空间(-Xmx)是物理内存的1/4。可以利用JVM提供的-Xmn -Xms -Xmx等选项可进行设置。
如果在导入过程中出现 java heap space， 可以尝试把上面的值改大， 我在导入Android4.1代码时就发生了此错误。
修改Eclipse的代码风格等：
相关文件介绍
可以简单的将android-formatting.xml和android.importorder导入eclipse
位置： android-formatting.xml、.classpath和android.importorder都放在development/ide/eclipse/下
android-formatting.xml用来配置eclipse编辑器的代码风格
android.importorder用来配置eclipse的import的顺序和结构。
导入方法
在window->preferences->java->Code style->Formatter中导入android-formatting.xml
在window->preferences->java->Code style->Organize Imports中导入android.importorder


###把android源码作为一个工程导入eclipse
注意：导入前先检查.classpath里的文件在android源码中是否有相应的文件（文件夹），否则会破坏android源码（一般是多添加文件/文件夹），.classpath里多余的路径可删除
最好重新换一个workspace，因为源码是比较庞大的。

直接new，然后选择C++，然后use default location，选择你的源码文件，点finish，ok。。。 
然后。。。等待。。。再然后，基本可以了~~java文件应该可以快捷（F3）访问了，C++也许还有点麻烦问题~~ 