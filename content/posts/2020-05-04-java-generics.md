---
title: "泛型"
date: 2020-05-03T23:29:33+08:00
tags: ["java"]
---

有些一些方法处理不同类型，这些方法之间只有操作的类型的不同，方法名相同，方法的行为相似甚至相同，那么可以使用参数化类型，也叫泛型，用一个方法就能处理不同类型，大大地减少了代码的重复，提高它的重用性。泛型能使问题的解决对类型能更加宽泛，即使是未来创建的类型，此方法依然运行。

泛型类如何表示类名后跟上尖括号，括号里面就是类型参数。

简单定义泛型：

```java
class Generic<T> {
    public T a;
    public void set(T b) { a = b; }
    public T get() { return a; }
    public void print() { System.out.println(a); }
    public static void main(String[] args) {
        Generic<String> gs = new Generic<String>();
        gs.set("String");
        gs.print();
        Generic<Integer> gi = new Generic<Integer>();
        gs.set(17);
        gs.set(new Integer(47));
        gs.print();
    }
}
```

基本类型不能赋予泛型，所以基本类型对应的包装类来赋予解决基本数据类型的问题

## 泛型方法和泛型接口

有时需要定义类的某些方法需要类型参数，而类不使用类型参数：

```java
class GenericMethod {
    public <T> void f(T t) {
        System.out.println(t.getClass().getName());
    }
}
```

在方法的返回类型前声明参数化类型，加括号里面有类型参数。由于static方法无法访问泛型类的类型参数，也要像泛型方法的定义获得泛型能力。

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

定义泛型接口和泛型类一样

```java
import java.util.*;
interface Generator<T> {
    T next();
}

class StringGenerator implements Generator<String>, Iterable<String> {
    static final String[] array = {
        "张三", "王二", "李四","梁九"
    };
    static final Random rand = new Random(47);
    public String next() {
        return array[rand.nextInt(4)];
    }
    public Iterator<String> iterator() {
        return new Iterator<String>() {
            int count = array.length;
            public boolean hasNext() { return count > 0; }
            public String next() {
                count--;
                return array[count];
            }
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
     public static void main(String[] args) throws Exception {
        StringGenerator sg = new StringGenerator();
        System.out.println(sg.next());
        Iterator<String> it = sg.iterator();
        while(it.hasNext()) {
            String s = it.next();
            System.out.print(s + " ");
        }
        System.out.println();
        for(String s : sg)
            System.out.print(s + " ");
    }
}
```

StringGenerator分别实现自定义泛型接口Generator< T >和内建的泛型接口Iterable< T >。显然有实现自定义数据类型的迭代功能要实现Iterable< T >接口，StringGenerator就可以用于foeach语句。

Arrays有给数组排序的功能，如果是自定义的数据类型数组如何排序，元素类型因无法比较，就不能排大小。可以即将定义的元素类型实现Comparable< T >接口，如果是已定的元素类型，则另外实现一个Comparator< T >接口。*如何实现Comparator< T >见我数组那章笔记*。

```java
import java.util.*;
import java.lang.reflect.*;
class Dog implements Comparable<Dog> {
    public String name;
    public int age;
    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public int compareTo(Dog o) {
        return age < o.age ? -1 : (age == o.age ? 0 : 1);
    }
    public String toString() {
        return "name:" + name + " age:" + age;
    }
    public static void main(String[] args) {
        Dog[] dogs = (Dog[])Array.newInstance(Dog.class, 3);
        dogs[0] = new Dog("狗大", 13);
        dogs[1] = new Dog("狗二", 7);
        dogs[2] = new Dog("狗三", 2);
        System.out.println(Arrays.toString(dogs));
        Arrays.sort(dogs);
        System.out.println(Arrays.toString(dogs));
    }
}
```

## 通配符

无界通配符<?>表示某种特定类型;超类通配符<? super T>或<? super MyClass>,由某个特定类的基类来界定类型参数，它规定类型的下界;
子类通配符<? extends T>或<? extends MyClass>，由某种特定类的子类来界定类型参数，规定了类型的上界。通配符用在方法的泛型类参数或泛型类变量声明上。

就如:

```java
// 假设定义类Fruit及其子类Apple,下面的代码在某个类内部

List<?> fruits = new ArrayList<Fruit>();

void write(List<? super Fruit> fruit, Fruit newFruit) {
    fruit.add(newFruit);
}

Fruit write(List<? extends Fruit> fruit, int i) {
    return fruit.get(i);
}
```

由于擦除的影响，泛型在使用过程中，JVM不知道泛型类内部具体类型参数是什么类型，而是都看成Object。类型参数的变量在内存中是以Object形式存储，内部操作都看成是对Object的操作，这些变量在方法返回或传入时，即在类的外界使用时才会了解具体的类型，那么在方法返回时值进行自动转型。

擦除还影响类型参数不能引用运行时类型操作，如转型、instanceof操作、new表达式等等。

擦除的目的是为了向前兼容，容器类在JavaSE5之前是非泛型版本，元素类型就是Object，帮助之前非泛型类库依旧能正常使用。再强调一遍，泛型类型只有静态类型检查(前面提到的方法的接受参数和返回值)才能发挥它的作用。运行时，内部的类型参数被擦除为Object。通配符就是为了让类型信息擦除非得为Object，可以是你规定的边界。

擦除的补偿，使用类型标签(Class类)补偿，Class类可以很好帮助泛型类内部实现以下操作，如创建泛型数组，`Array.newInstance(Class<?> type, int length)`;动态使用`isInstance(Object)`来代替instanceof静态检查对象是否属于某个类型;动态使用`newInstance()`来代替new表达式创建类型参数的对象。
