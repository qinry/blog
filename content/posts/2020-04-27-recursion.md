---
title: "简简单单知道递归"
date: 2020-04-27T20:21:23+08:00
draft: true
---

### 递归的含义

递归就是函数或方法通过调用自己本身来达到解决问题的目的，这种解法形式叫递归。用欧几里得算法的Java描述说明问题：

任意一对非负整数p，q(p > q),设r是p与q相除的余数。p，q的最大公约数等于q，p的最大公约数。

Java:

    public static int gcd(int p, int q) {
        if(q == 0) return p;
        int r = p % q;
        return gcd(q, r);
    }

### 为什么使用递归

* 面对某些复杂的问题使用递归可以很好的描述并有效解决，例如汉诺塔问题、二叉树的遍历、查询目录等等。

* 递归使算法实现的代码更加简洁，而且易懂。

### 误区——迭代一定比递归的效率高

当递归使用的是尾递归本质其是就是迭代，效率不亚于普通迭代(不考虑函数的开销，递归比迭代更好)。如果使用使用双递归、三递归等等，才会算法极度低效，这是用之不当所致

### 如何使用递归

使用数学归纳法诠释重要三点：

1. 递归总有一个最简单的情况——方法的第一句总是包含`return`的条件语句

2. 递归调用总是尝试解决一个规模更小的子问题，递归才能收敛到最简单的情况。

3. 递归调用的父问题和尝试解决的子问题之间不应该有交集。

### 一些递归的例子

#### 阶乘

Java:

    public static int fact(int N) {
        if(N == 0) return 1;
        return N * fact(N - 1);
    }
典型的尾递归，效率最高的递归形式

#### 斐波那契数列

Java:

    public static int fibonacci(int N) {
        if(N == 0) return 0;
        if(N == 1) return 1;
        return fibonacci(N - 2) + fibonacci(N - 1);
    }
典型的双递归，子问题有重复了工作，问题有交集，故效率低于迭代形式
迭代如下：
Java:

    public static int fibonacci(int N) {
        int n_2 = 0, n_1 = 1;
        int temp;
        for(int i = 0; i < N; i++) {
            temp = n_2 + n_1;
            n_2 = n_1;
            n_1 = temp;
        }
        return n_2;
    }

#### 底数，指数为非负整数的幂运算

Java:

    public static int pow(int X, int N) {
        if(N == 0) return 1;
        if(N == 1) return X;
        if(N % 2 == 0)
            return pow(X*X, N/2);
        else 
            return pow(X*X, N/2) * X;
    }
根据条件分路解决规模更小子问题，递归调用次数随N递减不呈指数增长,而是线性增长,也是尾递归。

### 扩展

#### 数学归纳法证明欧几里得算法能够计算任意一对非负整数p, q的最大公约数

假设p > q，r为p除以q的余数,引证p,q的最大公约数等于q, r的最大公约数的结论。
证明如下:

(1).简单情况，p能被q整除，最大公约数即q

(2)p不能q整除的情况，由于p,q的最大公约数等于q, r的最大公约数，
令p = q,q=r, r 为新p, q的余数，p, q的最大公约数等于新q，r的最大公约数,以此重复如上p，q值的转换
p和q往p能被q整除的情况收敛，它们的最大公约数一直等于原始p, q的最大公约数。

(3).最后p能被q整除,再令p = q，q = r = 0，p值的为上一次转换的除数，q为余数。
故最终p的值等于原始p, q的最大公约数。
