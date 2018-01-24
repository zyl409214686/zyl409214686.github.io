---
layout: post
title: android常用设计模式之单例模式
categories: 设计模式
description: android常用设计模式之单例模式
---

>定义：单例模式是一种对象创建模式，用于产生一个对象的具体实例，他可以确保系统中一个类只产生一个实例。

##### 单例模式类图：
![单例类图.jpg](http://upload-images.jianshu.io/upload_images/2229793-e7b38dc2c9f93138.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
类图解释：其中包含两个角色，单例类(Singleton)和使用者（SingletionMain）
该类图解释为：单例类为一个自身关联，使用者对单例类为一个单向关联（引用）

##### 优点：
- 对于频繁使用的对象，可以省略穿件对象所花费的时间，这对于那些重量级对象而言，是非常可观的一笔系统开销。
- 由于new操作的次数减少，因而对系统内存的使用频率也会降低，这将减轻GC压力，缩短GC停顿时间。

##### 实现方式：
 使用**静态内部类**的方式实现单例，既可以做到延迟加载，也不必要使用同步关键字，是一种比较完善的实现。

```
public class StaticSingleton {
    private StaticSingleton (){}

    private static class SingletonHolder{
        private static Singleton instance = new Singleton();
    }

    public static StaticSingleton getInstance(){
        return SingletonHolder.instance;
    }
}
```
#####原理：
在这个实现中，单例模式使用内部类来维护单例的实力，当StaticSingleton被加载时，其内部类并不会被初始化，故可以确保当StaticSingleton类被JVM加载时，不会初始化单例类，而当getInstance()方法被调用时，才会加载SingletonHolder,从而初始化instance。同时，由于实例的建立是在类加载时完成，故天生对多线程友好，getInstance()方法也不需要使用同步关键字。因此，这种实现方式同时兼备以上两种实现优点。

##### github
https://github.com/zyl409214686/DesignPatterns
