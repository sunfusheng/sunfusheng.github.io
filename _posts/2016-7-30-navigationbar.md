---
layout: post
author: 孙福生
title: 自定义 ShareView 之 NavigationBar 适配
background-color: '#607d8b'
tags: Android
---

分享功能在实际项目开发中经常用到的，有可能是在屏幕中间弹出的 Dialog 对话框，也有可能是从屏幕下方滑上来的视图。本文是自定义从屏幕下方滑上来的分享控件，将 ShareView add 到 DecorView 上，但是在带 NavigationBar 导航条的手机上出现适配问题，下面是我碰到的问题及解决方法。

<img src="/assets/2016/ok_share_view.png" style="width: 30%;"/>

这个功能如何实现的呢？贴下主要代码（[具体参考GitHub上项目](https://github.com/sunfusheng/In-depthStudy)）。

    public class ShareView {

        private Activity mActivity;
        private View fullMaskView; // 蒙层视图
        private View contentLayout; // 分享视图
        private int panelHeight = 0; // 分享视图高度

        public ShareView(Activity activity) {
            this.mActivity = activity;
            initShareView(activity);
        }

        private void initShareView(Activity activity) {
            fullMaskView = View.inflate(activity, R.layout.ui_view_full_mask_layout, null);
            contentLayout = LayoutInflater.from(activity).inflate(R.layout.ui_share, null);
            ButterKnife.bind(this, contentLayout);

            attachView();
            initListener();
        }

        // 将该View添加到根布局
        private void attachView() {
            ((ViewGroup) mActivity.getWindow().getDecorView()).addView(fullMaskView);
            FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(FrameLayout.LayoutParams.MATCH_PARENT, FrameLayout.LayoutParams.WRAP_CONTENT);
            params.gravity = Gravity.BOTTOM;
            ((ViewGroup) mActivity.getWindow().getDecorView()).addView(contentLayout, params);
            fullMaskView.setVisibility(View.GONE);
            contentLayout.setVisibility(View.GONE);
        }

        // 动画显示布局
        public void show() {
            fullMaskView.setVisibility(View.VISIBLE);
            contentLayout.setVisibility(View.VISIBLE);
            contentLayout.getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
                @Override
                public void onGlobalLayout() {
                    contentLayout.getViewTreeObserver().removeGlobalOnLayoutListener(this);
                    ViewGroup parent = (ViewGroup) contentLayout.getParent();
                    panelHeight = parent.getHeight() - contentLayout.getTop();
                    ObjectAnimator.ofFloat(contentLayout, "translationY", panelHeight, 0).setDuration(200).start();
                }
            });
        }

        // 动画隐藏布局
        public void hide() {
            fullMaskView.setVisibility(View.GONE);
            ObjectAnimator.ofFloat(contentLayout, "translationY", 0, panelHeight).setDuration(200).start();
        }
}

你觉得有问题吗？我觉得没问题，因为在项目中使用了几个版本了，还经过测试的，肯定没问题，但是看下面的效果图

<img src="/assets/2016/problem_share_view.png" style="width: 30%;"/>

不仔细看也没有问题啊，再看下，是不是“取消”按钮没啦，是不是屏幕下面多出一个 NavigationBar 的条目呢。

so, that`s the problem !

上面这张图是在华为 Nexus 手机上截的图，这么一来这个自定义 ShareView 在所有屏幕底下带有 NavigationBar 的手机上
都会有问题，经过测试，确实是这样的，虽然对于整体功能没有太大影响，但是仍不可以忍受。

我们使用的大多数 Android 手机上的Home键，返回键以及menu键都是实体触摸感应按键，但是，一些手机生厂商包括 Google 在内它们并没有实体按键或触摸感应按键，取而代之的是在屏幕的下方加上 NavigationBar 导航条，我是很吐糟这种设计的，虽然这使得手机外观的设计更加简约。

找到原因就是找到解决办法了，先获取手机是否有导航条，有则在显示和隐藏动画的时候多加上导航条的高度

    private int navigationBarHeight = 0; // 导航栏高度

    // 将该View添加到根布局
    private void attachView() {
        // ...
        if (hasNavigationBar(mActivity)) {
            navigationBarHeight = getNavigationBarHeight(mActivity);
        }
    }

    // 动画显示布局
    public void show() {
        // ...
        ObjectAnimator.ofFloat(contentLayout, "translationY", panelHeight, -navigationBarHeight).setDuration(200).start();
    }

    // 动画隐藏布局
    public void hide() {
        // ...
        ObjectAnimator.ofFloat(contentLayout, "translationY", -navigationBarHeight, panelHeight).setDuration(200).start();
    }

    // 判断设备是否有返回键、菜单键来确定是否有 NavigationBar
    public static boolean hasNavigationBar(Context context) {
        boolean hasMenuKey = ViewConfiguration.get(context).hasPermanentMenuKey();
        boolean hasBackKey = KeyCharacterMap.deviceHasKey(KeyEvent.KEYCODE_BACK);
        if (!hasMenuKey && !hasBackKey) {
            return true;
        }
        return false;
    }

    // 获取 NavigationBar 的高度
    public static int getNavigationBarHeight(Activity activity) {
        Resources resources = activity.getResources();
        int resourceId = resources.getIdentifier("navigation_bar_height", "dimen", "android");
        return resources.getDimensionPixelSize(resourceId);
    }

这样就OK了，效果图如下

<img src="/assets/2016/normal_share_view.png" style="width: 30%;"/>

[具体参考GitHub上项目](https://github.com/sunfusheng/In-depthStudy) 

解决办法参考：[http://blog.csdn.net/lnb333666/article/details/41821149](http://blog.csdn.net/lnb333666/article/details/41821149)

