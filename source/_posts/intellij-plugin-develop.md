---
title: intellij插件开发
date: 2016-08-11 22:09:31
tags:
---


## jdk1.6编译插件

<!--more-->

{% asset_img set_jdk_1.6_1.jpg  %}
{% asset_img set_jdk_1.6_2.jpg  %}

附加一个查看class版本号的命令：

1，进入到类名的路径

2，执行 javap -verbose 类名| findstr "major"

附加： 版本相关的知识——www.stackoverflow.com/questions/10382929/how-to-fix-unsupported-major-minor-version-51-0-error

## 导出插件
点击项目-右键-prepare plugin module ** for deployment

## 遇到的错误

```java

com.intellij.util.IncorrectOperationException: Incorrect method ' public static Test fromJson(JSONObject json) {
        try {
           Test item = new Test();
           item.imageNum = json.optInt("imageNum");

        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }'
	at com.intellij.psi.impl.PsiJavaParserFacadeImpl.newException(PsiJavaParserFacadeImpl.java:411)
	at com.intellij.psi.impl.PsiJavaParserFacadeImpl.createMethodFromText(PsiJavaParserFacadeImpl.java:227)
	at com.intellij.psi.impl.PsiElementFactoryImpl.createMethodFromText(PsiElementFactoryImpl.java:744)
	at com.intellij.psi.impl.PsiJavaParserFacadeImpl.createMethodFromText(PsiJavaParserFacadeImpl.java:236)
```

报IncorrectOperationException的错误，开始以为是没有import语句，然后就找import的语法如何写，api的文档不多，源码有注释，但是没有例子。后面尝试了这样的语句，也没用


```java
String methodFormat = "public static %s fromJson(JSONObject json) {\n" +
                    "        return null;\n" +
                    "    }";
                    
 mClass.add(mFactory.createReferenceFromText("import org.json.JSONObject;", mClass));
 
```

最后转变思路，为啥非要import呢，写类的全名就好了。但是还是报错，最后发现是public前面的特殊空格导致的，去掉就好了



