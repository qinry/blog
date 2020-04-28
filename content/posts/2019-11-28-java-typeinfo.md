---
title: "类型信息"
date: 2019-11-28T18:25:37+08:00
tags: [ "java"]
---

## Class对象

每个类都存在包含类型信息的Class对象，Class对象属于Java类中Class类型。有两种方法获得某类型的Class 对象，一种是使用`Class.forName()`静态方法，接受包含完整类名的字符串参数，创建对应类型的Class对象;第二种类字面量，`<ClassName>.class`。forName()方法必须放在try语句块，而类字面量不需要，故效率更高。

例子：

```java
class Rock {
    static { System.out.println("Hi, I'm a Rock!"); }  
    public String  toString() {
        return "I don't like water";
    }
}

public class PlayRock {
    static void play(Rock rc) {
        System.out.println(rc);
    }
    public static void main(String[] args) {
        try {
            Class<?> type = Class.forName("Rock");  // ?是通配符表示任何事物
        } catch(ClassNotFoundException e) {
            System.out.println("No find Rock");
        }
        Rock rc = null;
        try {
            rc = (Rock)type.newInstance(); // 实例化得到却是Object引用，所以要强制类型转换
            play(rc);
        } catch(InstantiationException e) {
            System.out.println("Cannot instantiate");
            System.exit(1);
        } catch(IllegalAccessException e) {
            System.out.println("Cannot access");
            System.exit(1);
        }
    }
}
```

output:
> Hi, I'm a Rock!  
> I don't like water  

Class包含了很多方法，比如: 获取类型的名称的getName(),getSimpleName( ),getCanonicalName( );获得当前Class对象指向某个类的超类的Class对象getSuperclass( ),
 获得的Class对象不等同当前class对象的超类;实例化方法newInstance( ),注意其返回的是Object引用;isAssignableFrom()检查对象是否在Class对象引用类型的继承体系。

## cast( )方法

cast( )接受参数对象，将其类型转换为Class指向的类型。用法形似c++的dynamic_cast< T >(obj ) 。

例子:

```java
class Building {}
class House extends Building {}

public class ClassCasts {
    public static void main(String[] args) {
        Building b = new House();
        Class<House> houseType = House.class; // 使用泛化Class引用
        House h = houseType.cast(b);
        h = (House)b; // ... 或者这样做.
    }
}
```

如果类型转换出错了，回抛出 `ClassCastException` 异常

## 类型检查

它们都是用来检查某个对象是否是某个类型的实例对象，类型检查用在类型转换之前。

### 关键字instanceof

它会返回boolean类型值。不过其之后必须跟类型名，否则报错。

```java
if  (x instanceof Dog)
    ((Dog)x).bark( );
```

当进行多个类型检查必须一个一个罗列。

### Class.isInstance( )方法

提供动态测试对象类型的方法，使用更为灵活。使用for循环可以方便对一个对象进行多次检查

```java
for (class t : types)
    if (t.isInstance(obj))
        // 执行一些操作
```

解释:types是Class数组，obj实例对象，检查obj是否是types数组中所指类型。

## 反射机制

由java.lang.reflect类库提供支持，包含实现Member接口的Field, Method, Constructor类，分别表示未知类型的域，方法，构造器。getFields( )得到Field数组,
 同理getMethods( )和 getConstructors( )是Method数组，Constructor数组。比较常用的是对Method对象调用invoke()使用Method关联的方法, 对Field对象
 用get( )读取字段，set( ) 设置字段。

### 与RTTI的区别

对于 RTTI，编译器在编译时打开和检查.class文件，而反射机制是在运行时进行的，还有要知道Class.forName( )生成结果在编译时是不知道的。

反射机制提供足够的支持，使得能够创建一个编译时期完全未知的对象，并调用该对象的方法。还支持对象序列化和JavaBean。

### 类方法提取器

通过反射机制能很好帮助我们了解某个类的完整接口。

例程：

