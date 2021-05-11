---
title: "Java内部类"
date: 2019-10-24T16:48:18+08:00
categories: "笔记"
description: "Java编程思想读书笔记——内部类"
tags: [  "笔记"]
---

## 创建内部类

直接将内部类置于外围类中

```java
// innerclasses/Parcel1.java
// Creating inner classes
public class Parcel1 {
 class Contents {
  private int i = 11;
  public int value() { return i; }
 }

 class Destination {
  private String label;

  Destination(String whereTo) {
   label = whereTo;
  }

  String readLabel() { return label; }
 }
 // Using inner classes looks just like
 // using any other class, within Parcel1;
 public void ship(String dest) {
  Contents c = new Contents();
  Destination d = new Destination(dest);
  System.out.println(d.readLabel());
 }
 public static void main(String[] args) {
  Parcel1 p = new Parcel1();
  p.ship("Tasmania");
 }
} /* Output:
Tasmania
*///:~
```

如果想从外部类的非静态方法之外的任意位置创建内部类的对象，那么不像在 **main**方法中那样，具体地指明这个对象的类型：OuterClassName.InnerClassName。

## 链接到外部类

内部类除了时名字隐藏和组织代码的模式，它还与制造它的外围类发生联系，能访问其外围对象的所有成员，无需任何特殊条件

```java
//: innerclasses/Parcel2.java
// Returning a reference to an inner class
public class Parcel2 {
 class Contents {
  private int i = 11;

  public int value() { return i; }
 }

 class Destination {
  private String label;

  Destination(String whereTo) {
   label = whereTo;
  }

  String readLabel() { return label; }
 }

 public Destination to(String s) {
  return new Destination(s);
 }

 public Contents contents() {
  return new Contents();
 }

 public void ship(String dest) {
  Contents c =  contents();
  Destination d = to(dest);
  System.out.println(d.readLabel());
 }

 public static void main(String[] args) {
  Parcel2 p = new Parcel2();
  p.ship("Tasmaina");
  Parcel2 q = new Parcel2();
  // Defining references to inner classes:
  Parcel2.Contents c = q.contents();
  Parcel2.Destination d = q.to("Borneo");
 }
}
```

这是个迭代器设计模式。内部类用到了**items**，它并不是内部类的部分，是外围类的private字段，说明了内部类可以访问外部类的成员。

当某个外围类创建了一个内部类对象，此内部类对象必定秘密捕获一个指向外围类的引用。然后，在你访问此外围类的成员时，就是通过那个引用来选择外围类成员。一般编译器会处理捕获引用的细节，而且内部对象只能在与其外围类的对象发生相关联的情况下才能被创建（内部类有使用外围类成员就会发生关联）。内部类是非static类。

## 使用.this 与 .new

如果内部类需要生成对外部类对象的引用，可以**OuterClassName.this**像这样，产生的引用会在编译器自动确定正确类型，没有运行时开销。

```java
//: innerclasses/DotThis.java
// Accessing the outer-class object
public class DotThis {
 void f() { System.out.println("DotThis.f()"); }
 public class Inner {
  public DotThis outer() {
   return DotThis.this;
   // A plain "this" would be Inner's "this"
  }
 }

 public Inner inner() { return new Inner(); }
  public static void main(String[] args) {
   DotThis dt = new DotThis();
   DotThis.Inner dti = dt.inner();
   dti.outer().f();
 }
}
```

如果可能创建某个对象的内部类对象，要在new表达式中提供外部类对象的引用, 像**OuterObject.new**的表达式

```java
//: innerclasses/DotNew.java
// Creating an inner class directly using .new syntax
public class DotNew {
 public class Inner { }
 public static void main(String[] args) {
  DotNew dn = new DotNew();
  DotNew.Inner dni = dn.new Inner(); 
 }
}
```

## 内部类向上转型

内部类向上转型为其基类，尤其是转型为一个接口，会有大用途。这是因为内部类可以设定为private（或protected），能够对外部（或包外除继承子类）不可见且不可用，得到的只是指向基类或接口的引用，所以通过这种方式可以完全组织任何依赖于类型的编码，很方便地实现隐藏实现细节。只有内部类可以设为private和protected。

## 在方法和作用域的内部类

在方法中或任意的作用域中定义内部类，有两个原因：

1.实现某个类型的接口，可以创建并返回对其的引用

2.要解决一个复杂问题，借用一个类辅助，但又不希望此类是公共可用的

### 局部内部类

在方法和作用域中，需要已命名的构造器或重载构造器，使用局部内部类，相比匿名类，可以使用多个该类对象。

### 匿名内部类

把对象生成和类型定义结合在一起，用在方法的返回值中, 只能实例初始化，如：

