---
layout: post
author: 孙福生
title: 通过 Retrolambda 体验 Java 8 Lambda 表达式
background-color: '#259b24'
tags: Android Retrolambda
---
   
Lambda 表达式是在Java 8中开始支持的一种函数式推导语言，能够大量减少匿名内部类那种冗余的代码。在Android中，可以大量使用在设置监听、设置异步回调等场景。

当然在Android工程中直接使用JDK 8编译向下兼容还是有问题，你可以通过 [Retrolambda](https://github.com/evant/gradle-retrolambda) 让使用Lambda表达式的Java 8代码运行在Java 7或更低版本上，让Lambda不再专属于Java 8，提高工程师的逼格。

1、将 [Retrolambda](https://github.com/evant/gradle-retrolambda) 运用于Android中：

```Java
	在Project的build.gradle中添加
	buildscript {
	  repositories {
	     mavenCentral()
	  }
	  dependencies {
	     classpath 'me.tatarka:gradle-retrolambda:3.2.5'
	  }
	}

	在app的build.gradle中添加
	apply plugin: 'com.android.application'
	apply plugin: 'me.tatarka.retrolambda'
	android {
	  compileOptions {
	    sourceCompatibility JavaVersion.VERSION_1_8
	    targetCompatibility JavaVersion.VERSION_1_8
	  }
	}
```

2、体验之前，先看一下Lambda的语法：

```Java
	parameter -> expression
	(parameters) -> expression
	(parameters) -> statement
	或者
    (parameters) -> { statements; }

    总结出一般的语法
    (Type1 param1, Type2 param2, ..., TypeN paramN) -> {
    	statment1;
		statment2;
		...
		return statmentN;
	}
```

3、看完语法后，下面看看它逼格到底有多高：

```Java
	匿名内部类的简化

	button.setOnClickListener((v) -> doSomething(v));
	button.setOnClickListener(this::doSomething); // 方法引用是使用两个冒号::这个操作符号

	private void doSomething(View v) {
		...
	}

	或者

	button.setOnClickListener((v) -> doSomething());
	button.setOnClickListener((v) -> {
		doSomething();
		doSomething();
		doSomething();
	});

	private void doSomething() {
		...
	}
```

当然，使用Lambda可以支持许多操作，如 map, filter, limit, sorted, count, min, max, sum, collect 等等，暂时体验一下。翠华，先不上菜啦，后续更新。




