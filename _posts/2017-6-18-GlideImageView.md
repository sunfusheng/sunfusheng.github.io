---
layout: post
author: 孙福生
title:  基于Glide V4.7.1封装的图片加载库，可以监听加载图片时的进度
cover: 'http://ourv0v3ed.bkt.clouddn.com/GlideImageView1.gif'
tags: 个人项目
---

### 该库是基于[Glide](https://github.com/bumptech/glide) V4.7.1设计的，实现如下特性:<br/>
1、监听加载网络图片的进度<br/>
2、动态加载成有弧度的图片、圆形图片、高斯模糊图片更方便<br/>
3、链式设置触摸图片的透明度和非使能的透明度<br/>
4、增加九宫格控件，具体使用参考[NineImageViewActivity](https://github.com/sfsheng0322/GlideImageView/blob/master/Sample/src/main/java/com/sunfusheng/glideimageview/sample/NineImageViewActivity.java)<br/>

<br/>

### [APK下载地址](https://fir.im/GlideImageView)，去手机上体验吧 (◐‿◑)

<br/>

### 具体使用说明如下

#### Gradle:

    compile 'com.sunfusheng:GlideImageView:<latest-version>'
    
<br/>

#### GlideImageView 主要方法

    load(String url, @DrawableRes int placeholder, int radius, OnProgressListener onProgressListener)

    load(Object obj, @DrawableRes int placeholder, Transformation<Bitmap> transformation, OnProgressListener onProgressListener)

    loadCircle(String url, @DrawableRes int placeholder, OnProgressListener onProgressListener)
    
    loadDrawable(@DrawableRes int resId, @DrawableRes int placeholder)
      
<br/>

### [GitHub地址](https://github.com/sunfusheng/GlideImageView)






