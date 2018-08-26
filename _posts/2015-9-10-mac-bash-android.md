---
layout: post
author: 孙福生
title: Android手机录视频转Gif格式
categories: 工具
tags: Util Mac
---

这篇文章主要为开发人员解决Android手机录视频转Gif格式困难痛点，下面是我在Mac下的操作步骤：  
1、Mac系统下通过bash连接Android手机。  
2、通过adb shell命令获得录制的视频。  
3、将视频转化为Gif文件。  
4、补充相关adb操作命令。  

### 先看下我录制的个人作品Gif效果图

<img src="/assets/gifs/图灵机器人.gif" style="width: 50%;">

源码参考个人作品 [【图灵机器人】](https://github.com/sfsheng0322/Tuling)

Demo[下载地址](http://fir.im/turing)

### 1、Mac系统下通过bash连接Android手机

如果在Mac bash下你连接不了手机，请参考下面的操作。

1、Mac系统通过数据线连接Android手机

2、找到Android手机的vendor ID：

	$ system_profiler SPUSBDataType

在列出的usb设备中找到自己的手机，copy下vendor ID

	MI 5:  
	Product ID: 0xff78  
	Vendor ID: 0x2017  
	Version: 3.18  
	Serial Number: 14886d4e  
	Speed: Up to 480 Mb/sec  
	Manufacturer: Xiaomi  
	Location ID: 0x14188000 / 11  
	Current Available (mA): 500  
	Current Required (mA): 500  

将vandor ID放到 ~/.android/adb_usb.ini 配置文件中： 

	$ vi  ~/.android/adb_usb.ini

3、如果没有设置adb环境变量，设置一下： 

	$ vi ~/.bash_profile  
	在文件最后加上(path因电脑而异)：   
	$ export PATH=/Users/sunfusheng/Android/Studio/sdk/platform-tools/:$PATH  
	$ wq   
	保存退出后刷新一下，终端执行CMD：   
	$ source ~/.bash_profile

4、测试是否连接上Android手机： 

	$ adb devices 

### 2、通过adb shell命令获得录制的视频

#### 录制命令

	$ adb shell screenrecord /sdcard/test.mp4

视频保存目录可以自己指定，如上面的 /sdcard/test.mp4，命令执行后会一直录制180s，按下 ctrl+c 可以提前结束录制。

#### 设定视频分辨率

	$ adb shell screenrecord --size 848*480 /sdcard/test.mp4

对于高分辨率的手机，录制的视频很大，我们分享又不需要这么大的我们可以设置录制的视频分辨率。

#### 设定视频比特率

	$ adb shell screenrecord --bit-rate 2000000 /sdcard/test.mp4

默认比特率是4M/s，为了分享方便，我们可以调低比特率为2M

#### 拉取视频文件

	$ adb pull /sdcard/test.mp4

⚠注意：使用adb pull 即可把手机SD卡中视频拉取到本地，如果拉下来的视频文件不能正常转换，可以手动将手机里面的视频发送到电脑上。

### 3、将视频转化为Gif文件

一、Mac上通过Gifrecord软件将视频转化为Gif文件。

二、Windows上通过Free Video to GIF Converter将视频转换成GIF，具体操作参考[【Video 转 Gif 实战】](http://www.jianshu.com/p/775f5e742ce8)，转换时还可以删除不需要的帧，这点真得很不错。

### 4、补充相关adb操作命令

	$ adb dervices #显示当前启动的仿真器装置序号
	$ adb help #显示adb指令用法
	$ adb verson #显示adb版本
	$ adb install #安装APK应用程序组件
	$ adb push #上传文件或目录（adb push 文件所在PC的位置即文件名 目的位置）
	$ adb pull #下载文件或目录（adb pull 文件所在手机的位置即文件名 目的位置）
	$ adb shell #进入Android系统命令行模式
	$ adb logcat #监控仿真器运行记录
	$ adb bugreport #生成adb出错报告
	$ adb start－server #启动adb服务器
	$ adb kill－server #关闭adb服务器
	$ adb get－state #取得adb服务器运行状态
	$ adb get－serialno #获得仿真器运行序号


