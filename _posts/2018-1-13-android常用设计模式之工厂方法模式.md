---
layout: post
title: android常用设计模式之工厂方法模式
categories: 设计模式
description: android常用设计模式之工厂方法模式
---

>定义：工厂方法模式属于创建型设计模式。定义一个用于创建对象的接口，让子类决定实例化哪个类。工厂方法使一个类的实例化延迟到其子类。

##### 工厂方法模式结构图：
![工厂方法模式结构图.jpg](http://upload-images.jianshu.io/upload_images/2229793-3adb7a0503cc7a25.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在工厂方法模式中有如下角色：
- Product: 抽象产品类
- ConcreteProduct: 具体产品类，实现Product接口。
- Factory: 抽象工厂类，该类返回一个Product类型的对象。
- ConcreteFactory: 具体工厂类，返回ConcreteProduct实例。

##### demo&代码

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
public abstract class AbstractLivingDetectionFactory {
    public abstract <T extends AbstractLivingDetection> T createLivingDetection(Class<T> t);
}
```

```
public class LivingDetectionFactory extends AbstractLivingDetectionFactory {

    @Override
    public <T extends AbstractLivingDetection>T createLivingDetection(Class<T> t){
        try {
            return  t.newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

#####使用场景

- 工厂类负责创建的对象比较少。
- 客户只需知道传入工厂类的参数，而无须关心创建对象的逻辑。
  优点：

##### 优点
- 使用户根据参数获得对应的类实例，避免了直接实例化类，降低了耦合性。
- 工厂方法模式不但包含简单工厂的优点，而且没有违背开放封闭原则。

**代码已上传**[github](https://github.com/zyl409214686/DesignPatterns)
