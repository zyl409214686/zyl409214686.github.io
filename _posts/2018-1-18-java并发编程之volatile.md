---
layout: post
title: java并发编程之volatile
categories: java并发编程
description: java并发编程之volatile
---

>Java语言规范第三版中对volatile的定义如下：Java编程语言允许线程访问共享变量，为了确保共享变量能被准确和一致地更新，线程应该确保通过排他锁单独获得这个变量。

了解volatile关键字之前需要先了解下Java内存模型，java内存模型抽象示意图如下：

## Java内存模型


![java内存模型抽象示意图](https://user-gold-cdn.xitu.io/2018/1/15/160f58a31ab8d1c1?w=326&h=433&f=png&s=28008)
线程A和线程B之间若要通信的话， 必须经历下面两个步骤
（1）线程A和线程A本地内存中更新过的共享变量刷新到主存中去。
（2）线程B到主存中去读取线程A之前更新过的共享变量。

由此可见执行下面的语句：

`int a = 100`
线程必须现在自己的工作线程中对变量i所在的缓存进行赋值操作，然后再写入主存当中，而不是直接将数值100写入主存中。

## 特性
1. 可见性
  当一个共享变量被volatile修饰时，它会保证修改的值立即被更新到主存，所以对其他线程是可见的。当其他线程需要读取该值时，其他线程会去主存中读取新值。相反普通的共享变量不能保证可见性，因为普通共享变量被修改后并不会立即被写入主存，何时被写入主存也不确定。当其他线程去读取该值时，此时主存可能还是原来的旧值，这样就无法保证可见性。

2. 有序性
  java内存模型中允许编译器和处理器对指令进行重排序，虽然重排序过程不会影响到`单线程`执行的正确性，但是会影响到多线程并发执行的正确性。这时可以通过volatile来保证有序性，除了volatile,也可以通过synchronized和Lock来保证有序性。synchronized和Lock保证每个时刻只有一个线程执行同步代码，这相当于让线程顺序执行同步代码，从而保证了有序性。如果不考虑原子性操作的话volatile比synchronized和Lock更轻量级，成本更低。

3. 不保障原子性
  volatile关键字只能保证共享变量的可见性和有序性。如果volatile修饰并发线程中共享变量， 而该共享变量是非原子操作的话，并发中就会出现问题。比如下面代码：

```
public class HelloVolatile{
    public volatile int mNumber = 0;
    public static void main(String []args){
        final HelloVolatile hello = new HelloVolatile();
        for(int i =0; i<10; i++){
            new Thread(){
                public void run(){
                    for(int j =0; j<1000; j++){
                        hello.mNumber ++;
                    }
                }
            }.start();
        }
        while (Thread.activeCount()>2){
            Thread.yield();
        }
        System.out.println("number:"+hello.mNumber);
    }
}
```
这段代码预期结果是10000，可是每次执行结果都有可能不一样。这是因为自增或自减都是非原子操作。

（1） 假如mNumber此时等于100，线程1进行自增操作。

（2）线程1先读取了mNumber的值100，然后它被堵塞了。

（3）这时候线程2读取mNumber的值100，然后进行了自增操作，并写入到主存中， 这时候主存中的值为101。

（4）这时候线程1继续执行，因为此前线程1已经读取到值100，然后进行自增操作101，然后将101写入到主存中。

可以看到两个线程分别对100进行了+1操作，预期主存中的nNumber = 102，实际mNumebr = 101;  这就是因为非原子操作造成的。

## 使用场景
(1)并发编程中不依赖于程序中任意其状态的状态标识。可以通过关键字volatile代替synchronized, 提高程序执行效率，并简化代码。

(2)单例模式的双重检查模式DCL

```
public class DclSingleton {
    private volatile static DclSingleton mInstance = null;
    public static DclSingleton getInstance(){
        if(mInstance==null){
            synchronized (DclSingleton.class){
                if(mInstance==null){
                    mInstance = new DclSingleton();
                }
            }
        }
        return mInstance;
    }
}
```

## 原理浅析
将volatile修饰的变量转变成汇编代码，如下：

`... lock addl $0x0,(%rsp)`

通过查IA-32架构安全手册可知，Lock前缀指令在多核处理器会引发两件事。

1）将当前处理器缓存行的数据写回到系统内存。

2）这个写回内存的操作会使在其他CPU里缓存了该内存地址的数据无效。

---
解读 ：

为了提高，处理器不直接和内存进行通信，而是先将系统内存的数据读到内部缓存后再进行操作，但操作完不知道何时再写回内存。如果对声明了volatile的变量进行写操作，JVM会向处理机发送一条Lock前缀指令，将这个变量所在的缓存行的数据写回到系统内存。

但是写会内存后，如果其他处理器缓存的值还是旧的，再执行计算操作就会出现问题。所以在多处理器下，为了保证各个处理器缓存是一致的，就会实现缓存一致性协议，如下图：

![](https://user-gold-cdn.xitu.io/2018/1/15/160f7f31a84be510?w=618&h=372&f=jpeg&s=53962)

每个处理器通过嗅探在总线上传播的数据来检查自己缓存的数据是否过期了，当处理器发现自己的缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置成无效状态。当处理器对这个数据进行操作的时候，就会重新从系统内存中把数据读到处理器缓存中。