---
layout: post
author: 孙福生
title: Android沉浸式通知栏
background-color: '#673ab7'
tags: StatusBar
---

当Android 4.4 KitKat发布的时候，每个人都很兴奋看到新的半透明状态栏和导航栏，这确实提高了安卓系统的美感，
我也是被很多这样的应用吸引，试着实现这样沉浸式的效果，看下效果图更直观。

<img src="/assets/2015/android_systembartint_icon.png" style="width: 50%;">

完成这样的效果很简单，GitHub上已经实现对应的[库](https://github.com/jgilfelt/SystemBarTint)。

#### 首先，Studio下添加依赖或者引入相应的jar文件。

	compile 'com.readystatesoftware.systembartint:systembartint:1.0.3'

#### 其次，在Activity中加入如下代码：

```Java
	public void initSystemBarTint(boolean on, int res) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            setTranslucentStatus(on);
            SystemBarTintManager tintManager = new SystemBarTintManager(this);
            tintManager.setStatusBarTintEnabled(on);
            tintManager.setStatusBarTintResource(res);
        }
    }

    private void setTranslucentStatus(boolean on) {
        Window win = getWindow(); WindowManager.LayoutParams winParams = win.getAttributes();
        int bits = WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS;
        if (on) {
            winParams.flags |= bits;
        } else {
            winParams.flags &= ~bits;
        }
        win.setAttributes(winParams);
    }
```

#### 最后，在对应Activity的根布局中加入下面的属性即完成。

	android:fitsSystemWindows=”true” 