```java
public interfaces Destination {
    String readLabel();
}
public class Parcel9 {
// Argument must be final or "effectively final"
// to use within the anonymous inner class:
 public Destination destination(final String dest) {
  return new Destination() {
   private String label = dest;
   @Override
   public String readLabel() { return label; }
  };
 }
 public static void main(String[] args) {
  Parcel9 p = new Parcel9();
  Destination d = p.destination("Tasmania");
  //System.out.println(d.readLabel());
 }
}
```

如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用是final。想做一些类似构造器的行为，但匿名类没有命名的构造器，可以用实例初始化来达到构造器的行为。

### 再访工厂方法

```java
//: innerclasses/Factories.java
import static net.mindview.util.Print.*;

interface Service {
    void method1();
    void method2();
}

interface ServiceFactory {
    Service getService();
}

class Implementation1 implements Service {
    private Implementation1() {}
    public void method1() { print("Implementation1.method1"); }
    public void method2() { print("Implementation1.method2"); }
    public static ServiceFactory factory = 
        new ServiceFactory() {
            @Override
            public Service getService() {
                return new Implementation1();
            }
        };
}

class Implementation2 implements Service {
    private Implementation2() {}
    public void method1() { print("Implementation2.method1"); }
    public void method2() { print("Implemetation2.method2"); }
    public static ServiceFactory factory =
        new ServiceFactory() {
            @Override
            public Service getService() {
                return new Implementation2();
            }
        };
}

public class Factories {
    public static void serviceConsumer(ServiceFactory fact) {
        Service s = fact.getService();
        s.method1();
        s.method2();
    }
    public static void main(String[] args) {
        serviceConsumer(Implementation1.factory);
        serviceConsumer(Implementation2.factory);
    }
}
```

**Implementation1** 和 **Implementation2**的构造器是private的，并且没有任何要去创建作为工厂的具名类。另外，你经常只需要单一的工厂对象，因此在本例中它被创建为Service实现中的一个static域。

### 嵌套类

将内部类声明为static，通称为嵌套类。意味着要创建嵌套类的对象，并不需要其外围类的对象；不能从嵌套类的对象中访问非静态的外围类对象。也就说嵌套类是与外部类没有联系的内部类。它和内部类的区别在于，普通内部类的字段和方法不能是static，也不能包含嵌套类，而嵌套类可以包含这些。

#### 接口内部的类

如果想创建某些 *公共代码* ，使得它可以被某个接口的所有不同实现所共用，那么在接口内部嵌套类会显得方便。接口中的类自动地是public 和 static的，因为类是static的，只是将嵌套类置于接口的命名空间内，并不会违反接口的规则。

```java

public interface ClassInInterface {
    void howdy();
    class Test implememnts ClassInInterface {
        public void howdy() {
            System.out.println("Howdy!");
        }
        public static void main(String[] args) {
            new Test().howdy();
        }
    }
}
```

一般建议为每个类写一个**main**函数，用来测试这个类，这样做有个缺点，必须带着那些已编译过的额外代码。如果这对你很麻烦，可以使用嵌套类来放置测试代码，在不需要的时候，删掉嵌套类文件。

一个内部类被嵌套多少层，都能够透明地访问到所有它所嵌入的外围类的所有成员。

## 为什么需要内部类

可以认为内部类提供某种进入其外围类的窗口。还可以是多继承的解决方案变得完整，
如果拥有的是抽象的类或具体的类，而不是接口，只能使用内部类实现多继承。

## 继承内部类

继承内部类的构造器必须连接到指向基类的外围类的引用，还要在构造器内调用基类的构造器
，编译才能通过。

```java
//: innerclasses/InheritInner.java
// Inheriting an inner class
class WithInner {
 class Inner {}
}
public class InheritInner extends WithInner.Inner {
 //- InheritInner() {} // Won't compile
 InheritInner(WithInner wi) {
  wi.super();
 }
 public static void main(String[] args) {
  WithInner wi = new WithInner();
  InheritInner ii = new InheritInner(wi);
 }
}
```

## 内部类可以覆盖吗

当继承了某个外围类时，内部类并没有发生什么神奇的变化,两个内部类时完全独立的两个实体。

## 内部类标识符

类文件命名规则其实是外围类名字加上$，再加上内部类的名字;
如果内部类是匿名类，编译器简单地产生一个数字作为标识符；
如果内部类是嵌套在别的内部类之中，只需直接将它们的名字放在其外围类标识符与$的后面
例如：

> Counter.class,
> LocalInnerClass$1.class, // 匿名类标识符：用一个数字表示，文件命名：外围类名字+$+数字
> LocalInner$1LocalCounter.class, //
> LocalInnerClass.class
