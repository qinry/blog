---
title: "接口"
date: 2019-10-22T16:29:48+08:00
tags: [ "java"]
---

## 抽象类和抽象方法

* 抽象方法：仅有声明而没有方法体。 如： ```abstract void f();```

* 抽象类： 包含抽象方法的类。

如果一个类包含一个或多个抽象方法，该类必须限定为抽象的。如：```abstract class A {}```

继承抽象类，如果不把抽象方法提供定义， 那么导出类也是抽象类，所以要创建抽象类的子类的对象，必须在子类为基类提供方法的定义。有时，我们也可能创建一个没有任何抽象方法的抽象类，比如不想创建该类的对象，可声明为abstract。

抽象类还是很有用的重构[^1]工具，因为它们使得我们可以很容易地将公共方法沿继承层次结构上移动。

[^1]: 重构（Refactoring）就是通过调整程序代码改善软件的质量、性能，使其程序的设计模式和架构更趋合理，提高软件的扩展性和维护性。

## 接口

使用关键字interface 产生一个完全抽象的类，它根本就没有提供任何具体实现。

```java
interface Instrument { void play(); String what(); void adjust(); }
```

接口只提供形式，不提供具体实现。可在interface前添加public（但仅限于该接口在与其同名文件中被定义）。如果不添加public，它只有包访问权限。包也可以包含域，但是这些域隐式地是static和final的；方法如果没声明为public，是会自动转换为public的

implements关键字让一个类遵循某个特定的接口，实现接口的功能。
如：

```java
class Wind implements Instrument {
    void play() {}
    String what() {}
    void adjust() {}
}
```

实现了一个接口时，在接口中被定义的方法必须被定义为public；否则，它们将只能得到默认的包访问权限，这样的方法被继承的过程中，其访问权限就被降低了，这是Java编译器所不允许的。

通过适配器模式，将接口从具体实现中解耦使得接口可以应用于多种不同的具体实现，因此代码也就更具可复用性

## 多重继承

在C++中，组合多个类的接口的行为称作**多重继承**。每个类都有具体的实现，这样代码量会大的多，编写相较麻烦。

在Java中，你可以执行相同的行为，但只有一个类可以有具体的实现。代码量比C++少了不少。

如果要从一个非接口的类继承，那么只能从一个类去继承。其余基元素必须是接口。需要将所有接口名都置于implements关键字之后，用逗号将它们一一隔开。将具体类和多个接口组合在一起，此具体类必须放在前面，后面跟着的才是接口（否则编译器报错）

使用接口的核心原因：为了能够向上转型为多个基类型（以及由此带来的灵活性）。还有与使用抽象基类相同，防止客户端程序员创建该类对象，并确保这仅仅是接口。允许同一个类有多个具体的实现

### 应该使用接口还是抽象类

如果要创建不带任何方法定义和成员变量的基类，那么应该选择接口而不是抽象类型。

## 接口继承

一般情况下，只可以将extends 用于单一类，但是接口可以引用多个基类接口。

## 组合接口时的命名冲突

在打算组合的不同接口中使用相同的方法名通常会造成代码可读性的混乱，请尽量避免这种情况。重载方法仅通过返回类型是分不开的。

## 接口中的域

接口中的域默认是static，final的，所以接口是成为一种很便捷的用来创建常量组的工具。过去JavaSE5，用它来产生C和C++的enum类型效果。

## 嵌套接口

接口可以嵌套于接口或类中。
嵌套于接口的接口是不能声明为private，必须是public，在此会严格执行。实现一个接口时，并不需要实现嵌套在其内部的任何接口。而且，private接口不能在定义它的 *类* 之外被发现，只能被类自身所使用。

## 接口与工厂

生成遵循某个接口的对象的典型方法时 *工厂方法* 设计模式，与构造函数不同，工厂对象调用的工厂方法，会返回（后者说生成）接口的某一个实现的对象。也可使用匿名内部类实现工厂，下章再谈。

```java
interface Service {
    void f();
}
interface ServiceFactory {
    Service getService();
}
class Implementation implements Service {
    public void f() { System.out.println("Implementation.f()"); }
    Implementation() {}
}
class ImplementationFactory implements ServiceFactory {
    public Service getService() { return new Implementation(); }
}
public class Factory {
    public static void serviceConsumer(ServiceFactory fact) {
        Service s = fact.getService();
        s.f();
    }
    public static void main(String[] args) {
        serviceFactory(new ImplementationFactory());
    }
}
```
