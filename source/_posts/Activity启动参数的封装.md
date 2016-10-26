---
title: Activity启动参数的封装
date: 2016-10-26 23:09:14
tags:
---


给activity传参比较繁琐，有的直接在调用处构造intent，然后传参。但是这种方式不够内聚，删除参数的时候到处都需要改动，打开页面写相关代码也费时，容易漏参数。下面讨论几种传参数的方法


<!--more-->
### 1、每个页面提供打开自身的静态方法


```java
实现

public class SearchAct {
    public static void startAct(Context ctx, boolean showSearch, boolean showClose){
    //impl
    }
}

调用方法
SearchAct.startAct(act, true, true)
```

添加参数后的代码

```java
public class SearchAct {

public static void startAct(Context ctx, boolean showSearch, boolean showClose,
boolean showTitle){
//impl
}

public static void startAct(Context ctx, boolean showSearch, boolean showClose){
    startAct(ctx, showSearch,showClose, false);
}

调用方法
SearchAct.startAct(act, true, true, true)
```

存在的问题
- 可读性不高
- 扩展性不好，当添加一个参数的时候需要增加1个方法
- 获取参数的地方也需要添加代码

### 2、用Builder改进

```java

public class SearchBuilder {
    private boolean showClose;
    private boolean showSearch;
    private boolean showTitle;

    public SearchBuilder setShowClose(boolean showClose) {
        this.showClose = showClose;
        return this;
    }

    public SearchBuilder setShowSearch(boolean showSearch) {
        this.showSearch = showSearch;
        return this;
    }

    public SearchBuilder setShowTitle(boolean showTitle) {
        this.showTitle = showTitle;
        return this;
    }

    public void startAct(Context ctx) {
        //impl;
    }
}

调用方法
new SearchBuilder()
.setShowClose(true)
.setShowSearch(true)
.setShowTitle(true)
.startAct(ctx);

```
优点
- 可读性增强

缺点：
- 增加参数，还是需要在builder里添加方法
- 造intent和解析intent的时候也要添加对应代码

Builder的意义
- 提高可读性
- 参数的检测和提供默认值

### 3、用Parcelable+Builder改造

```java
public class SearchParam implements Parcelable{
    boolean showSearch;
    boolean showClose;
    boolean showTitle;

    public SearchParam(boolean showClose, boolean showSearch, boolean showTitle) {
        this.showClose = showClose;
        this.showSearch = showSearch;
        this.showTitle = showTitle;
    }

    public SearchParam() {
    }

    public SearchParam setShowClose(boolean showClose) {
        this.showClose = showClose;
        return this;
    }

    public SearchParam setShowSearch(boolean showSearch) {
        this.showSearch = showSearch;
        return this;
    }

    public SearchParam setShowTitle(boolean showTitle) {
        this.showTitle = showTitle;
        return this;
    }

    protected SearchParam(Parcel in) {
        showSearch = in.readByte() != 0;
        showClose = in.readByte() != 0;
        showTitle = in.readByte() != 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeByte((byte) (showSearch ? 1 : 0));
        dest.writeByte((byte) (showClose ? 1 : 0));
        dest.writeByte((byte) (showTitle ? 1 : 0));
    }

    @Override
    public int describeContents() {
        return 0;
    }

    public static final Creator<SearchParam> CREATOR = new Creator<SearchParam>() {
        @Override
        public SearchParam createFromParcel(Parcel in) {
            return new SearchParam(in);
        }

        @Override
        public SearchParam[] newArray(int size) {
            return new SearchParam[size];
        }
    };
}

调用方调用

SearchParam param = new SearchParam()
        .setShowClose(true)
        .setShowSearch(true)
        .setShowTitle(true);
SearchAct.startAct(ctx, param);

```

优点：
- 添加参数只需要修改builder的代码，不再需要修改构造Intent和从Intent里获取方法的代码

注意：
- 添加参数后，需要更新Parcelable接口实现(通过ide更新一下就好)
- Builder也可以在生成set的时候通过ide生成