```java
// e.g {java ShowMethods ShowMethods}
import java.lang.reflect.*;

public class ShowMethods {
    public static void main(String[] args) {
        if (args.length <  1) {
            System.exit(0);
        }
        try {
            Class<?>  c = Class.forName(args[0]);
            Method[] methods = c.getMethods();
            Constructor[] ctors = c.getConstructors();
            if (args.length == 1) {
                for (Method method : methods) {
                    System.out.println(method.toString());
                }
                for (Constructor ctor : ctors) {
                    System.out.println(ctor.toString());
                }
            } else {
                for (Method method : methods) {
                    if (method.toString().indexOf(args[1]) != -1) {
                        System.out.println(method.toString());
                    }
                }
                for (Constructor ctor : ctors) {
                    if (ctor.toString().indexOf(args[1]) != -1) {
                        System.out.println(ctor.toString());
                    }
                }
            }
        } catch(ClassNotFoundException e) {
            System.out.println("No such class: " + e);
        }
    }
}
```

output:
>public static void ShowMethods.main(java.lang.String[])
>public final void java.lang.Object.wait(long,int) throws
>java.lang.InterruptedException
>public final native void java.lang.Object.wait(long) throws
>java.lang.InterruptedException
>public final void java.lang.Object.wait() throws
>java.lang.InterruptedException
>public boolean java.lang.Object.equals(java.lang.Object)
>public java.lang.String java.lang.Object.toString()
>public native int java.lang.Object.hashCode()
>public final native java.lang.Class java.lang.Object.getClass()
>public final native void java.lang.Object.notify()
>public final native void java.lang.Object.notifyAll()
>public ShowMethods()

## 动态代理

例程:

```java
import java.lang.reflect.*;

interface Interfaces {
    void doSomething();

    void somethingElse(String arg);
}

class RealObject implements Interfaces {
    public void doSomething() {
        System.out.println("doSomething");
    }

    public void somethingElse(String arg) {
        System.out.println("somethingElse : " + arg);
    }
}

class DynamicProxyHandler implements InvocationHandler {
    private Object proxied; // 被代理

    public DynamicProxyHandler(Object proxied) {
        this.proxied = proxied;
    }
    // 接口调用重定向为代理的调用
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println(
                "**** proxy: " + proxy.getClass() + ", method: " + method + ", args: " + args);
        if (args != null)
            for (Object arg : args)
                System.out.println(" " + arg);
        return method.invoke(proxied, args); // 转发给被代理对象调用
    }
}

public class SimpleDynamicProxy {
    public static void consumer(Interfaces iface) {
        iface.doSomething();
        iface.somethingElse("bonobo");
    }

    public static void main(String[] args) {
        RealObject real = new RealObject();
        consumer(real);
        // 添加代理并再次调用实际对象
        Interfaces proxy = (Interfaces) Proxy.newProxyInstance(Interfaces.class.getClassLoader(),
                new Class[] { Interfaces.class }, new DynamicProxyHandler(real));
        consumer(proxy);
    }
}
```

