---
title: 动态代理使用
date: 2017-01-12 22:41:05
tags:
---

### 写一个存储模块，接口如下，测试的时候需要计算存储的耗时

```java
 public interface IStore {
 
        boolean save(String key, String val);

        String get(String key);

        void remove(String key);
    }
```

<!--more-->

### 使用动态代理之前，实现如下

- 每个方法都添加统计的代码
- 如果统计的方式需要修改，所有地方都要改
- 上线的时候不好移除

```java
public class StoreFileImpl implements StoreFactory.IStore {

    @Override
    public boolean save(String key, String val) {
		 long start = SystemClock.elapsedRealtime();
		 //impl
		 Log.d("per", "save cost " + (SystemClock.elapsedRealtime() - start));
        return true;
    }
	...
}
```
### 使用动态代理之后，实现如下
- 只需要在一个地方添加统计代码
- 在一个地方修改，所有实现都修改了
- 上线的时候灵活移除
- 代码可重用性高（后续需要统计方法时间也能使用）
- 代码可读性高，不影响实现的代码

```java
public class StoreTimeDynamicProxy implements InvocationHandler {
    private Object subject;

    public StoreTimeDynamicProxy(Object subject) {
        this.subject = subject;
    }

    @Override
    public Object invoke(Object object, Method method, Object[] args)
            throws Throwable {
        long start = SystemClock.elapsedRealtime();
        Object result = method.invoke(subject, args);

        Logger.d("per", method.getName() + (args != null && args.length > 0 ? args[0] : "")
                + " cost " + (SystemClock.elapsedRealtime() - start));

        return result;
    }
}

private static final class ProxyStoreHolderInternal {
        private static final IStore INSTANCE = getInstance();

        private static IStore getInstance() {

            //    我们要代理哪个真实对象，就将该对象传进去，最后是通过该真实对象来调用其方法的
            InvocationHandler handler = new StoreTimeDynamicProxy(FileStoreHolderInternal.INSTANCE);

        /*
         * 通过Proxy的newProxyInstance方法来创建我们的代理对象，我们来看看其三个参数
         * 第一个参数 handler.getClass().getClassLoader() ，我们这里使用handler这个类的ClassLoader对象来加载我们的代理对象
         * 第二个参数realSubject.getClass().getInterfaces()，我们这里为代理对象提供的接口是真实对象所实行的接口，表示我要代理的是该真实对象，这样我就能调用这组接口中的方法了
         * 第三个参数handler， 我们这里将这个代理对象关联到了上方的 InvocationHandler 这个对象上
         */
            return (IStore) Proxy.newProxyInstance(handler.getClass().getClassLoader(),
                    FileStoreHolderInternal.INSTANCE
                            .getClass().getInterfaces(), handler);
        }
    }

```


