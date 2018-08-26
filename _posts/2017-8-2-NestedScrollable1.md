---
layout: post
author: 孙福生
title: 嵌套滑动之NestedListView
background-color: '#6d4c41'
tags: Android
---

为什么 CoordinatorLayout 支持 NestedScrollView 嵌套滑动不支持 ScrollView；  
为什么 CoordinatorLayout 支持 RecyclerView 嵌套滑动不支持 ListView；  
这一篇博文我们要知道 CoordinatorLayout 为什么不支持，还要让它支持 ListView 的嵌套滑动。  

熟悉 Material Design 设计风格的同学肯定对 CoordinatorLayout 不陌生，CoordinatorLayout 常常和 AppBarLayout、CollapsingToolbarLayout 一起组合使用，并和 RecyclerView 或 NestedScrollView 一起联动使用，各种组合的效果很是炫酷，如果你再自定义个 layout_behavior 更是屌的不要不要的。

但是为什么呢？  

Android 5.0 Lollipop(棒棒糖) 发布以后，增加了这节我们关注的两个接口和两个帮助类

    NestedScrollingParent  
    NestedScrollingChild  
    NestedScrollingParentHelper  
    NestedScrollingChildHelper  

CoordinatorLayout 之所以可以和 RecyclerView、NestedScrollView 联动，是因为它实现了 NestedScrollingParent 接口，看一下它的继承关系

    public class CoordinatorLayout extends ViewGroup implements NestedScrollingParent {
        ...
    }

如果子类可以实现 NestedScrollingChild，这样通过 NestedScrollingChildHelper 可以通知父类接收滑动事件继而实现联动，我们再看一下 RecyclerView 和 NestedScrollView 的继承关系

    public class RecyclerView extends ViewGroup implements ScrollingView, NestedScrollingChild {
        ...
    }

    public class NestedScrollView extends FrameLayout implements NestedScrollingParent, NestedScrollingChild, ScrollingView {
        ...
    }

看到这我们就知道了，为什么 CoordinatorLayout 不支持和 ListView、ScrollView 嵌套联动，因为这俩小子没开这个卦呀

    public class ListView extends AbsListView {
        ...
    }

    public class ScrollView extends FrameLayout {
        ...
    }

### NestedListView 源码如下

<img src="/assets/2017/NestedListView_code1.png"/>  
<img src="/assets/2017/NestedListView_code2.png"/>

改完之后我们看下效果，和包裹 RecyclerView 的效果一样

<img src="/assets/2017/NestedScrollable_gif1.gif" style="width: 50%;"/>

### [GitHub地址](https://github.com/sunfusheng/NestedScrollableDemo)






