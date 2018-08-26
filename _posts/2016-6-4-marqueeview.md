---
layout: post
author: 孙福生
title: 自定义View之垂直翻页公告
background-color: '#C34A42'
tags: 个人项目
---

俗名：垂直跑马灯  
学名：垂直翻页公告

### 动态效果图：

<img src="/assets/2016/MarqueeView_Gif.gif" style="width: 50%;"/>

### [GitHub开源地址](https://github.com/sfsheng0322/MarqueeView)

### [APK下载地址](http://fir.im/MarqueeView)

### 使用

#### Gradle:

    compile 'com.sunfusheng:marqueeview:<latest-version>'

#### 属性

| Attribute 属性          | Description 描述 |
|:---                    |:---|
| mvAnimDuration         | 一行文字动画执行时间           |
| mvInterval         | 两行文字翻页时间间隔           |
| mvTextSize         | 文字大小           |
| mvTextColor         | 文字颜色          |
| mvGravity         | 文字位置:left、center、right          |
| mvSingleLine         | 单行设置          |

#### XML

    <com.sunfusheng.marqueeview.MarqueeView
        android:id="@+id/marqueeView"
        android:layout_width="match_parent"
        android:layout_height="30dp"
        app:mvAnimDuration="1000"
        app:mvInterval="3000"
        app:mvTextColor="@color/white"
        app:mvTextSize="14sp"
        app:mvSingleLine="true"/>

#### 设置列表数据

    MarqueeView marqueeView = (MarqueeView) findViewById(R.id.marqueeView);

    List<String> info = new ArrayList<>();
    info.add("1. 大家好，我是孙福生。");
    info.add("2. 欢迎大家关注我哦！");
    info.add("3. GitHub帐号：sfsheng0322");
    info.add("4. 新浪微博：孙福生微博");
    info.add("5. 个人博客：sunfusheng.com");
    info.add("6. 微信公众号：孙福生");
    marqueeView.startWithList(info);

#### 设置字符串数据

    String notice = "心中有阳光，脚底有力量！心中有阳光，脚底有力量！心中有阳光，脚底有力量！";
    marqueeView.startWithText(notice);

#### 设置事件监听

    marqueeView.setOnItemClickListener(new MarqueeView.OnItemClickListener() {
        @Override
        public void onItemClick(int position, TextView textView) {
            Toast.makeText(getApplicationContext(), String.valueOf(marqueeView1.getPosition()) + ". " + textView.getText(), Toast.LENGTH_SHORT).show();
        }
    });

源码很简单，如果想看源码可以访问[GitHub](https://github.com/sunfusheng/MarqueeView)

具体实现思路参考[diygreen简书文章](http://www.jianshu.com/p/c5ef2803c823)

第一次尝试将开源库上传到jcenter上，具体操作参考鸿洋的[文章](http://blog.csdn.net/lmj623565791/article/details/51148825#rd)