Output:
> doSomething  
> somethingElse : bonobo  
>**** proxy: $Proxy0, method: public abstract void  
>Interfaces.doSomething(), args: null  
> doSomething  
>**** proxy: $Proxy0, method: public abstract void  
> Interfaces.somethingElse(java.lang.String), args:  
> [Ljava.lang.Object;@4b67cf4d
> bonobo
> somethingElse : bonobo

静态方法Proxy.newProxyInstance()可以创建动态代理，它接受三个参数，一、类加载器,通过已经加载的对象获取，如上的Interfaces.class.getClassLoader( );二、希望代理实现的接口列表不包括类或者抽象类;三、InvocationHandler接口的实现。动态代理将所有方法的调用重定向给调用处理器，所以常常向调用处理器的构造器传入“实际”对象的引用，以便调用处理器请求转发任务。

## 空对象

内置null表示缺少对象，每次使用引用是都必须检测是否为null，使用null会引发NullPointerException异常;而使用空对象就可以不检查null，但同样来表示为空值，可以简单理解为通过实际对象虚拟的空值对象，来替代null，变得更智能。不过如果需要测试其为空，只要创建一个标记接口就行了。如：

```java
// Null.java
public interface Null { }
```

让空对象实现这个标记接口，通过`instanceof`来探测空对象，不强制为所有的类添加像isNull( )方法。空对象通常是单例，用`staic` 和`final` 修饰，故还可以使用equals()，== 与 空对象比较。空对象可以使用具体类或接口来创建，如果是通过接口，可以使用DynamicProxy自动创建空对象。

例程：

```java
// Book.java
public interface Book {
    String title();
    double price();
    int count();
    double total();
    class Test {
        public static void test(Book b) {
            if (b instanceof Null)
                System.out.println("Null Object");
            System.out.println("title : " + b.title());
            System.out.println("count: " + b.count() + ", price: "
             + b.price());
            System.out.println("total price: " + b.total());
        }
    }
}

// Novel.java
public class Novel implements Book {
    private String title;
    private double price;
    private int count;

    public Novel(String title) {
        this.title = title;
    }

    public Novel(String title, double price) {
        this(title);
        this.price = price;
    }

    public Novel(String title, double price, int count) {
        this(title, price);
        this.count = count;
    }

    public String title() {
        return title;
    }

    public int count() {
        return count;
    }

    public double price() {
        return price;
    }

    public double total() {
        return price * (double) count;
    }

    public static void main(String[] args) {
        Book.Test.test(new Novel("Robinson Crusoe by Daniel Defoe", 47.10, 3));
    }
}
```

output:
> title : Robinson Crusoe by Daniel Defoe  
> count: 3, price: 47.1  
> total price: 141.3

```java
// NullBook.java
import java.lang.reflect.*;

class NullBookProxyHandler implements InvocationHandler {
    private String nullName;
    private Book proxied = new NBook();

    public NullBookProxyHandler(Class<? extends Book> type) {
        nullName = type.getSimpleName() + " NullBook";
    }

    private class NBook implements Book, Null {
        public String title() {
            return nullName;
        }

        public int count() {
            return 0;
        }

        public double price() {
            return 0.0;
        }

        public double total() {
            return 0.0;
        }
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return method.invoke(proxied, args);
    }
}

public class NullBook {
    public static Book newNullBook(Class<? extends Book> type) {
        return (Book) Proxy.newProxyInstance(Book.class.getClassLoader(), new Class[] { Book.class, Null.class },
                new NullBookProxyHandler(type));
    }

    public static void main(String[] args) {
        Book books[] = { new Novel("Thinking in Java", 108.00, 1), newNullBook(Novel.class) };
        for (Book book : books)
            Book.Test.test(book);
    }
}
```

output:
> title : Thinking in Java  
> count: 1, price: 108.0  
> total price: 108.0  
> Null Book  
> title : Novel NullBook  
> count: 0, price: 0.0  
> total price: 0.0  

### 模拟对象和桩

空对象的逻辑变体是模拟对象和桩，它们都假扮可以传递实际信息的存活对象。模拟对象应用与轻量级和自测试，通常为了处理不同的测试情况被创建;桩返回桩数据，是重量级的，在测试之间被复用。

## 接口与类型的信息

通过反射机制几乎可以访问类的任何成员，无论是private成员还是包访问权限的成员，抑或是内部类，匿名类。

举个简单例程：

```java
import java.lang.reflect.*;

class WithPrivateFinalField {
    private int i = 1;
    private final String s = "I'm totally safe";
    private String s2 = "Am I safe?";
    public String toString() {
        return "i = " + i + ", " + s + ", " + s2;
    }
}

public class ModifyingPrivateFields {
    public static void main(String[] args) throws Exception {
        WithPrivateFinalField pf = new WithPrivateFinalField();
        System.out.println(pf);
        Field f = pf.getClass().getDeclaredField("i");
        f.setAccessible(true);
        System.out.println("f.getInt(pf): " + f.getInt(pf));
        f.setInt(pf, 47);
        System.out.println(pf);

        f = pf.getClass().getDeclaredField("s");
        f.setAccessible(true);
        System.out.println("f.get(pf): " + f.get(pf));
        f.set(pf, "No, you're not!");
        System.out.println(pf);

        f = pf.getClass().getDeclaredField("s2");
        f.setAccessible(true);
        System.out.println("f.get(pf): " + f.get(pf));
        f.set(pf, "No, you're not");
        System.out.println(pf);
    }
}
```

Output:

> i = 1, I'm totally safe, Am I safe?  
> f.getInt(pf): 1  
> i = 47, I'm totally safe, Am I safe?  
> f.get(pf): I'm totally safe  
> i = 47, I'm totally safe, Am I safe?  
> f.get(pf): Am I safe?  
> i = 47, I'm totally safe, No, you're not

不过final域是不能修改的，尽管遭到修改，运行系统会在不抛出异常的情况下接受任何修改的尝试，但是实际上不会发生任何修改
