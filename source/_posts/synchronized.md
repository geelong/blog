---
title: synchronized
date: 2016-09-13 15:09:01
tags:

- synchronized
---

### synchronized 方法锁住的是this对象，下面2段代码效果一样

<!--more-->

```java
public synchronized void test() {  
        System.out.println("test开始..");  
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("test结束..");  
    }  
    
    public void test() {  
    	synchronized(this){
        System.out.println("test开始..");  
	        try {  
	            Thread.sleep(1000);  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	        System.out.println("test结束..");  
        }
    }  
```



### static synchronized 方法锁住的是class对象，下面2段代码效果一样


```java
public static synchronized void test() {  
        System.out.println("test开始..");  
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("test结束..");  
    }  
    
    public void test() {  
    	synchronized(Test.class){
        System.out.println("test开始..");  
	        try {  
	            Thread.sleep(1000);  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	        System.out.println("test结束..");  
        }
    }  
```



