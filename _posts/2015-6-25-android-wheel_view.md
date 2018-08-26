---
layout: post
author: 孙福生
title: 自定义Android时间选择器
categories: Android
tags: Technology
---

在Android项目开发中我们经常会遇到设置时间的操作，根据要求设置我们需要的年、月、日、时、分、秒等信息，
下面这个自定的WheelView同样是因为我们项目中的需要，经过修改后使用。项目中的屏幕截图如图一：

<table>
    <tr>
        <td><img src="/assets/android_wheelview_icon.png" style="width: 80%;"></td>
        <td><img src="/assets/android_shareview_icon.png" style="width: 80%;"></td>
    </tr>
</table>

这个View和它后面的透明遮挡是attach到相应的activity上去的，这样的好处不言而喻
(我们分享的UI也是这样处理的如图二，原来会想到用PopupWindow，不过这样会麻烦些)。
touch到它的透明遮挡或click取消按钮都会使
这个View隐藏。当然它的显示和隐藏并不会反人类的生硬的显示与隐藏，是有相应的渐入渐出的动画效果。

主要代码如下：

#### 将View添加到Activity的界面里

	public void attachView(Activity activity) {
	    ((ViewGroup) activity.getWindow().getDecorView()).addView(fullMaskView);
	    FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(FrameLayout.LayoutParams.MATCH_PARENT, FrameLayout.LayoutParams.WRAP_CONTENT);
	    params.gravity = Gravity.BOTTOM;
	    ((ViewGroup) activity.getWindow().getDecorView()).addView(contentLayout, params);
	}

#### 动态显示与隐藏该View

	public void show() {
	    fullMaskView.setVisibility(View.VISIBLE);
	    contentLayout.setVisibility(View.VISIBLE);
	    YoYo.with(Techniques.SlideInUp).duration(200).playOn(contentLayout);
	}

	public void hide() {
	    fullMaskView.setVisibility(View.GONE);
	    YoYo.with(Techniques.SlideOutDown).duration(200).withListener(new SlideOutDownAnimatorListener(contentLayout)).playOn(contentLayout);
	}

#### 初始化WhellView

为这个时间选择器设置数据，不可循环滚动，设置显示5个item和显示item的高度和字体的大小，并为这个View设置监听器。

	private void initWhellView(WheelView wheelView, WheelAdapter adapter, OnWheelChangedListener listener, int currentIndex) {
	    wheelView.setAdapter(adapter);
	    wheelView.setCyclic(false);
	    wheelView.setVisibleItems(5);
	    wheelView.setCurrentItem(currentIndex);
	    wheelView.TEXT_SIZE = 14;
	    wheelView.ADDITIONAL_ITEM_HEIGHT = 24;
	    wheelView.addChangingListener(listener);
	}





