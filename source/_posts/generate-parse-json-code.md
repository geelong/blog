---
title: 生成解析json的代码
date: 2016-08-08 21:58:04
tags:
- json

---

下面的工具可以根据json的数据，自动生成json的解析代码

使用步骤

## 1. GsonFormat安装
- AndroidStudio-preferences-plugins-install plugin from disk
- 选择{% asset_link plugin_gsonformat.jar plugin_gsonformat.jar %}文件 

- 根据提示，重启AndroidStudio

<!--more-->

## 2.生成Json解析代码
### alt+s 弹出生成代码的对话框(快捷键可能不同，可以在快捷键列表里查一下)
{% asset_img input_dlg.png %}
### 在对话框里张贴json格式的数据
~~~java
{
"url":"abc",
"urls":["abc"],
"type":1,
"iamge":{
    "desc":{
            "name":"abc",
            "age": 123
        }
    }
}
~~~

{% asset_img select_field_dlg.png %}
一直点击OK
### 生成代码如下

~~~java
package com;

import org.json.JSONArray;
import org.json.JSONObject;

import java.util.ArrayList;
import java.util.List;

/**
 * Created on 16/9/6.
 */
public class Test {
    private static final String URLS = "urls";
    private static final String IAMGE = "iamge";
    private static final String URL = "url";
    private static final String TYPE = "type";
    /**
     * url : abc
     * urls : ["abc"]
     * type : 1
     * iamge : {"desc":{"name":"abc","age":123}}
     */

    private String url;
    private int type;
    /**
     * desc : {"name":"abc","age":123}
     */

    private IamgeBean iamge;
    private List<String> urls;

    /**
     * auto generate from gsonfromat
     * @param json
     * @return null if parse error
     */
    public static Test fromJson(JSONObject json) {
        try {
            Test item = new Test();
            item.url = json.has(URL) ? json.optString(URL) : "";
            item.type = json.optInt(TYPE);
            JSONObject iamgeJson = json.optJSONObject(IAMGE);
            if (iamgeJson != null) {
                item.iamge = IamgeBean.fromJson(iamgeJson);
            }
            JSONArray urlsJsonArray = json.optJSONArray(URLS);
            List<String> urlsList = new ArrayList<>();
            if (urlsJsonArray != null && urlsJsonArray.length() > 0) {
                for (int i = 0; i < urlsJsonArray.length(); i++) {
                    if (!urlsJsonArray.isNull(i)) {
                        urlsList.add(urlsJsonArray.optString(i));
                    }
                }
            }
            item.urls = urlsList;
            return item;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public int getType() {
        return type;
    }

    public void setType(int type) {
        this.type = type;
    }

    public IamgeBean getIamge() {
        return iamge;
    }

    public void setIamge(IamgeBean iamge) {
        this.iamge = iamge;
    }

    public List<String> getUrls() {
        return urls;
    }

    public void setUrls(List<String> urls) {
        this.urls = urls;
    }

    public static class IamgeBean {
        private static final String DESC = "desc";
        /**
         * name : abc
         * age : 123
         */

        private DescBean desc;

        /**
         * auto generate from gsonfromat
         * throw exception if parse error
         * @param json
         * @return
         */
        public static IamgeBean fromJson(JSONObject json) {
            IamgeBean item = new IamgeBean();
            JSONObject descJson = json.optJSONObject(DESC);
            if (descJson != null) {
                item.desc = DescBean.fromJson(descJson);
            }
            return item;
        }

        public DescBean getDesc() {
            return desc;
        }

        public void setDesc(DescBean desc) {
            this.desc = desc;
        }

        public static class DescBean {
            private static final String NAME = "name";
            private static final String AGE = "age";
            private String name;
            private int age;

            /**
             * auto generate from gsonfromat
             * throw exception if parse error
             * @param json
             * @return
             */
            public static DescBean fromJson(JSONObject json) {
                DescBean item = new DescBean();
                item.name = json.has(NAME) ? json.optString(NAME) : "";
                item.age = json.optInt(AGE);

                return item;
            }

            public String getName() {
                return name;
            }

            public void setName(String name) {
                this.name = name;
            }

            public int getAge() {
                return age;
            }

            public void setAge(int age) {
                this.age = age;
            }
        }
    }
}
~~~


## 3.Code Review

对于嵌套结构复杂的代码，最好进行codeReview





