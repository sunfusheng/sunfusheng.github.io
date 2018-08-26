---
layout: post
author: 孙福生
title: 自定义ImageView实现图片上传进度显示
categories: Android
tags: Technology
---

最近自己在做个app名叫Bingo，其中需要上传图片到服务器上，于是仿QQ传输图片的效果实现图片上传功能，效果如下面两张图片：  
<img src="/assets/upload_avatar.gif" style="width: 50%;margin: auto;">

<img src="/assets/upload_pic.gif" style="width: 50%;margin: auto;">

###初始化Paint画笔

    private Paint imagePaint;
    private Paint textPaint;

    protected void initPainters(Context context) {
        imagePaint = new Paint();
        imagePaint.setAntiAlias(true);
        imagePaint.setStyle(Paint.Style.FILL);

        textPaint = new TextPaint();
        textPaint.setColor(Color.parseColor("#FFFFFF"));
        textPaint.setTextSize(DisplayUtil.sp2px(context, 18));
        textPaint.setAntiAlias(true);
        setProgressStart();
    }

###处理上传进度和显示的文字

    private int progress = 0;

    //获得显示的文字
    private String getDrawText() {
        if (progress >= 0 && progress <= 100) {
            return progress+"%";
        }
        return null;
    }

    public void setProgressStart() {
        setProgress(-1);
    }

    public void setProgressFinish() {
        setProgress(101);
    }

    public void setProgress(int progress){
        this.progress=progress;
        postInvalidate(); //更新View绘制
    };

###在onDraw中画出图形

    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        if (progress < 0) {
            imagePaint.setColor(Color.parseColor("#70000000"));
            canvas.drawRect(0, 0, getWidth(), getHeight(), imagePaint);
        } else if (progress > 100) {
            imagePaint.setColor(Color.parseColor("#00000000"));
            canvas.drawRect(0, 0, getWidth(), getHeight(), imagePaint);
        } else {
            imagePaint.setColor(Color.parseColor("#70000000"));
            canvas.drawRect(0, 0, getWidth(), getHeight() - getHeight() * progress / 100, imagePaint);

            imagePaint.setColor(Color.parseColor("#00000000"));
            canvas.drawRect(0, getHeight() - getHeight() * progress / 100, getWidth(), getHeight(), imagePaint);

            String text = getDrawText();
            if (!TextUtils.isEmpty(text)) {
                float textHeight = textPaint.descent() + textPaint.ascent();
                canvas.drawText(text, (getWidth() - textPaint.measureText(text)) / 2.0f, (getWidth() - textHeight) / 2.0f, textPaint);
            }
        }
    }

###整理小的知识点

要绘图，首先得调整画笔，待画笔调整好之后，再将图像绘制到画布上，这样才可以显示在手机屏幕上。Android 中的画笔是 Paint类，Paint 中包含了很多方法对其属性进行设置， 上面用到的主要方法如下：

* setAntiAlias: 设置画笔的锯齿效果。 
* setColor: 设置画笔颜色 
* setTextSize: 设置字体尺寸。 
* setStyle:  设置画笔风格，空心或者实心。 
* setStrokeWidth: 设置空心的边框宽度。

TextPaint是Paint的子类，用它可以很方便的进行文字的绘制，一般情况下遇到绘制文字的需求时，我们一般用TextPaint所提供的方法。
 绘制text的高度的时候会用到textPaint.descent()和textPaint.ascent()，他们的意义如下：

![](/assets/capture_custom_view_textpaint1.png)
![](/assets/capture_custom_view_textpaint2.png)

Baseline是基线，在Android中，文字的绘制都是从Baseline处开始的，Baseline往上至字符“最高处”的距离我们称之为ascent（上坡度），Baseline往下至字符“最低处”的距离我们称之为descent（下坡度），leading（行间距）则表示上一行字符的descent到该行字符的ascent之间的距离。




