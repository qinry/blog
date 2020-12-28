---
title: "设计模式——单件模式"
date: 2020-11-16T08:08:26+08:00
categories: "Design Pattern"
tags: ["Design Pattern"]
---

# 单件模式

## 什么是单件模式
单件模式是在设计模式中常用的一种需要为类只实例化唯一对象的设计方案。意思是说，对于某个类来说，其对象只能创建一个，且每次使用这个类的时候，用到的都是同一个对象。在有些场景下，有些对象我们只需要一个，比如：线程池、缓存、对话框、处理偏好设置和注册表的对象、日志对象、打印机、显卡等表示设备某些资源的对象。也常常用来管理共享的资源，比如数据库连接池等。

看看单件模式的官方定义：**单件模式** 确保一个类只有一个实例，并提供一个全局访问点。

## 单件模式的优点
单件模式除了可以确保只有一个实例会被创建，还提供与全局变量一样方便的访问。全局变量，它可能必须在程序一开始就要创建好，如果这个对象很耗资源，而程序偏偏没有使用到它，这对资源就是一种实实在在的浪费，所以应该在需要时才被创建就可以解决这个问题，正好单件模式能够做到。

## 如何做到只实例化和管理唯一的对象
在Java中通过`new`表达式公开创建对象，我们必须隐藏这个创建动作，只能由类自己进行，对外禁止使用`new`。那么客户如何获得对象呢？首先类内管理一个私有成员实例变量，这成员类型就是本身，由于要求对象唯一，声明为`static`变量，再可以通过一个公共静态方法，叫`getInstance()`好了，向类发出请求访问并获得这个实例变量即可，为什么要用静态方法？避免外部显式直接获取对象导致**硬编码**。还有获取对象的构造器对外是严令禁止，实例方法无法使用，借助类方法或者叫静态方法不用实例化就可以直接使用的方法获得对象。对象的创建其实就封装在这个静态方法`getInstance()`，通过条件判断实例变量是否为空，为空则创建对象再返回；不为空直接返回对象。这种在需要时创建的对象叫做**延迟实例化**。

```java
public class Singleton {
    private static Singleton uniqueInstance;
    private Singleton() {}
    public static getInstance() {
        if(uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

---

### 需要注意的问题
在使用单件模式需要考虑多线程多次调用`getInstance()`方法获取对象可能导致创建对象不唯一的问题，那这还算单件吗？就如同前面的代码就是线程不安全的单件，如果有两个线程同时交替执行通过了`if(uniqueInstance == null)`语句,接下来就会导致`new`使用了两次，实例化了两个不同的对象。

### 使用synchronized方法解决
将`getInstance()`方法声明为`synchronized`，给这个方法加了锁，确保只有一个线程访问这个方法。虽然解决了线程安全问题，但是执行效率下去了，因为只有第一次调用`getInstance()`才需要同步，之后的同步是累赘，拖慢程序。

```java
public class Singleton {
    private static Singleton uniqueInstance;
    private Singleton() {}
    public synchronized static getInstance() {
        if(uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

不考虑性能时，synchronized方法解决最直接。


### 去延迟实例化而急切实例化解决
在静态初始化器中创建单件，保证线程安全，`getInstance()`也不用声明`synchronized`直接返回实例变量即可。JVM保证任何线程在访问`getIntance()`方法之前，创建单件实例。

```java
public class Singleton {
    private static Singleton uniqueInstance = new Singleton();
    private Singleton() {}
    public static getInstance() {
        return uniqueInstance;
    }
}
```

解决线程不安全单件的一种可行方案。如果创建的对象小巧不太耗资源可以考虑使用这个方案。

## 使用双重检查加锁解决
利用双重检查加锁(double-checked locking)，可以减少同步，首先检查是否实例化，如果还没有，才进行同步，这样只有第一次调用`getInstance()`同步，后面直接返回对象。

```java
public class Singleton {
    private static volatile Singleton uniqueInstance;
    private Singleton() {}
    public static getInstance() {
        if(uniqueInstance == null) {
            synchronized(Singleton.class) {
                if(uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

使用双重检查加锁是在考虑性能因素时解决线程不安全的单件的最优方案，不过如果某些原因使用旧版Java（1.4以前版本）就不支持这个方案，这要求Java5及其以后的版本才能正确执行。现在都2020年了，基本上用的都是Java8及其以后的版本。

## 小结
单例模式确保程序中一个类最多只有一个实例；单件提供全局访问点。实现该模式，需要私有构造器，一个静态方法和一个静态变量。确定性能和资源上的限制，然后小心选择适当的方案实现单件，以解决多线程问题（同步方法，急切实例化，双检测加锁）。如果使用多个类加载器可能导致单件失效，所以要指定一个类加载器。

---
#### 参考
* Head First 设计模式（中文版）