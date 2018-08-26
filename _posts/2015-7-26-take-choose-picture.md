---
layout: post
author: 孙福生
title: 拍照和从相册选择图片
categories: Android
tags: Technology
---

好久没有写博客了，感到深深的自责。这是一个以分享为乐的时代，显然这段时间我对不起这个时代了，呵呵。
现在Android市场上app的数量越来越多了，这就要求我们在开发软件和版本迭代也要快（如外包公司和创业公司就需要快速拿出产品和简单的demo原型）。
现在大多数的软件在使用时需要用户注册账号设置个人信息，而设置用户的头像是必不可少的，我们的项目也用到了这个功能。

效果图如下：

<table>
    <tr>
        <td><img src="/assets/android_take_choose_picture.png" style="width: 80%;"></td>
        <td><img src="/assets/android_take_choose_picture1.png" style="width: 80%;"></td>
    </tr>
</table>


有图有真相，下面是真相：

###首先调系统的拍照和从相册选择图片的界面

    public static final int TAKE_PICTURE_REQUEST_CODE = 7;
    public static final int CHOOSE_PICTURE_REQUEST_CODE = 23;

    //拍照
    public static void gotoTakePicture(Activity activity, String takePicturePath) {
        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        Uri uri = Uri.fromFile(new File(Environment.getExternalStorageDirectory(), takePicturePath));
        intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);
        activity.startActivityForResult(intent, TAKE_PICTURE_REQUEST_CODE);
    }

    //从相册选择
    public static void gotoChoosePicture(Activity activity) {
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("image/*");
        activity.startActivityForResult(intent, CHOOSE_PICTURE_REQUEST_CODE);
    }

###在相应的界面下处理返回的数据，onActivityResult()方法中处理

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode == RESULT_OK) {
            switch (requestCode) {
                case NavigateManager.TAKE_PICTURE_REQUEST_CODE:
                    imagePath = Environment.getExternalStorageDirectory() + takePicturePath;
                    setImageViewWithPath(civUserAvatar, imagePath);
                    break;
                case NavigateManager.CHOOSE_PICTURE_REQUEST_CODE:
                    Uri uri = data.getData();
                    if (uri != null) {
                        String[] proj = {MediaStore.Images.Media.DATA};
                        Cursor cursor = getContentResolver().query(uri, proj, null, null, null);
                        if (cursor != null && cursor.moveToFirst()) {
                            imagePath = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA));
                            setImageViewWithPath(civUserAvatar, imagePath);
                            cursor.close();
                        }
                    }
                    break;
            }
        }
    }

###这就完了吗？显然还没有，图片显示也考验着软件的性能，原来我自己处理返回的图片并转换为Bitmap再缩放，可偶尔还是会出现OutOfMemoryError，这样还是有风险的。
想想为甚要重新造轮子，用现在开源的ImageLoader可以安全轻松搞定。

    private void setImageViewWithPath(ImageView imageView, String imagePath) {
        DisplayImageOptions imageOptions = new DisplayImageOptions.Builder().cacheInMemory(true).cacheOnDisc(true)
            .showImageOnLoading(R.drawable.ic_user)
            .showImageForEmptyUri(R.drawable.ic_user)
            .showImageOnFail(R.drawable.ic_user)
            .build();

        ImageLoader.getInstance().displayImage("file://" + imagePath, imageView, imageOptions);
    }



