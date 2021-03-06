---
title: "Java类复用"
date: 2019-10-21T12:54:56+08:00
categories: "笔记"
description: "Java编程思想读书笔记——复用类"
tags: [  "笔记"]
---

## 从现有类生成新类型

* 组合： 一般将现有类型作为新类型底层实现一部分来加以复用
* 继承：仅仅复用接口 用到 extends关键字

## 继承

### 继承中的初始化

在构造器中调用基类构造器来执行初始化来执行初始化。对象包含基类的子对象，它的正确初始化很重要

### 清理

必须显式编写特殊方法，还必须将这清理动作置于finally子句（异常处理的内容，以后再谈，只知道异常与否都会最后执行的代码）中，以防异常，最好不适用finalize()。

## 继承和组合的选择

一般最后选择组合，因为编程更为灵活。当类型有向上转型的需求是，则使用继承

有时允许类用户直接访问新类中的组合成分（将成员对象声明为public）如果成员对象自身隐藏了实现，这么做是安全的

@Override 注解表示方法要覆写（如果重载，报错）

## final关键字

可以使用在数据（域），方法， 类

修饰数据，表示值不可改变。意思是说，永远不改变编译常量，还有运行时初始化后不可再改变。当域既是final，又是static，表示一块存储区域不可以改变；空白的final在使用前必须初始化；而却允许final修饰参数列表（类似于C函数参数中const）

* 注：上面说明了final修饰基本数据类型的情况，如果final修饰声明是对象引用，表示引用的指向不可改变，不代表不可改变对象

置于方法之前，表示禁止覆盖此方法（在继承中使用），继承不能覆写该方法；或者不再继承中使用，会同意编译器将方法内嵌调用以提高速度（类似C的内联函数）

* 注：不可以覆盖private方法，会造成混淆，因为本质并不是覆盖，仅是生成新方法，名称与基类private相同，且编译器不宝座

final类表示不可被继承，所以方法隐式指定为final，而域自己选择是否为final。是否使用final类，主要出于设计的考虑

* 注： Java中基类重载方法名称，在派生类重新定义该名称方法不会屏蔽基类的重载版本（与C++不同）