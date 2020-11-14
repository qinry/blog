---
title: "控制流程"
date: 2019-10-19T20:27:47+08:00
categories: "Java"
tags: [ "java"]
---

**关键词**：**if-else**, **while**, **do-while**, **for**, **return**, **break**, **continue**, **switch**

## 条件判断 (if-else)

判断某个条件是否为真，若为真，则执行 if 后的语句;为假执行else后的语句

例如:

```java
int i = 47;
if (i % 2 == 0)
    System.out.println("The number is even!");
else
    System.out.println("The number is odd!");
```

## while循环

### 进口循环 (while)

先判断条件是否为真才执行循环动作，条件为真就执行代码；直到为假，才停止循环

例如

```java
int i = 0;
while ( i > 0) {
    System.out.println(i--);
}

output:
没有输出!!!
```

### 出口循环 (do-while)

先执行一次代码再判断条件是否为真才继续执行代码，直到假才停止

例如：

```java
int i = 0;
do
{
    System.out.println(i--);
} while(i > 0);

output:
-1
```

注：do-while执行判断的次数比while少一次

## for语句

### 计数器

有三个部分组成：初始变量， 布尔表达式， 变量递增
当表达式为真，执行代码同时递增变量，直到为假停止
for (initializer; boolean-exp; increment )

```java
for (int i = 0; i < 4; i++)
    System.out.println(i);
output:
0
1
2
3
```

### for范围循环

常用于遍历容器元素

```java
int[] f = { 3, 2, 4, 6};
for (int e : f)
    System.out.println(e);

output:
3
2
4
6
```

## 跳转 (break 和 continue)

break 退出循环；continue 停止当前行为，执行进入下个循环

```java
int i = 0;
while (i <= 0) {
    if (i > 100) {
        break; // 当i 大于 100退出循环
    }
    if (i % 9 == 0) {
        i--;
        continue; // 当i 小于 100 跳过循环，执行下个循环
    }
    i += 3;
}
```

## 带标签的break和continue 是Java中的goto

形如：

```java
label1:
outer-iteration {
    inner-iteration {
        // ...
        break;
        // ...
        continue;
        // ...
        continue label1;
        // .. 
        break label1;
    }
}
```

1. 标签和迭代之间最好不要插入任何语句，防止混乱

2. Java中使用标签的理由是因为有循环嵌套存在，而且想从**多层嵌套**中break 或 continue

## 选择（switch）

switch表达式只用于整型表达式，匹配**case**再执行代码，case中没有遇到break,将从上往下一直执行。没有匹配的**case**, 执行**default**

```java
Random rand = new Random(47);
int i = rand.nextInt(3);
switch (i) {
    default:
    case 0: System.out.println(i);
            break;
    case 1: System.out.println(i);
            break;
    case 2: System.out.println(i);
            break;
}
```
