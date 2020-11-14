---
title: "字符串"
date: 2019-11-25T09:46:16+08:00
categories: "Java"
tags: [ "Java"]
---

## String对象不可变

String对象被创建后不可修改，某些方法看似是修改源字符串，其实是重新创建新字符串来代替。

## StringBuilder

在循环体中对String对象反复使用 "+=", "+" 运算符，以达字符串的拼接,其实效率不高。因为"+","+="工作实际上虚拟机会自动创建StringBuilder来起到拼接效果，每次循环建立一个StringBuilder,这将导致资源的浪费。可以在循环体外创建一个StringBuilder，每次调用append()方法拼接字符串，最后toString()返回字符串。如下:

```Java
import java.util.*;
public class Mango {
    public static void main(String[] args) {
        Random rand = new Random(47);
        StringBuilder result = new StringBuilder("[");
        for (int i = 0; i < 10; i++) {
            result.append(rand.nextInt(100));
            result.appen(", ");
        }
        result.delete(result.length() - 2, result.length());
        result.append("]");
        String s = result.toString();
        System.out.println(s);
    }
}
```

## String的常用方法

以下方法改变内容返回的String都是新的String, 没有改变内容返回的String都是原始字符串的引用

```Java
构造器
String(String)
String(StringBuilder)
String(StringBuffer)
String(char[])
String(byte[])

长度
int length()

查找字符
char charAt(int)

复制
char[] getChars(int, int, char[], int) // 参数:起始和终止索引, 目标数组，目标数组的起始索引
byte[] getBytes(int, int, byte[], int)

生成Char数组
char[] toCharArray()

比较
boolean equals(String) // 内容上的比较是否相等
boolean equalsIgnoreCase(String) // 忽略大小写比较内容是否相等
int compareTo(String) // 大小写不等价
区域比较
boolean regionMatches(int, String, int, int) // 参数:索引，模式字符串，串的偏移量， 比较长度
开头比较
boolean startsWith(String)
boolean startsWith(String, int)  // 参数: 模式字符串，串的偏移量
末尾比较
boolean endsWith(String)
boolean endsWith(String, int)
包含
boolean contains(CharSequence)
取索引
// 从头到尾搜索 失败返回 -1
int indexOf(String)
int indexOf(char)
int indexOf(char, int) // 参数: 字符, 起始索引
int indexOf(String, int)  // 参数: 字符串， 起始索引
// 从尾向前搜索 失败返归 -1
int lastIndexOf(String)
int lastIndexOf(char)
int lastIndexOf(char, int)
int lastIndexOf(String, int)



子字符串
String subString(int) // 参数:起始索引
String subString(int, int) // 参数: 起始和终止索引

拼接
String concat(String)

替换
String replace(char, char)
String replace(CharSequence, CharSequence)

大小写转换
String toLowerCase()
String toUpperCase()

转变为字符串
static String valueOf(Object|char|boolean|int|long|float|double)
static String valueOf(char[])
static String valueOf(char[], int, int) // 数组， 数组偏移量， 个数

去两端空白
String trim()

为字符序列生成String唯一引用
String intern()

```

## 格式化输出

System.out.printf() 与 C的printf类似
System.out.format() 也是如此

### 格式化说明符

%[argument_index$][flags][width][.precision]conversion
注：其中[.precision]不能用于整数类型，否则触发异常

### Formatter类

使用:

```Java
在某个方法内
String name = "Jack";
int age = 12;
Formatter f = new Formatter(System.out);
f.format("name: %s, age: %d", name, age);
```

解释: Formatter类会将format()内的参数打印到System.out

String.format方法是静态方法，内部创建Formatter对象，将传入的参数，传给
Formatter。

## 扫描输入

使用java.io.Scanner，扫描输入， 减少分割字符串操作
例子:

```Java
import java.io.*;

public class ScanRead {
    public static BufferedReader input = new BufferedReader(
        new StringReader("Thingking In Java\n2019 3.14")
    );
    public static void main(String[] args) {
        Scanner in = new Scanner(input);
        System.out.println("What book do you read?");
        String book = in.nextLine();
        System.out.println(book);
        System.out.println("What year is it");
        int year = in.nextInt();
        System.out.println(year);
        System.out.println("How much pi do you know?");
        double pi = in.nextDouble();
        System.out.println("yes , pi = " + pi);
    }
}

```
