---
layout: post
title: android常用设计模式之简单工厂模式
categories: 设计模式
description: android常用设计模式之简单工厂模式
---

>定义：简单工厂模式属于创建型模式，其又被称为工厂方法模式，这是由一个工厂对象决定创建出哪一种产品型的实例。

##### 简单工厂模式类图：
![简单工厂模式类图.jpg](http://upload-images.jianshu.io/upload_images/2229793-3a6274730a44bf88.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在简单工厂模式中有如下角色：
- Factory: 工厂类，这是简单工厂模式的核心，负责实现创建实例内部的逻辑。
- IProduct:抽象产品类，这是简单工厂模式所创建的所有对象的父类，它负责描述所有实例所工有的公共接口。
- Product: 具体产品类，这是简单工厂类的创建目标。

##### DEMO&代码
场景： 最近公司在接入了两款活体检测sdk，而且后续还会接入其他的活体检测sdk。代码如下：
```
public abstract class AbstractLivingDetection {
    /**
     * 开始检测
     */
  public abstract void startDetection();
}
```

```
public class HaiXinLivingDetection extends AbstractLivingDetection {
    @Override
    public void startDetection() {
        System.out.println("开启海鑫活体检测");
    }
}
```

```
public class TongFuDunLivingDetection extends AbstractLivingDetection {
    @Override
    public void startDetection() {
        System.out.println("开启通付盾活体检测");
    }
}
```

```
public class LivingDetectionFactory {
    public static AbstractLivingDetection createLivingDetection(String type){
        AbstractLivingDetection livingDetection = null;
        switch (type){
            case "tongfudun":
                livingDetection = new TongFuDunLivingDetection();
                break;
            case "haixin":
                livingDetection = new HaiXinLivingDetection();
                break;
            default:
                break;
        }
        return  livingDetection;
    }
}
```
##### 使用场景
- 工厂类负责创建的对象比较少。
- 客户只需知道传入工厂类的参数，而无须关心创建对象的逻辑。

##### 优点：
- 使用户根据参数获得对应的类实例，避免了直接实例化类，降低了耦合性。
##### 缺点：
- 可实例化的类型在编译期间已经确定。如果增加新类型，则需要修改工厂，这违背了开放封闭原则。简单工厂需要知道所有要生成的类型，其当子类过多或者子类层次过多时不适合使用。

**代码已上传**[github](https://github.com/zyl409214686/DesignPatterns)
