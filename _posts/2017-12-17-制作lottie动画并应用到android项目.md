---
layout: post
title: android lottie动画制作运用
categories: 开源
description: 制作lottie动画并应用到android项目
---
## 效果图：
![启动页.gif](/images/blog/2017-12-17-制作lottie动画并应用到android项目/splash.gif)
最近在做一款音乐剪切的项目，启动页需要静态图太生硬了， 于是做了一个lottie动画效果。具体过程如下：

## AE动画制作
**工具准备**
1. 动画效果工具Adobe After Effects CC, 我这里用到的环境是Mac，下载地址以及破解请看这里 http://www.sdifen.com/mac-adobe-after-effecs-cc.html。 
2. 导出动画json文件，需要下载bodymovin AE插件 http://www.mq2014.com/bodymovin-ae-cha-jian-mac-win-an-zhuang-xia-zai.html。

**动画制作**
首先打开 AE工具创建一个合成，持续时间设置为 0;00;03;00 ，表示3秒
![创建一个合成](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot01.jpg)

导入图片素材操作如下：
![导入图片素材](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot02.jpg)

导入图片之后显示这样子
![导入成功](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot03.jpg)

接下来新建一个蒙版
![新建一个蒙版](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot04.jpg)


接下来点击小三角，形状显示出蒙版形状窗口
![按照如下操作点开蒙版形状窗口](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot05.jpg)

设置左侧、右侧初始值为0像素，然后点击蒙版路径左侧的小闹铃，接下来的操作将触发关键帧。
![设置初始值并开启关键帧](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot06.jpg)


滑动事件滑块到最右侧（3秒处），再次点开蒙版形状弹出框，设置右侧值500像素，然后勾选重置为矩形，然后确定。
![设置关键帧参数](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot07.jpg)

我们的动画完成啦，接下来就可以点击预览框播放按钮来看一下效果喽。
![预览操作](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot08.jpg)

动画制作完成了，不过还没结束哦， 接下来我们来导出json。点击工具栏创库哦、拓展、Bodymovin。
![点击bodymovin](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot09.jpg)


接下来选中要导出json的动画， 然后选择要导出的data.json的位置，然后点击Render。
![导出json](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot10.jpg)


如下显示就是导出成功啦
![导出成功](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot11.jpg)

导出如下文件
![导出文件.png](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot12.jpg)



## Android Lottie 加载动画

1. 先来将刚才生成的文件放入项目 Assets文件夹下。![放入assets](/images/blog/2017-12-17-制作lottie动画并应用到android项目/screenshot13.jpg)

2. build.gradle中增加依赖
  `compile 'com.airbnb.android:lottie:2.0.0-beta4'`

3. xml中增加LottieAnimationView，这里我们需要设置lottie用到的图片所在Assets下文件目录名称。也可以设置是否循环播放，自否自动播放，默认为false。这里因为我们的场景是启动页，所以不需要自动播放和循环播放。
```
<com.airbnb.lottie.LottieAnimationView
            android:id="@+id/lottie_imageview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:lottie_fileName="data.json"
            app:lottie_imageAssetsFolder="images/"
            android:layout_alignParentBottom="true"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="35dp"/>
            <!--app:lottie_loop="true"-->
            <!--app:lottie_autoPlay="true"-->
```
4.  在Activity 中设置事件
```
private void setListener() {
        ValueAnimator animator = new ValueAnimator().ofFloat(0f, 1f).setDuration(3000);
        animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                mBinding.lottieImageview.setProgress(Float.parseFloat
                        (animation.getAnimatedValue().toString()));
            }
        });
        animator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {
                Logger.d("onAnimationStart");
            }

            @Override
            public void onAnimationEnd(Animator animation) {
                Logger.d("onAnimationEnd");
                goToMainActivity();
            }

            @Override
            public void onAnimationCancel(Animator animation) {
                Logger.d("onAnimationCancel");
            }

            @Override
            public void onAnimationRepeat(Animator animation) {
                Logger.d("onAnimationRepeat");
            }
        });
        animator.start();
    }
```
这里我们通过`ValueAnimator`属性动画结合`LottieAnimationView.setProgress()`在3秒的时间完成我们的动画，到这里就结束了。能力有限，多提意见。

## AE动画以及项目代码 
- AE动画项目已经发布到[github](https://github.com/zyl409214686/AE_Animtions)<br>
- android lottie项目代码是[Mp3Cutter]((https://github.com/zyl409214686/Mp3Cutter))的启动页SplashActivity<br>

## Blog
如果对mp3剪切器项目感兴趣可以看这里有详细的介绍
[Mp3剪切器Blog](https://juejin.im/post/5a324f3f5188253da72e7956)



