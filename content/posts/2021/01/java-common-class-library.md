---
title: "Java常用类库"
date: 2021-01-31T17:08:58+08:00
categories: "Java"
description: "一些常用类库的使用，如日期和时间，字符串处理，数组处理等"
tags : ["Java"]
---

文章目录

*   [一、java.util.Objects](#%E4%B8%80javautilobjects)

    *   [1.1 检查对象相等](#11-%E6%A3%80%E6%9F%A5%E5%AF%B9%E8%B1%A1%E7%9B%B8%E7%AD%89)

        *   [1.1.1 使用equals](#111-%E4%BD%BF%E7%94%A8equals)
        *   [1.1.2 equals源码](#112-equals%E6%BA%90%E7%A0%81)

        *   [1.2 检查空对象](#12-%E6%A3%80%E6%9F%A5%E7%A9%BA%E5%AF%B9%E8%B1%A1)

            *   [1.2.1 使用isNull和nonNull](#121-%E4%BD%BF%E7%94%A8isnull%E5%92%8Cnonnull)
        *   [1.2.2 isNull和nonNull源码](#122-isnull%E5%92%8Cnonnull%E6%BA%90%E7%A0%81)
        *   [1.2.3 使用requirenonNull](#123-%E4%BD%BF%E7%94%A8requirenonnull)
        *   [1.2.4 requireNonNull源码](#124-requirenonnull%E6%BA%90%E7%A0%81)

*   [二、 java.lang.Math](#%E4%BA%8Cjavalangmath)

    *   [2.1 求绝对值](#21-%E6%B1%82%E7%BB%9D%E5%AF%B9%E5%80%BC)
    *   [2.2 求两数的最大值和最小值](#22-%E6%B1%82%E4%B8%A4%E6%95%B0%E7%9A%84%E6%9C%80%E5%A4%A7%E5%80%BC%E5%92%8C%E6%9C%80%E5%B0%8F%E5%80%BC)
    *   [2.3 浮点数的四舍五入](#23-%E6%B5%AE%E7%82%B9%E6%95%B0%E7%9A%84%E5%9B%9B%E8%88%8D%E4%BA%94%E5%85%A5)
    *   [2.4 向下取整](#24-%E5%90%91%E4%B8%8B%E5%8F%96%E6%95%B4)
    *   [2.5 向上取整](#25-%E5%90%91%E4%B8%8A%E5%8F%96%E6%95%B4)

*   [三、java.utils.Arrays](#%E4%B8%89javautilsarrays)

    *   [3.1 数组内容的字符串表示](#31-%E6%95%B0%E7%BB%84%E5%86%85%E5%AE%B9%E7%9A%84%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%A1%A8%E7%A4%BA)
    *   [3.2 排序](#32-%E6%8E%92%E5%BA%8F)
    *   [3.3 查找](#33-%E6%9F%A5%E6%89%BE)
    *   [3.4 拷贝数组](#34-%E6%8B%B7%E8%B4%9D%E6%95%B0%E7%BB%84)
    *   [3.5 数组的比较](#35-%E6%95%B0%E7%BB%84%E7%9A%84%E6%AF%94%E8%BE%83)

*   [四、java.math.BigDecimal](#%E5%9B%9Bjavamathbigdecimal)
*   [五、日期和时间](#%E4%BA%94%E6%97%A5%E6%9C%9F%E5%92%8C%E6%97%B6%E9%97%B4)

    *   [5.1 java.util.Date](#51-javautildate)

        *   [5.1.1 创建日期](#511-%E5%88%9B%E5%BB%BA%E6%97%A5%E6%9C%9F)
        *   [5.1.2 获取时间戳](#512-%E8%8E%B7%E5%8F%96%E6%97%B6%E9%97%B4%E6%88%B3)
        *   [5.1.3 比较时间](#513-%E6%AF%94%E8%BE%83%E6%97%B6%E9%97%B4)

        *   [5.2 java.text.DateFormat](#52-javatextdateformat)

            *   [5.2.1 格式化日期字符串表示](#521-%E6%A0%BC%E5%BC%8F%E5%8C%96%E6%97%A5%E6%9C%9F%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%A1%A8%E7%A4%BA)
        *   [5.2.2 解析日期字符串表示](#522-%E8%A7%A3%E6%9E%90%E6%97%A5%E6%9C%9F%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%A1%A8%E7%A4%BA)
        *   [5.2.3 日期和时间的模式](#523-%E6%97%A5%E6%9C%9F%E5%92%8C%E6%97%B6%E9%97%B4%E7%9A%84%E6%A8%A1%E5%BC%8F)

        *   [5.3 java.util.Calendar](#53-javautilcalendar)

*   [六、java.lang.System](#%E5%85%ADjavalangsystem)

    *   [6.1 System.gc](#61-systemgc)
    *   [6.2 System.exit](#62-systemexit)
    *   [6.3 System.arraycopy](#63-systemarraycopy)

*   [七、字符串](#%E4%B8%83%E5%AD%97%E7%AC%A6%E4%B8%B2)

    *   [7.1 java.lang.String](#71-javalangstring)

        *   [7.1.1 String的创建](#711-string%E7%9A%84%E5%88%9B%E5%BB%BA)
        *   [7.1.2 常见方法](#712-%E5%B8%B8%E8%A7%81%E6%96%B9%E6%B3%95)

        *   [7.2 java.lang.StringBuilder](#72-javalangstringbuilder)
    *   [7.2 java.lang.StringBuffer](#72-javalangstringbuffer)

一些常用类库的使用，没有列出完整代码，只有只有代码的主要部分

### 一、java.util.Objects

java.lang.Objects是JDK 7之后提供的工具类，里面包含丰富的static方法，用于操作对象或操作前的检查某些条件。比如检查两个对象是否相等，计算对象的哈希值，检查对象是否为空，以及检查索引或子范围是否超出等等。

#### 1.1 检查对象相等

##### 1.1.1 使用equals

例如：

```java
String s1 = null;
String s2 = "123";
String s3 = "456";
// 由于s1为空，调用equals方法会引发NullPointerException
System.out.println(s1.equals(s2));
System.out.println(Objects.equals(s1, s2));
System.out.println(Objects.equals(s2, s3));
```

输出：

```java
false
false
```

比较两个对象是否相等，最好使用Objects的equals方法，防止NullPointerException异常。

##### 1.1.2 equals源码

```java
public static boolean equals(Object a, Object b) {
    return (a == b) || (a == null && a.equals(b));
}
```

a和b如果内存位置相同直接返回true，a和b相等检测前，对a进行了空的检查。

#### 1.2 检查空对象

##### 1.2.1 使用isNull和nonNull

例如：

```java
String s1 = null;
String s2 = "123";
String s3 = "456";

System.out.println(Objects.isNull(s1));
System.out.println(Objects.nonNull(s1));
```

输出：

```java
true
false
```

方法名可以知道意思,isNull判断对象是否为空，nonNull判断对象是否为非空。对空对象来说,isNull返回true，nonNull返回false；非空对象，反之。

##### 1.2.2 isNull和nonNull源码

```java
public static boolean isNull(Object obj) {
    return obj == null;
}

public static boolean nonNull(Object obj) {
    return obj != null;
}
```
isNull是对象与null的==比较，而nonNull是用!=比较

##### 1.2.3 使用requirenonNull

例如：

```java
String s1 = null;

System.out.println(Objects.requireNonNull(s1));
```

输出：

```java
Exception in thread "main" java.lang.NullPointerException
	at java.base/java.util.Objects.requireNonNull(Objects.java:221)
	at com.github.qinry.utils.UseObjects.main(UseObjects.java:18)
```

与isNull和nonNull都是检测空对象，但是如果对象是空的，requireNonNull会实例化NullPointerException对象抛出异常。常常用在某些方法的流程中，要求传入的参数须具备有值，可以在调用该方法前，使用requireNonNull判断是否为空，来保证数据有效，若为空中断方法的调用，须做异常的处理。

##### 1.2.4 requireNonNull源码

```java
public static <T> T requireNonNull(T obj) {
    if (obj == null)
        throw new NullPointerException();
    return obj;
}
```

如果对象为空，抛出异常NullPointerException；否则直接返回原对象。

### 二、java.lang.Math

java.lang.Math是java提供的基本数学库，用于一些基本的数字运算，例如：幂运算，求对数，平方根和三角函数等等。下面展示简单的应用。

#### 2.1 求绝对值

例如：

```java
System.out.println(Math.abs(-47));
System.out.println(Math.abs(47));
```

输出：

```java
47
47
```

abs除了求int类型的绝对值，还有三个重载的版本（long、float、double）

#### 2.2 求两数的最大值和最小值

例如：

```java
System.out.println(Math.max(38, 47));
System.out.println(Math.min(-2, 3));
```

输出：

```java
47
-2
```

min和max除了可以比较int的最大值和最小值，还有三个重载的版本（long、float、double）

#### 2.3 浮点数的四舍五入

有float和double两个版本

例如：

```java
System.out.println(Math.round(3.5));
```

输出：

```java
4
```

#### 2.4 向下取整

只有double版本

例如：

```java
// 返回小于参数的最大整数
System.out.println(Math.floor(3.5));
System.out.println(Math.floor(-3.5));
```

输出：

```java
3.0
-4.0
```

#### 2.5 向上取整

只有double版本

例如：

```java
// 返回大于参数的最小整数
System.out.println(Math.ceil(3.5));
System.out.println(Math.ceil(-3.5));
```

输出：

```java
4.0
-3.0
```

2.6 随机数

Math.random()得到0~1（不包含）的双精度浮点数

得到0到n（包含）的随机整数，用法：

```java
(int)(Math.random() * (n+1));
```

得到i到n（不包含）的随机整数（i &lt; n），用法：

```java
i + (int)(Math.random()*(n-i));
```

举个例子，取1000~9999得随机数：

```java
int num = 1000 + (int)(Math.random()*(10000-1000));
```

### 三、java.utils.Arrays

java.utils.Arrays是Java提供操作数组得工具类，如排序，查找，两数组比较，拷贝数组，数组内容字符串表示等等。

#### 3.1 数组内容的字符串表示

toString返回指定数组内容的字符串表示，有基本数据类型数组的版本和Object[]版本

例如：

```java
int[] a = { 3, 8, 7, 2, 6, 4 };
System.out.println(a);
System.out.println(Arrays.toString(a));
```

输出：

```java
[I@7c30a502
[3, 8, 7, 2, 6, 4]
```

直接打印数组引用a，输出的不是a的内容，而是数组类型和地址，`[I`表示整型数组类型，@后面的16进制整数是数组对象的起始地址。toString才是打印数组的内容。

toString源码：

```java
public static String toString(int[] a) {
    if (a == null)
        return "null";
    int iMax = a.length - 1;
    if (iMax == -1)
        return "[]";

    StringBuilder b = new StringBuilder();
    b.append('[');
    for (int i = 0; ; i++) {
        b.append(a[i]);
        if (i == iMax)
            return b.append(']').toString();
        b.append(", ");
    }
}
```

使用了StringBuilder类，遍历数组每一个元素，把元素拼接为一个字符串，最后返回。

#### 3.2 排序

sort对数组排序，有基本数据类型数组（除了boolean[]），Object[]类型和泛型数组类型版本。泛型数组的排序要额外传入Comparator参数，Comparator是Java定义好的一个接口，功能是用于指定对象类型比较大小的比较器。

例如：

```java
int[] a = { 3, 8, 7, 2, 6, 4 };
System.out.println(Arrays.toString(a));
Arrays.sort(a);
System.out.println(Arrays.toString(a));
```

输出：

```java
[3, 8, 7, 2, 6, 4]
[2, 3, 4, 6, 7, 8]
```

排序的效率高于冒泡排序，sort根据数组的情况选择一些更加高效的排序，例如：插入排序、快速排序、归并排序。

#### 3.3 查找

binarySearch，顾名思义，就是折半查找，亦或称二分查找。需要传入数组a和要查找的目标关键字key。有基本数据类型数组（除了boolean[]），Object[]类型和泛型数组类型版本。泛型数组的查找要额外传入Comparator参数。使用binarySearch先要把排序好数组。

例如：

```java
int[] a = { 3, 8, 7, 2, 6, 4};
Arrays.sort(a);
System.out.println(Arrays.binarySearch(a, 4));
```

输出：

```java
2
```

#### 3.4 拷贝数组

copyOf是从源数组的下标0开始拷贝数组，需传入源数组引用和返回拷贝数组的长度两个参数，返回值是拷贝数组。有基本数组类型数组和泛型数组类型版本。若拷贝数组长度大于源数组，会用默认值填充；若小于，则截断源数组后面的内容。copyOf的返回值赋值给原来的数组引用，常常起到数组尺寸的动态调整的作用。

例如扩容：

```java
int[] a = { 3, 8, 7, 2, 6, 4};
System.out.println(a.length);
System.out.println(Arrays.toString(a));

a = Arrays.copyOf(a, a.length*2);

System.out.println(a.length);
System.out.println(Arrays.toString(a));
```

输出：

```java
6
[3, 8, 7, 2, 6, 4]
12
[3, 8, 7, 2, 6, 4, 0, 0, 0, 0, 0, 0]
```

拷贝数组长度大于源数组，拷贝数组后面全部填充了默认值

例如减容：

```java
int[] a = { 3, 8, 7, 2, 6, 4};
System.out.println(a.length);
System.out.println(Arrays.toString(a));

a = Arrays.copyOf(a, a.length/2);

System.out.println(a.length);
System.out.println(Arrays.toString(a));
```

输出：

```java
6
[3, 8, 7, 2, 6, 4]
3
[3, 8, 7]
```

拷贝数组长度小于源数组，拷贝数组截断后面内容，全部丢弃

#### 3.5 数组的比较

compare用于比较数组，传入需要比较的两个数组引用参数。有基本数据类型数组和泛型数组版本。泛型数组版本有2个，一个是实现Comparable接口的泛型数组版本，另一个没有实现Comparable接口的泛型数组版本。实现了Comparable接口的导出类，允具备比较大小的能力。Comparator接口则是用来弥补没有实现Comparable接口的导出类不能比较大小的能力。

compare返回值，若为-1，表示左数组小于右数组；若为0，数组相等；若为1，左数组大于右数组；

```java
int[] a = { 3, 8, 7, 2, 6, 4};
int[] b = new int[a.length*2];
System.arraycopy(a, 0, b, 0, a.length);
Arrays.sort(a);
System.out.println(Arrays.compare(a, b));
```

输出：

```java
-1
```

System.arraycopy见下节的[6.3 System.arraycopy](#63-systemarraycopy)

compare源码：

```java
public static int compare(int[] a, int[] b) {
    if (a == b)
        return 0;
    if (a == null || b == null)
        return a == null ? -1 : 1;

    int i = ArraysSupport.mismatch(a, b,
                                   Math.min(a.length, b.length));
    if (i >= 0) {
        return Integer.compare(a[i], b[i]);
    }

    return a.length - b.length;
}
```

1.  如果数组a和b，内存位置相同或者都为空，返回0；
2.  a和b其中一个是空，若a为空，返回-1，若b为空，返回1；
3.  获取数组之间第一个相等不匹配的相对索引。

    *   索引大于0，说明存在这个索引， 返回比较第一个不匹配的元素的比较结果
*   索引小于0，说明不存在此索引，返回数组长度的比较结果

### 四、java.math.BigDecimal

double和float的算数运算是不精确的，为了解决精确计算，java.math包提供了BigDecimal则用来精确的浮点数运算。

常用方法有加(add)、减(substract)、乘(mutiply)、除(divide)，和转换为int(intValue)、long(longValue)、float(floatValue)、double(doubleValue)和BigInteger(toBigInteger)的方法。

还有获取绝对值的方法abs，与同类型的val求两数最大值的max，与同类型的val求两数最小值的min。

创建BigDecimal，可以使用构造方法和传入long或double参数的静态valueOf。常用的构造方法是传入字符串参数的构造方法,即BigDecimal(String val)。创建的BigDecimal对象是不可变的，可以认为是常量，所以像b1.add(b2)，不会改变b1和b2，而是会返回BigDecimal的结果。

例如：

```java
BigDecimal b1 = new BigDecimal("0.1");
BigDecimal b2 = new BigDecimal("0.2");
BigDecimal b3 = null;

System.out.println(b1);
System.out.println(b2);

b3 = b1.add(b2);
System.out.println(b3);

b3 = b1.subtract(b2);
System.out.println(b3);

b3 = b1.multiply(b2);
System.out.println(b3);

b3 = b1.divide(b2);
System.out.println(b3);

double d = b3.doubleValue();
float f = b3.floatValue();
int i = b3.intValue();
long l = b3.longValue();

System.out.println(d+"\n"+i);
System.out.println(f+"\n"+l);
```

输出：

```java
0.1
0.2
0.3
-0.1
0.02
0.5
0.5
0.5
0
0
```

### 五、日期和时间

#### 5.1 java.util.Date

##### 5.1.1 创建日期

创建当前的日期：

```java
Date date = new Date();
System.out.println(date);
```

输出：

```java
Mon Jan 25 21:27:14 CST 2021
```

创建指定的日期：

```java
Date date = new Date(1611581144493L);
System.out.println(date);
```

输出：

```java
Mon Jan 25 21:25:44 CST 2021
```

Date有两个常用的构造器：一个无参构造器和一个传入long类型的时间戳变量的构造器。无参构造器构造的是当前时间的Date对象。时间戳是指1970年1月1日（格林尼治时间是00:00，东八区时间是8:00）到指定日期时间的所经历的毫秒数。通过时间戳也可以创建Date对象表示时间

##### 5.1.2 获取时间戳

例如：

```java
Date date = new Date();
System.out.println(date.getTime());
```

输出：

```java
1611581744578
```

通过时间戳可以计算，两时间点所经历的时间长度。例如：

```java
Date old = new Date(1611581144493L);
Date date = new Date();
System.out.println((date.getTime()-old.getTime())/1000/60+"分钟");
```

输出：

```java
14分钟
```

##### 5.1.3 比较时间

date是否为old之后的日期时间，可以用curr.after(old)来判断；date是否为old之前的日期时间，可以用curr.before(old)来判断。

例如：

```java
Date old = new Date(1611581144493L);
Date curr = new Date();
System.out.println(curr.after(old));
System.out.println(curr.before(old));
```

输出：

```java
true
false
```

#### 5.2 java.text.DateFormat

Date对象默认输出的格式，例如：Mon Jan 25 21:25:44 CST 2021。Java提供了java.text.DateFormat接口，可以自定义日期的字符串表示, 实现这个接口的类有SimpleDateFormat。

##### 5.2.1 格式化日期字符串表示

例如：

```java
SimpleDateFormat format = new SimpleDateFormat("yyyy年MM月dd日 HH:mm ss");
String text = format.format(new Date());
System.out.println(text);
```

输出：

```java
2021年01月25日 21:51 18
```

##### 5.2.2 解析日期字符串表示

```java
String s = "1999年01月22日 05:50 30";
Date date = format.parse(s);
System.out.println(date + " ~ " +date.getTime());
```

输出：

```java
Fri Jan 22 05:50:30 CST 1999 ~ 916955430000
```

##### 5.2.3 日期和时间的模式

有些可以充当转义为日期和时间的特殊字符，如下：
![picture](../../../../images/posts/2021/01/java-common-class-library/1.png)

#### 5.3 java.util.Calendar

Calendar是个抽象类，不能直接new获得，但提供了静态方法getInstance()获取Calendar的子类对象。提供了getTime方法获取Date对象。

get方法用来获取日历中日期某一字段的信息，像年份、月份、当月几号等等，这些字段信息是储存在int数组fields中，为了方便检索日期某一字段的信息,Calendar内定义许多int常量，比如Calendar.YEAR，这些常量在设置日历，对日历做运算都有要使用，有了它们就不用记住哪个下标的元素代表哪个字段的信息，若是要获得年份可以get(Calendar.YEAR)，要获取其他信息只要使用定义好的常量就行。注意get方法得到月份的信息，是0到11，0表示1月，真正月份是得到信息再加1.

set方法设置日历某一字段，像set(Calendar.MONTH, Calendar.FEBRUARY)把日历设置成2月。set(Calendar.DAY_OF_WEEK, Calendar.SUNDAY)，可以把日期的改为这个星期的星期天。注意一周的第一天是周日。

add方法可以按照指定时间量添加或减少给定的日历字段，像add(Calendar.YEAR, 1)把时间加了一年，add(Calendar.MONTH, -1)把时间减了一个月。

获取某一日历字段的最大值用getActualMaximun方法，可以解决某年有多少天，某月有多少天，某年有多少周，某月有多少周的问题。

例如：

```java
Calendar cl = Calendar.getInstance();
Date date = cl.getTime();
System.out.println(date);

int year = cl.get(Calendar.YEAR);
int month = cl.get(Calendar.MONTH);
System.out.println(year);
System.out.println(month+1);

System.out.println(cl.getActualMaximum(Calendar.DAY_OF_MONTH));
System.out.println(cl.getActualMaximum(Calendar.DAY_OF_YEAR));
System.out.println(cl.getActualMaximum(Calendar.WEEK_OF_YEAR));
System.out.println(cl.getActualMaximum(Calendar.WEEK_OF_MONTH));

cl.set(Calendar.DAY_OF_WEEK, Calendar.SUNDAY);
System.out.println(cl.getTime());

cl.set(Calendar.MONTH, Calendar.FEBRUARY);
System.out.println(cl.get(Calendar.MONTH)+1);


cl.add(Calendar.YEAR, -1);
System.out.println(cl.get(Calendar.YEAR));
```

输出：

```java
Mon Jan 25 22:35:35 CST 2021
2021
1
31
365
52
6
Sun Jan 24 22:35:35 CST 2021
2
2020
```

### 六、java.lang.System

#### 6.1 System.gc

运行垃圾回收器

#### 6.2 System.exit

终止当前运行的Java虚拟机，此方法要传入状态码参数，状态码是int类型，0表示正常，其他数表示非正常。

#### 6.3 System.arraycopy

其实System也提供了数组拷贝的方法arraycopy。它是native方法。按顺序传入的参数为，源数组的引用src，源数组被拷贝的开始位置srcPos，目标数组的引用dest，拷贝到目标数组的开始位置destPos，拷贝的长度length。

如果目标数组引用就是源数组的引用，它甚至可以做到数组子序列在数组中的整体移动。System.arraycopy不像Arrays.copyOf具有填充和截断的操作。

srcPos+length大于源数组长度，或destPos+length大于目标数组长度，或desPos、srcPos和length任意一个为负数,会引发异常ArrayIndexOutOfBoundsException;src或dest为null，会引发NullPointerException;src和dest的基元类型不同,或者任意一个不是数组类型，或者一个数组的元素是基本数类型，另一个数组的元素是引用类型，引发ArrayStoreException；假设k小于length的非负整数，src[srcPos+k]无法转换类目标数组元素类型，也引发ArrayStoreException，注意srcPos到srcPos+k-1的部分已经被复制到目标数组，目标数组其他位置不会修改，

Arrays.copyOf拷贝肯定是从0开始的，而System.arraycopy是指定拷贝的位置，它的可操作空间更大。

例如:

```java
int[] a = { 3, 8, 7, 2, 6, 4};
int[] b = new int[a.length*2];
System.arraycopy(a, 0, b, 0, a.length);
System.out.println(Arrays.toString(a));
System.out.println(Arrays.toString(b));
```

输出:

```java
[3, 8, 7, 2, 6, 4]
[3, 8, 7, 2, 6, 4, 0, 0, 0, 0, 0, 0]
```

### 七、字符串

String、StringBuilder和StringBuffer都实现了CharSequence和Comparable接口。

#### 7.1 java.lang.String

String表示字符串常量，是不可变的。

##### 7.1.1 String的创建

字符串最常用的创建方式，使用双引号包起一段文字。第一次出现的字符串，才会创建并保存在字符串常量池（在运行时常量池内）。可以赋值给String引用，如：`String s = &quot;hello&quot;;`

第二次出现相同的字符串赋值给新的字符串引用，是不会重复创建，而是共享了第一次创建的字符串的内存地址。不过，把这个字符串传入构造器，使用new表达式是会在内存中重新创建字符串。

例如：

```java
String s1 = "123";
String s2 = "123";
String s3 = new String("123");
System.out.println(s1 == s2);
System.out.println(s1 == s3);
```

输出：

```java
true
false
```

s1和s2引用的同一个字符串，内存地址相同。凡是new表达式创建的对象，一定会在内存分配了空间，所以s1和s3的地址不同。

##### 7.1.2 常见方法

构造方法：

```java
String(byte[] bytes) 通过使用平台的默认字符集解码指定的字节数组构造新的 String 。 
String(byte[] bytes, String charsetName) 构造一个新的String由指定用指定的字节的数组解码charset 。  
String(char[] value) 分配新的 String ，使其表示当前包含在字符数组参数中的字符序列。
String(String original) 初始化新创建的String对象，使其表示与参数相同的字符序列; 换句话说，新创建的字符串是参数字符串的副本。
```

静态方法:

```java
static String format(String format, Object... args) 使用指定的格式字符串和参数返回格式化字符串。
static String copyValueOf(char[] data) 相当于 valueOf(char[]) 。  
static String copyValueOf(char[] data, int offset, int count) 相当于 valueOf(char[], int, int) 。  
static String join(CharSequence delimiter, CharSequence... elements) 返回由 CharSequence elements的副本组成的新String，该副本与指定的 delimiter的副本连接在一起。  
static String join(CharSequence delimiter, Iterable<? extends CharSequence> elements) 返回由 String的副本组成的新 String ，其中 CharSequence elements指定的 delimiter的副本。  
static String valueOf(boolean b) 返回 boolean参数的字符串表示形式。  
static String valueOf(char c) 返回 char参数的字符串表示形式。  
static String valueOf(char[] data) 返回 char数组参数的字符串表示形式。  
static String valueOf(char[] data, int offset, int count) 返回 char数组参数的特定子数组的字符串表示形式。  
static String valueOf(double d) 返回 double参数的字符串表示形式。  
static String valueOf(float f) 返回 float参数的字符串表示形式。  
static String valueOf(int i) 返回 int参数的字符串表示形式。  
static String valueOf(long l) 返回 long参数的字符串表示形式。  
static String valueOf(Object obj) 返回 Object参数的字符串表示形式。
```

非静态方法：

```java
int length() 返回此字符串的长度。  
char charAt(int index)                         返回指定索引处的 char值。 
int codePoints(int index)                      返回指定索引处的字符（Unicode代码点）。 
int offsetByCodePoints(int index, int codePointOffset) 返回此 String中的索引，该索引从给定的 index偏移 codePointOffset的代码点。    
```

```java
boolean startsWith(String prefix)              测试此字符串是否以指定的前缀开头。
boolean startsWith(String prefix, int toffset) 测试从指定索引开始的此字符串的子字符串是否以指定的前缀开头。 
boolean endsWith(String suffix)                测试此字符串是否以指定的后缀结尾。
int compareTo(String anotherString)            按字典顺序比较两个字符串。 
int compareToIngnoreCase(String str)           按字典顺序比较两个字符串，忽略大小写差异。
boolean contains(CharSequence s)               当且仅当此字符串包含指定的char值序列时，才返回true。 
boolean contentEquals(CharSequence cs)         将此字符串与指定的 CharSequence 内容比较。 
boolean contentEquals(StringBuffer sb)         将此字符串与指定的 StringBuffer 内容比较。
boolean equals(Object anObject)                将此字符串与指定的对象进行比较。
boolean equalsIgnoreCase(String anotherString) 将此 String与另一个 String比较，忽略了大小写。   
```

```java
byte[] getBytes() 使用平台的默认字符集将此 String编码为字节序列，将结果存储到新的字节数组中。  
byte[] getBytes(String charsetName) 使用命名的字符集将此 String编码为字节序列，将结果存储到新的字节数组中。
void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin) 
int indexOf(int ch) 返回指定字符第一次出现的字符串中的索引。  
int indexOf(int ch, int fromIndex) 返回指定字符第一次出现的此字符串中的索引，从指定索引处开始搜索。 
int indexOf(String str) 返回指定子字符串第一次出现的字符串中的索引。 
int indexOf(String str, int fromIndex) 从指定的索引处开始，返回指定子字符串第一次出现的字符串中的索引。  
int lastIndexOf(int ch) 返回指定字符最后一次出现的字符串中的索引。  
int lastIndexOf(int ch, int fromIndex) 返回指定字符最后一次出现的字符串中的索引，从指定的索引开始向后搜索。  
int lastIndexOf(String str) 返回指定子字符串最后一次出现的字符串中的索引。  
int lastIndexOf(String str, int fromIndex) 返回指定子字符串最后一次出现的字符串中的索引，从指定索引开始向后搜索。  
String intern() 返回字符串对象的规范表示。与此字符串具有相同内容的字符串，但保证来自唯一字符串池。 
boolean isBlank() 如果字符串为空或仅包含 white space代码点，则返回 true ，否则 false 。  
boolean isEmpty() 当且仅当length()是 0 ，返回 true。
boolean matches(String regex) 判断此字符串是否与给定的 regular expression匹配。  
boolean regionMatches(int toffset, String other, int ooffset, int len) 测试两个字符串区域是否相等。
boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len) 测试两个字符串区域是否相等。
```

```java
String repeat(int count) 返回一个字符串，其值为此字符串的串联重复 count次。  
String replace(char oldChar, char newChar) 返回从替换所有出现的导致一个字符串 oldChar在此字符串 newChar 。  
String replace(CharSequence target, CharSequence replacement) 将此字符串中与文字目标序列匹配的每个子字符串替换为指定的文字替换序列。  
String replaceAll(String regex, String replacement) 将给定替换的、给定 regular expression匹配的此字符串的每个子字符串替换。  
String replaceFirst(String regex, String replacement) 将给定替换的、给定 regular expression匹配的此字符串的第一个子字符串替换。  
String[] split(String regex) 将此字符串拆分为给定 regular expression的匹配 项 。  
String[] split(String regex, int limit) 将此字符串拆分为给定 regular expression的匹配 项 。  
String strip() 返回一个字符串，其值为此字符串，并删除了所有前导和尾随 white space 。 
String stripLeading() 返回一个字符串，其值为此字符串，并删除了所有前导 white space 。  
String stripTrailing() 返回一个字符串，其值为此字符串，并删除所有尾随 white space 。 
```

```java
CharSequence subSequence(int beginIndex, int endIndex) 返回作为此序列的子序列的字符序列。  
String substring(int beginIndex) 返回一个字符串，该字符串是此字符串的子字符串。  
String substring(int beginIndex, int endIndex) 返回一个字符串，该字符串是此字符串的子字符串。  
char[] toCharArray() 将此字符串转换为新的字符数组。  
String toLowerCase() 使用默认语言环境的规则将此 String所有字符转换为小写。  
String toUpperCase() 使用默认语言环境的规则将此 String所有字符转换为大写。  
String trim() 返回一个字符串，其值为此字符串，删除了所有前导和尾随空格，其中space被定义为其代码点小于或等于 'U+0020' （空格字符）的任何字符。  
```

#### 7.2 java.lang.StringBuilder

可认为是线程不安全的字符串变量，拼接字符串更加高效。

```java
StringBuilder sb = new StringBuilder();
for (int i=0; i<10; i++) {
    sb.append(i);
}
String s = sb.toString();
System.out.println(s);
```

输出：

```java
123456789
```

常用方法：

构造方法：

```java
StringBuilder() 构造一个字符串构建器，其中不包含任何字符，初始容量为16个字符。  
StringBuilder(String str) 构造一个初始化为指定字符串内容的字符串构建器。
```

非静态方法：

```java
int length() 返回长度（字符数）。  
char charAt(int index) 返回指定索引处的此序列中的 char值。  
int codePointAt(int index) 返回指定索引处的字符（Unicode代码点）。  
int codePointBefore(int index) 返回指定索引之前的字符（Unicode代码点）。  
int codePointCount(int beginIndex, int endIndex) 返回此序列的指定文本范围内的Unicode代码点数。 
int compareTo(StringBuilder another) StringBuilder字典顺序比较两个 StringBuilder实例。 
int offsetByCodePoints(int index, int codePointOffset) 返回此序列中的索引，该索引从给定的 index偏移 codePointOffset代码点。 
StringBuilder replace(int start, int end, String str) 使用指定的 String的字符替换此序列的子字符串中的字符。  
StringBuilder reverse() 导致此字符序列被序列的反向替换。  

StringBuilder append(boolean b) 将 boolean参数的字符串表示形式追加到序列中。  
StringBuilder append(char c) 将 char参数的字符串表示形式追加到此序列。  
StringBuilder append(char[] str) 将 char数组参数的字符串表示形式追加到此序列。  
StringBuilder append(char[] str, int offset, int len) 将 char数组参数的子数组的字符串表示形式追加到此序列。  
StringBuilder append(double d) 将 double参数的字符串表示形式追加到此序列。  
StringBuilder append(float f) 将 float参数的字符串表示形式追加到此序列。  
StringBuilder append(int i) 将 int参数的字符串表示形式追加到此序列。  
StringBuilder append(long lng) 将 long参数的字符串表示形式追加到此序列。  
StringBuilder append(CharSequence s) 将指定的字符序列追加到此 Appendable 。  
StringBuilder append(CharSequence s, int start, int end) 将指定的 CharSequence序列追加到此序列。  
StringBuilder append(Object obj) 追加 Object参数的字符串表示形式。  
StringBuilder append(String str) 将指定的字符串追加到此字符序列。  
StringBuilder append(StringBuffer sb) 将指定的 StringBuffer追加到此序列。  
StringBuilder appendCodePoint(int codePoint) 将 codePoint参数的字符串表示形式追加到此序列。  

StringBuilder delete(int start, int end) 删除此序列的子字符串中的字符。  
StringBuilder deleteCharAt(int index) 按此顺序删除指定位置的 char 。  
void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin) 将字符从此序列复制到目标字符数组 dst 。  
int indexOf(String str) 返回指定子字符串第一次出现的字符串中的索引。  
int indexOf(String str, int fromIndex) 从指定的索引处开始，返回指定子字符串第一次出现的字符串中的索引。 
int lastIndexOf(String str) 返回指定子字符串最后一次出现的字符串中的索引。  
int lastIndexOf(String str, int fromIndex) 返回指定子字符串最后一次出现的字符串中的索引，从指定索引开始向后搜索。 

StringBuilder insert(int offset, boolean b) 将 boolean参数的字符串表示形式插入此序列中。  
StringBuilder insert(int offset, char c) 将 char参数的字符串表示形式插入此序列中。  
StringBuilder insert(int offset, char[] str) 将 char数组参数的字符串表示形式插入此序列中。  
StringBuilder insert(int index, char[] str, int offset, int len) 将str数组参数的子数组的字符串表示形式插入此序列中。 
StringBuilder insert(int offset, double d) 将 double参数的字符串表示形式插入此序列中。  
StringBuilder insert(int offset, float f) 将 float参数的字符串表示形式插入此序列中。  
StringBuilder insert(int offset, int i) 将第二个 int参数的字符串表示形式插入此序列中。  
StringBuilder insert(int offset, long l) 将 long参数的字符串表示形式插入此序列中。  
StringBuilder insert(int dstOffset, CharSequence s) 将指定的 CharSequence插入此序列。  
StringBuilder insert(int dstOffset, CharSequence s, int start, int end) 将指定的 CharSequence序列插入此序列。  
StringBuilder insert(int offset, Object obj) 将 Object参数的字符串表示形式插入此字符序列中。  
StringBuilder insert(int offset, String str) 将字符串插入此字符序列。  

void setCharAt(int index, char ch) 指定索引处的字符设置为 ch 。  
void setLength(int newLength) 设置字符序列的长度。  
CharSequence subSequence(int start, int end) 返回一个新的字符序列，它是该序列的子序列。  
String substring(int start) 返回一个新的 String ，其中包含此字符序列中当前包含的字符的子序列。  
String substring(int start, int end) 返回一个新的 String ，其中包含当前包含在此序列中的字符的子序列。  
void trimToSize() 尝试减少用于字符序列的存储空间。 
```

#### 7.2 java.lang.StringBuffer

用于StringBuffer类似，但可认为线程安全的字符串变量，里面很多方法用synchronized修饰，表示同步方法，多个线程操作StringBuffer对象，是不会同时进行，字符串处理是互斥的。

常用方法：

构造器：

```java
StringBuffer() 构造一个字符串缓冲区，其中没有字符，初始容量为16个字符。  
StringBuffer(String str) 构造一个初始化为指定字符串内容的字符串缓冲区。  
```

非静态方法：

```java
char charAt(int index) 返回指定索引处的此序列中的 char值。  
int codePointAt(int index) 返回指定索引处的字符（Unicode代码点）。  
int codePointBefore(int index) 返回指定索引之前的字符（Unicode代码点）。  
int codePointCount(int beginIndex, int endIndex) 返回此序列的指定文本范围内的Unicode代码点数。  
int offsetByCodePoints(int index, int codePointOffset) 返回此序列中的索引，该索引从给定的 index偏移 codePointOffset代码点。 
StringBuffer replace(int start, int end, String str) 使用指定的 String的字符替换此序列的子字符串中的字符。  
StringBuffer reverse() 导致此字符序列被序列的反向替换。  

StringBuffer append(boolean b) 将 boolean参数的字符串表示形式追加到序列中。  
StringBuffer append(char c) 将 char参数的字符串表示形式追加到此序列。  
StringBuffer append(char[] str) 将 char数组参数的字符串表示形式追加到此序列。  
StringBuffer append(char[] str, int offset, int len) 将 char数组参数的子数组的字符串表示形式追加到此序列。  
StringBuffer append(double d) 将 double参数的字符串表示形式追加到此序列。  
StringBuffer append(float f) 将 float参数的字符串表示形式追加到此序列。  
StringBuffer append(int i) 将 int参数的字符串表示形式追加到此序列。  
StringBuffer append(long lng) 将 long参数的字符串表示形式追加到此序列。  
StringBuffer append(CharSequence s) 将指定的 CharSequence追加到此序列。  
StringBuffer append(CharSequence s, int start, int end) 将指定的 CharSequence序列附加到此序列。  
StringBuffer append(Object obj) 追加 Object参数的字符串表示形式。  
StringBuffer append(String str) 将指定的字符串追加到此字符序列。  
StringBuffer append(StringBuffer sb) 将指定的 StringBuffer追加到此序列。  
StringBuffer appendCodePoint(int codePoint) 将 codePoint参数的字符串表示形式追加到此序列。  

int compareTo(StringBuffer another) StringBuffer字典顺序比较两个 StringBuffer实例。  
StringBuffer delete(int start, int end) 删除此序列的子字符串中的字符。  
StringBuffer deleteCharAt(int index) 按此顺序删除指定位置的 char 。  
void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin) 将字符从此序列复制到目标字符数组 dst 。  
int indexOf(String str) 返回指定子字符串第一次出现的字符串中的索引。  
int indexOf(String str, int fromIndex) 从指定的索引处开始，返回指定子字符串第一次出现的字符串中的索引。  
int lastIndexOf(String str) 返回指定子字符串最后一次出现的字符串中的索引。  
int lastIndexOf(String str, int fromIndex) 返回指定子字符串最后一次出现的字符串中的索引，从指定索引开始向后搜索。  

StringBuffer insert(int offset, boolean b) 将 boolean参数的字符串表示形式插入此序列中。  
StringBuffer insert(int offset, char c) 将 char参数的字符串表示形式插入此序列中。  
StringBuffer insert(int offset, char[] str) 将 char数组参数的字符串表示形式插入此序列中。  
StringBuffer insert(int index, char[] str, int offset, int len) 将 str数组参数的子数组的字符串表示形式插入此序列中。 
StringBuffer insert(int offset, double d) 将 double参数的字符串表示形式插入此序列中。  
StringBuffer insert(int offset, float f) 将 float参数的字符串表示形式插入此序列中。  
StringBuffer insert(int offset, int i) 将第二个 int参数的字符串表示形式插入到此序列中。  
StringBuffer insert(int offset, long l) 将 long参数的字符串表示形式插入此序列中。  
StringBuffer insert(int dstOffset, CharSequence s) 将指定的 CharSequence插入此序列。  
StringBuffer insert(int dstOffset, CharSequence s, int start, int end) 将指定的 CharSequence序列插入此序列。  
StringBuffer insert(int offset, Object obj) 将 Object参数的字符串表示形式插入此字符序列。  
StringBuffer insert(int offset, String str) 将字符串插入此字符序列。  

void setCharAt(int index, char ch) 指定索引处的字符设置为 ch 。  
void setLength(int newLength) 设置字符序列的长度。  
CharSequence subSequence(int start, int end) 返回一个新的字符序列，它是该序列的子序列。  
String substring(int start) 返回一个新的 String ，其中包含此字符序列中当前包含的字符的子序列。  
String substring(int start, int end) 返回一个新的 String ，其中包含当前包含在此序列中的字符的子序列。  
void trimToSize() 尝试减少用于字符序列的存储空间。  
```