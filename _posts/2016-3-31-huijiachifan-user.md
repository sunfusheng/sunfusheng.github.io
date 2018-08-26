---
layout: post
author: 孙福生
title: 回家吃饭Android客户端整理后总结
background-color: '#ff9e80'
tags: Technology
---

来到<a href="http://www.jiashuangkuaizi.com/"  target="_black">回家吃饭</a>五个月了！客户端从Eclipse环境上迁移到Studio上变化比较大，
下面具体从以下几方面总结下回家吃饭Android客户端的变化。

<a href="http://www.jiashuangkuaizi.com/"  target="_black">
    <img src="/assets/2016/huijiachifan_logo.png" style="width: 30%;"/>
</a>


### 1、使用动态代理AOP编程框架简洁、高效开发

该动态代理框架的核心是通过dexmaker和Spring的拦截器实现AOP编程；dexmaker是运行在Android DVM上，利用Java编写，来动态生成DEX字节码的API。如果了解Spring AOP编程的话，应该听说过cglib or ASM，但这两个工具生成都是Java字节码，而DVM加载的必须是DEX字节码。所以，想要在Android上进行AOP编程，Google 的dexmaker可以说是一个非常好的选择。

辅助的还有注解和反射，使用注解来标注同步、异步、加载框和加载显示的文字；反射回调继承以下Base类子类的方法：

```Java
    BaseAsyncActivity
    BaseAsyncFragment
    BaseAsyncListAdapter
    BaseAsyncObject
```

同时着重使用系统的Handler并封装为MessageProxy进行消息的分发与处理。

封装映射Map为ModelMap，方便回调时的数据传递。

因为公司项目是不外泄的，具体可以参考：  
<a href="http://sunfusheng.github.io/%E9%A1%B9%E7%9B%AE/2015/09/09/bingo.html" target="_black">个人作品《BingoWorld》</a>  
<a href="http://fir.im/Bingo" target="_black">下载地址</a>  
<a href="https://github.com/sunfusheng/Bingo" target="_black">开源地址</a>  

### 2、使用OkHttp网络库提升安全及稳定性

客户端加入动态代理AOP编程框架后，又开始更换网络库，去掉xUtils和Volley，使用OkHttp。着重封装为四种请求加载方式：

```Java
    OkHttpGet
    OkHttpPost
    OkHttpDownload
    OkHttpUpload
```

OkHttp是Google推崇的，使用它不会担心适配的问题和稳定性，实际上现在封装的网络库确实稳定。

同时，通过Java的SSL对称加密和OkHttp结合，保护我们的数据安全不被拦截恶意篡改。

### 3、AS 下使用Gradle快速构建

客户端从Eclipse环境上迁移到Studio上最大的益处就是使用Gradle快速构建，通过maven和apt可以快速添加、使用和删除第三方库。

在Gradle下使用统一的签名方式，独立配置签名信息，在工程中不管debug还是release版都使用正式签名，包括地图的、推送、支付、分享和友盟统计等。

多渠道打包，定义不同的product flavor, 并把AndroiManifest中的channel渠道编号替换为对应的flavor标识。

自定义Build Type，可以使用默认的两种debug测试版和release正式版，还可以加入Grey测试版。

Gradle下，压缩资源、zip优化、混淆代码等控制方便，可以使客户端包更小，不容易反编译。

```Java
    shrinkResources：压缩资源
    zipAlignEnabled：zip优化
    minifyEnabled：混淆代码
```

### 4、抽象、封装加重构多管齐下
以上三点的改变还是基础性的调整，在稳固的基础上我们要提高的就是代码清晰的层次和健壮性；回家吃饭客户端对多个界面、逻辑和功能进行封装后抽象出来，方便以后维护。

#### 重构的界面有：

```Java
    CodeLoginActivity：验证码登录
    RiskControlActivity：风控验证
    KitchenDetailActivity：厨房详情
    MenuDetailActivity：菜品详情
    SearchActivity：搜索页面
    TakeWordActivity：捎句话
    CouponActivity：优惠券
```

#### 抽象涉及到的：

```Java
    DetailLoveDoubleDishView：爱心双拼菜品
    DetailLittleTableView：小饭桌菜品
    DetailMenuDishView：菜品详情
    DetailRecommendDishView：推荐菜
    DetailCommonDishView：普通菜
    DetailCommentView：评论
```

#### 自定义View封装涉及到的：

```Java
    ChoiceView：菜品选择View
    ShareView：分享View
    ShoppingView：购物车
    TicketView：家厨饭票
    UploadImageView：上传图片View
```

#### 还有各种Manager管理类和Util工具类

```Java
    KitchenCartManager ：购物车管理类
    LocationManager：定位管理类
    ShareManager：分享管理类
    NavigateManager：路由跳转管理类
    DateUtil：时间工具类
    FastJsonUtil：数据解析工具类
    KeyBoardUtil：软键盘显示与隐藏工具类
    SpecialViewUtil：文字颜色多样化显示工具类
```

### 5、使用别人的好“轮子”

现在的开发都在追求高效和快速，所以一个高效快速的开发离不开别人稳定的第三方库，我们也不可能从零开始，先看看我们的这些“轮子”：

```Java
    Butterknife：黄油刀，编译时期的依赖注入，省去大量的findViewById重复操作
    Logger：log打印利器，打印清晰，可以打印出对应的log行数
    Esperandro：SharedPreferences key-value存取利器
    OkHttp：Google推崇的网络库
    Fastjson：阿里的数据解析库，用于网络请求后的数据解析
    Universal Image Loader：使用最普遍的图片加载库
    MultiDex：解决App中方法数超过65535个的分包库
    Shape Image View：稳定不会引起内存泄漏的原型图片库，主要用于头像的显示
    EventBus：线程间、页面间数据传递解耦利器
```

### 6、性能优化

产品 = 功能 + 性能，回家吃饭客户端已经在下面几个方面进行了性能优化：

```Java
    LeakCanary：内存泄露目前在项目中一般用leakcanary基本就能搞定，配置起来也相当简单
    ViewStub：View的延迟加载
    过度绘制：使用RelativeLayout减少视图树的层级，防止过度绘制
    onTrimMemory回调：应用响应此回调释放非必须内存，如图片的内存占用
    lint检查：通过Android Studio中的 Analyze->Inspect Code 对工程代码做静态扫描；找出潜在的问题代码并修改，去掉无用的图片
    代码优化：去掉大量的临时变量和部分全局变量，优化for循环，建议使用增强for循环，在适当的生命周期中创建释放对象
```

### 7、没变化完的继续变化＋实践新技术

上面大概的总结回家吃饭Android客户端的变化，但是还会继续变化，接下来的任务有：

```Java
    重构还没有完，会继续重构其他页面，应用AOP编程框架
    升级OkHttp2.6到OkHttp3.2，异步数据解析更加方便
    使用Glide图片库和Realm数据库
    注意lint检查，每次发版前通过lint检查找出潜在的问题代码并修改
    修改工程的命名，使整个工程更加规范化
    继续调研插件化和热更新，同时使整个工程模块化
    尝试使用新技术到生产环境中，如RxAndroid、Retrofit、Drager2等
```



    