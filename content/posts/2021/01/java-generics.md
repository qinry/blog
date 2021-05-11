---
title: "泛型"
date: 2021-01-31T17:09:15+08:00
categories: "Java"
description: "泛型类，泛型接口，泛型方法，泛型通配符以及类型擦除"
tags : ["Java"]
---

### 理解泛型

泛型是将类型参数化，类定义时一些成员变量和一些方法形式参数的类型成为变量，在使用时再传入一个具体类型，进一步抽象了变量的类型。好像是给方法或类写了一个模板，根据模板赋予不同的类型参数，就会生成不同的类。将那些类似方法或类都集中在一起编写，也就是将相同的部分都封装起来，不同的部分参数化。某种程度，减少编写只因类型不同，逻辑大部分相同的多个类的代码量，使这部分代码耦合度降低，提高了代码的复用率。在Java中的泛型使用时指定类型但不需要强制类型转换，不必担心，类型是安全的，编译器会检查。

### 使用泛型

#### 泛型类

定义泛型类：

```java
public class Stack<E> implements Iterable {
    private Node first;
    private class Node {
 		private E element;
  		private Node next;
  		public Node(E e) {
        	this.element = e;
        	next = null;
  		}
    	public Node(Node x) {
    		element = x.element;
  			if (x.next != null) {
     			next = new Node(x.next);
    		}
  		}
	}
    // 其他方法省略
}
```

创建泛型类的对象：

```java
Stack stack = new Stack<String>();
```

成员内部类是可以使用外部类的类型参数，静态内部类却不能。

#### 泛型接口

定义泛型接口：

```java
interface Callable<V> {
    V call() throws Exception;
}
```

实现泛型接口，指定类型：

```java
class StringCaller implements Callable<String> {
   @Override
   public String call() {
       return "String";
   }
}
```

实现泛型接口，不指定类型：

```java
class Caller<V> implements Callable<V> {
    private V val;
    public V call() {
        return val;
    }
    public void setVal(V val) {
        this.val = val;
    }
}
```

#### 泛型方法

一个普通类的方法可以使用泛型。形式大致：

```java
权限修饰符 <T> T 方法名(T a, T b) {}
```

非静态方法：

```java
class GenericMethod {
    public <T> void f(T t) {
        System.out.println(t.getClass().getName());
    }
}
```

静态方法：

例如：
```java
import java.lang.reflect.*;
import java.util.regex.*;
class StaticGenericMethod<T> {
    public static <T> void f(T t) {
        Pattern p = Pattern.compile("\\w+\\.");
        for(Method m : t.getClass().getMethods())
            System.out.println(
                p.matcher(
                    m.toString()).replaceAll(""));
    }
}
```

由于static方法无法访问泛型类的类型参数，static的泛型方法必须在返回值类型前加上`&lt;类型参数&gt;`这个类型参数的声明。非静态方法如果是在泛型类内部，其实已经隐含了声明，所以不用特别写出来，但如果是普通类中，一定要写。

#### 泛型限制类型

在使用泛型时， 可以指定泛型的限定区域 ，

*   例如： 必须是某某类的子类或 某某接口的实现类，格式：

```java
<T extends 类或接口1 & 接口2> 
```

#### 泛型的通配符

```java
public class Demo {
   public static void main(String[] args) {
       ArrayList<Fruit> fruits = new ArrayList<Apple>();
   }
}
class Fruit {}
class Apple extends Fruit {}
```

上面的main方法，编译器是无法通过运行的。不同于方法的多态，是可以传入变量参数时，实际参数时可以为形参的子类。但泛型的使用不允许这么做，这里可以理解，类型ArrayList&lt;Fruit&gt;和ArrayList&lt;Apple&gt;是完全不同的类型，之间更没父子关系。Java为了更好的使用泛型，提出了泛型的通配符。

类型通配符是使用？代替方法具体的类型实参。

无界通配符&lt;?&gt;表示某种特定类型，创建对象时传入的类型参数可以是任意类型，对象一旦传入创建好了，类型就确定，它方法使用类型参数也就跟着确定了。与&lt;Object&gt;是不同的意思，类型参数确定为Object，只不过使用这个类型参数的方法是可以传入任何类型的变量（因为Object是所有类共同的基类，Java是走单继承体系的）;

超类通配符&lt;? super T&gt;或&lt;? super 具体类&gt;,由T或具体类的基类来界定类型参数，它规定类型的下界;

子类通配符&lt;? extends T&gt;或&lt;? extends 具体类&gt;，由T或具体类的子类来界定类型参数，规定了类型的上界。通配符用在方法的泛型类参数或泛型类变量声明上。

就如:

```java
List<?> fruits = new ArrayList<Fruit>();
```

```java
// 假设定义好类Fruit及其子类Apple,下面的代码在某个类内部

void write(List<? super Fruit> fruit, Fruit newFruit) {
    fruit.add(newFruit);
}

Fruit read(List<? extends Fruit> fruit, int i) {
    return fruit.get(i);
}
```

#### 泛型擦除

在编译之后程序会采取去泛型化的措施。

也就是说Java中的泛型，只在编译阶段有效。

在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦除，并且在对象进入和离开方法的边界处添加 类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段