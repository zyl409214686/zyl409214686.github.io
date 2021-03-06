---
layout: post
title: android常用设计模式之代理模式
categories: 设计模式
description: android常用设计模式之代理模式
---

>定义：代理模式属结构型设计模式。为其他对象提供一种代理以控制对这个对象的访问。

## 代理模式结构图
![代理类结构图.jpg](http://upload-images.jianshu.io/upload_images/2229793-46ac91ca06fa1880.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在代理模式中有如下角色：
- ISubject: 抽象主题类，声明真实主题与代理的共同接口方法。
- RealSubject：真实主题类,代理类所代表的真实主题。客户端通过代理类间接地调用真实主题类的方法。
- Proxy：代理类，持有对真实主题类的引用，在其所实现的接口方法中调用真实主题类中相应的接口方法执行。

## 代码
```
public interface IShop {
    void buy();
}

public class BuyProxy implements IShop {

    private IShop mShop;

    public BuyProxy(IShop shop){
        mShop = shop;
    }

    @Override
    public void buy() {
        mShop.buy();
    }
}

public class Customer implements IShop {
    @Override
    public void buy() {
        System.out.print("顾客购物");
    }
}

public class BuyProxyTest {
    private Customer mCustomer;
    private BuyProxy mBuyProxy;
    @Before
    public void setUp() throws Exception {
        mCustomer = new Customer();
        mBuyProxy = new BuyProxy(mCustomer);
    }

    @Test
    public void buy() throws Exception {
        mBuyProxy.buy();
    }

}
```
## 优点
- 真是主题类就是实现实际的业务逻辑，不用关心其他的非本职工作。
- 任何主题类随时和嗯呢该发生变化，但是因为它实现了公共接口，所以代理类可以不做任何修改就能够使用。

**代码已上传**[github](https://github.com/zyl409214686/DesignPatterns)
