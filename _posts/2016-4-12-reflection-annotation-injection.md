---
layout: post
author: 孙福生
title: 反射、注解与依赖注入总结
categories: Java
tags: Technology
---

上一篇[【线程、多线程与线程池总结】](http://www.jianshu.com/p/b8197dd2934c)中主要记录线程、多线程相关概念，侧重于线程的Future使用与线程池的操作；同样这一篇【反射、注解与依赖注入总结】依然着重于相关概念与使用。<br/>  
现在在我们构建自己或公司的项目中，或多或少都会依赖几个流行比较屌的第三方库，比如：[Butter Knife](https://github.com/JakeWharton/butterknife)、[Retrofit](https://github.com/square/retrofit)、[Dagger 2](https://github.com/google/dagger)等，如果你没用过，那你需要找时间补一下啦；有时在使用后我们会好奇他们到底是怎么做到这种简洁、高效、松耦合等诸多优点的，当然这里我不探讨它们具体怎么实现的，而关心的是它们都用到同样的技术反射和注解，并实现的依赖注入。

如果你好奇这些库具体是怎么实现的，或者想了解他们实现的原理，这里向你推荐几篇文章：  
1、[android注解Butterknife的使用及代码分析](http://www.jianshu.com/p/6f7a04488462)  
2、[Retrofit源码1: 为什么写一个interface就可以实现http请求](http://xuzhengchao.com/java/retrofit-source-code.html)  
3、[Retrofit分析-漂亮的解耦套路](http://www.jianshu.com/p/45cb536be2f4)    
4、[Dagger 源码解析](http://a.codekk.com/detail/Android/%E6%89%94%E7%89%A9%E7%BA%BF/Dagger%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90)  
5、[Android：dagger2让你爱不释手－基础依赖注入框架篇](http://www.jianshu.com/p/cd2c1c9f68d4)  
6、[Android：dagger2让你爱不释手－重点概念讲解、融合篇](http://www.jianshu.com/p/1d42d2e6f4a5)  
7、[Android：dagger2让你爱不释手－终结篇](http://www.jianshu.com/p/65737ac39c44)  


这些好文章已经帮你收藏了，下面直接进入我的主题【反射、注解与依赖注入总结】。


## ● 反射（Reflection）

#### 反射的概念

主要是指程序可以访问，检测和修改它本身状态或行为的一种能力，并能根据自身行为的状态和结果，调整或修改应用所描述行为的状态和相关的语义。

概念看着就有些晕或不知所云啦，可以通过反射的作用理解它的概念。

#### 反射的作用

反射可以让我们在运行时获取类的属性，方法，构造方法、父类、接口等信息，通过反射还可以让我们在运行期实例化对象、调用方法、即使方法或属性是私有的的也可以通过反射的形式调用。

所有为什么第三方库基本都会使用到反射，正是因为反射这种 “看透 Class” 的能力。

#### 反射相关的类、方法

要看透一个类，首先要获取这个类的对象，其它信息都是通过这个对象获取的，下面的所有的示例具体操作代码请参考 [【个人学习项目DroidStudy】](https://github.com/sfsheng0322/In-depthStudy)，我在这个工程下新建一个 ReflectionActivity，包的路径为 com.sun.study.ui.activity.ReflectionActivity，通过反射相关的类、方法让我看透这个类。

1、获取对象的三种方式：

第一种、知道一个类，直接获取 Class 对象

	Class<?> cls1 = ReflectionActivity.class;

第二种、如果已经得到了某个对象，可以通过这个对象获取 Class 对象

	ReflectionActivity activity = new ReflectionActivity();
	Class<?> cls2 = activity.getClass();

第三种、如果你在编译期获取不到目标类型，但是你知道它的完整类路径，那么你可以通过如下的形式来获取 Class 对象，这样获取可能会抛出异常 ClassNotFoundException。

	try {
		Class<?> cls3 = Class.forName("com.sun.study.ui.activity.ReflectionActivity");
	} catch (ClassNotFoundException e) {
		e.printStackTrace();
	}

2、反射的相关方法和示例

列出反射的相关方法

	getName()：获得类的完整名字。  
	newInstance()：通过类的不带参数的构造方法创建这个类的一个对象。

	getFields()：获得类的public类型的属性。  
	getDeclaredFields()：获得类的所有属性。

	getMethods()：获得类的public类型的方法。  
	getDeclaredMethods()：获得类的所有方法。  
	getMethod(String name, Class[] parameterTypes)：获得类的特定方法。
	
	getModifiers()和Modifier.toString()：获得属修饰符，例如private，public，static等  
	getReturnType()：获得方法的返回类型  
	getParameterTypes()：获得方法的参数类型

	getConstructors()：获得类的public类型的构造方法。  
	getConstructor(Class[] parameterTypes)：获得类的特定构造方法。
	
	getSuperclass()：获取某类的父类  
	getInterfaces()：获取某类实现的接口
	
示例一：获得类的所有方法（Method）信息
	
	private void getMethodsInfo() {
        Class<ReflectionActivity> cls = ReflectionActivity.class;
        Method[] methods = cls.getDeclaredMethods();
        if (methods == null) return;

        StringBuilder sb = new StringBuilder();
        for (Method method:methods) {
            sb.append(Modifier.toString(method.getModifiers())).append(" ");
            sb.append(method.getReturnType()).append(" ");
            sb.append(method.getName()).append("(");
            Class[] parameters = method.getParameterTypes();
            if (parameters != null) {
                for (int i=0; i<parameters.length; i++) {
                    Class paramCls = parameters[i];
                    sb.append(paramCls.getSimpleName());
                    if (i < parameters.length - 1) sb.append(", ");
                }
            }
            sb.append(")\n\n");
        }

        tvInfo.setText(sb.toString());
    }
    
运行结果如下图：

<img src="/assets/reflection_icon1.png" style="width: 50%;">

示例一：获得类的所有属性（Field）信息，并修改类型Int属性i的值
    
    private void modifyFieldValue() {
        Class<ReflectionActivity> cls = ReflectionActivity.class;
        Field[] fields = cls.getDeclaredFields();
        if (fields == null) return;

        StringBuilder sb = new StringBuilder();
        sb.append("获得类的所有属性信息：\n\n");
        for (Field field:fields) {
            sb.append(Modifier.toString(field.getModifiers())).append(" ");
            sb.append(field.getType().getSimpleName()).append(" ");
            sb.append(field.getName()).append(";");
            sb.append("\n\n");
        }

        try {
            sb.append("属性i的默认值：i = ");
            Field f = cls.getDeclaredField("i");
            sb.append(f.getInt("i")).append("\n\n");
            f.set("i", 100);
            sb.append("属性i修改后的值：i = ");
            sb.append(f.getInt("i")).append("\n\n");
        } catch (Exception e) {
            e.printStackTrace();
        }

        tvInfo.setText(sb.toString());
        toolbar.setSubtitle("修改类型Int属性i的值");
    }
    
运行结果如下图：

<img src="/assets/reflection_icon2.png" style="width: 50%;">

更多示例请参考 [【个人学习项目DroidStudy】](https://github.com/sfsheng0322/In-depthStudy)

反射的相关内容先记录到这，接下来看看注解相关概念与使用。
	

## ● 注解（Annotation）

#### 注解的概念

注解（Annotation），也叫元数据。一种代码级别的说明。它是JDK 1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明，注释。

#### 注解的作用

1、标记作用，用于告诉编译器一些信息让编译器能够实现基本的编译检查，如@Override、Deprecated，看下它俩的源码
	
	@Target(ElementType.METHOD)
	@Retention(RetentionPolicy.SOURCE)
	public @interface Override {
	}
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	public @interface Deprecated {
	}

2、编译时动态处理，动态生成代码，如[Butter Knife](https://github.com/JakeWharton/butterknife)、[Dagger 2](https://github.com/google/dagger)

3、运行时动态处理，获得注解信息，如[Retrofit](https://github.com/square/retrofit)

#### 注解的分类

注解的分类有两种分法：

##### 第一种分法

1、基本内置注解，是指Java自带的几个Annotation，如@Override、Deprecated、@SuppressWarnings等

2、元注解（meta-annotation），是指负责注解其他注解的注解，JDK 1.5及以后版本定义了4个标准的元注解类型，如下：

	1、@Target
	2、@Retention
	3、@Documented
	4、@Inherited

3、自定义注解，根据需要可以自定义注解，自定义注解需要用到上面的meta-annotation
	

##### 第二种分法，根据作用域分类

1、源码时注解（RetentionPolicy.SOURCE）  
2、编译时注解（RetentionPolicy.CLASS）  
3、运行时注解（RetentionPolicy.RUNTIME）

#### 注解相关知识点

1、元注解相关信息

@Target：指Annotation所修饰的对象范围，通过ElementType取值有8种，如下

    TYPE：类、接口（包括注解类型）或枚举
    FIELD：属性
    METHOD：方法
    PARAMETER：参数
    CONSTRUCTOR：构造函数
    LOCAL_VARIABLE：局部变量
    ANNOTATION_TYPE：注解类型
    PACKAGE：包

@Retention：指Annotation被保留的时间长短，通过RetentionPolicy取值有3种，如下：

	SOURCE：在源文件中有效（即源文件保留）  
	CLASS：在class文件中有效（即class保留）  
	RUNTIME：在运行时有效（即运行时保留）

@Documented：是一个标记注解，用于描述其它类型的注解应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。

@Inherited：也是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的

2、注解定义格式

	public @interface 注解名 { 定义体 }

3、注解参数可支持的数据类型：

	8种基本数据类型 int、float、boolean、byte、double、char、long、short  
	String、Class、enum、Annotation  
	以上所有类型的数组
	
4、⚠注意：自定义注解如果只有一个参数成员，最好把定义体参数名称设为"value"，如@Target

	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Target {
    	ElementType[] value();
	}

#### 看一个示例

具体要求和运行结果都在下面这张图上显示出来了，贴下图

<img src="/assets/annotation_icon.png" style="width: 50%;">

再贴三块代码，首先是自定义注解代码：

	@Target(ElementType.METHOD)
	@Retention(RetentionPolicy.RUNTIME)
	@Inherited
	@Documented
	public @interface RequestAnnotation {
    	boolean withDialog() default true;
    	String withMessage() default "正在加载，请稍后...";
	}

其次是执行模拟的网络请求，核心代码是通过上面的反射和注解完成的；具体详细代码请参考 [【个人学习项目DroidStudy】](https://github.com/sfsheng0322/In-depthStudy)，下次使用动态代理和Google的dexmaker完成这个功能，敬请关注，如果你对线程池还不清晰请参考我以前的文章[【线程、多线程与线程池总结】](http://www.jianshu.com/p/b8197dd2934c)。贴下核心代码：

	// 线程池
	private static ExecutorService pool = Executors.newCachedThreadPool();

	// 模拟处理网络请求
    public boolean process(final Class<?> clazz, String methodName, final Object... args) throws Exception {
        Class[] argsClass = getClazzByArgs(args);

        final Method method = clazz.getDeclaredMethod(methodName, argsClass);
        if (method == null) {
            sendMsg(TYPE_ERROR);
            return false;
        }

        // 获取注解信息
        RequestAnnotation annotation = method.getAnnotation(RequestAnnotation.class);
        if (annotation != null && annotation.withDialog()) {
            loadingDialog.show(annotation.withMessage());
        }

        pool.execute(new Runnable() {
            @Override
            public void run() {
                try {
                    method.setAccessible(true);
                    method.invoke(clazz.newInstance(), args);
                    sendMsg(TYPE_SUCCESS);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        return true;
    }
    
最后是调用网络请求接口：

	@RequestAnnotation(withDialog = false, withMessage = "正在加载，请稍后...")
    public void apiTestFunc(String param1, String param2) {
        try {
            // 模拟网络请求的耗时操作
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    
    // 点击执行的代码
    DynamicProxyUtil proxyUtil = new DynamicProxyUtil(AnnotationActivity.this);
	proxyUtil.process(RequestNetworkApi.class, "apiTestFunc", "参数一", "参数二");

## ● 依赖注入（Dependency Injection）

依赖注入（Dependency Injection）：可以通过这个服务来安全的注入组件到应用程序中，在应用程序部署的时候还可以选择从特定的接口属性进行注入。

看完上面反射和注解的记录后，可以更好的理解依赖注入，如果你不用那些第三方的注入库你也在经常用到依赖注入，比如下面这一段从[codekk](http://p.codekk.com/)上截取的代码：

	public class Human {
    	...
    	Father father;
    	...
    	public Human(Father father) {
        	this.father = father;
    	}
	}
	
上面代码中，我们将 father 对象作为构造函数的一个参数传入。在调用 Human 的构造方法之前外部就已经初始化好了 Father 对象。像这种非自己主动初始化依赖，而通过外部来传入依赖的方式，我们就称为依赖注入。

依赖注入的实现有多种途径，而在 Java 中，使用注解是最常用的。比如通过Butter Knife、Dagger依赖注入库实现，都是使用注解来实现依赖注入，但它利用 APT(Annotation Process Tool) 在编译时生成辅助类，这些类继承特定父类或实现特定接口，程序在运行时加载这些辅助类，调用相应接口完成依赖生成和注入。

依赖注入在这里仅仅剖析下概念，有时间将会补一个例子，暂且到这吧。





