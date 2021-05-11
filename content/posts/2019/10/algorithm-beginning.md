---
title: "数据结构与算法分析(笔记) -- 开始"
date: 2019-10-17T23:29:10+08:00
categories: "Algorithm"
Description: "简单说明时间复杂度和时间复杂度的概念；掌握写递归算法的法则，知道大O标记来描述算法时间复杂度，最大子序列和问题的解法，介绍两条简单的算法"
tags : ["Algorithm"]
---

## 递归使用的基本法则

1. 基准情形：要有退出条件
2. 不断推进：从初始条件趋向退出条件
3. 设计准则 ：假设递归准确执行
4. 合成效益法则：防止重复工作，比如生成斐波那契数列的递归实现使用双递归，导致一些工作的重复，进而影响效率

递归符合前三个法则才能正确进行，符合第四法则递归的使用才有意义。虽然递归的代码逻辑清晰，不过某些情况效率不高。当递归不能很好解决问题时，可能考虑采用迭代。

## 算法分析

4种描述算法效率的数学模型，常用的有大O记法

大O记法：时间T(N) = O( f( N ) ), 意思是T( N ) 的相对增长率小于 f(N)

### 计算运行时间

一般法则

1. FOR循环
2. 嵌套的FOR循环
3. 顺序语句
4. IF/ELSE语句

---

#### 最大子序列和问题解

##### 算法1（嵌套3层for循环）

```C
int
MaxSubsequenceSum(const int A[], int N)
{
    int ThisSum, MaxSum, i, j, k;

    MaxSum = 0;
    for (i = 0; i < N; i++)
        for (j = i; j < N; j++)
        {
            ThisSum = 0;
            for (k = i; k <= j; k++)
                ThisSum += A[k];
            if (ThisSum > MaxSum)
                MaxSum = ThisSum;
        }
    return MaxSum;
}
```

##### 算法2（嵌套2层for循环）

```C
int
MaxSubsequenceSum(const int A[], int N)
{
    int ThisSum, MaxSum, i, j;

    MaxSum = 0;
    for (i = 0; i < N; i++)
    {
        ThisSum = 0;
        for (j = i; j < N; j++)
        {
            ThisSum += A[j];
            if (ThisSum > MaxSum)
                MaxSum = ThisSum;
        }
    }
    return MaxSum;
}
```

##### 算法3（分治）

```C
int Max3(int x, int y, int z)
{
    return (x > y
            ? (x > z ? x : z)
            : (y > z ? y : z)
            );
}
static int
MaxSubSum(const int A[], int Left, int Right)
{
    int MaxLeftSum, MaxRightSum;
    int MaxLeftBorderSum, MaxRightBorderSum;
    int LeftBorderSum, RightBorderSum;
    int Centerm i;

    if ( Left == Right )
        if ( A[Left] > 0 )
            return A[ Left ];
        else
            return 0;

    Center = (Left + Right) / 2;
    MaxLeftSum = MaxSubSum( A, Left, Center);
    MaxRightSum = MaxSubSum(A, Center + 1, Right);

    MaxLeftBorderSum = 0;LeftBorderSum = 0;
    for (i = Center; i >= Left; i--)
    {
        LeftBorderSum += A[i];
        if (LeftBorederSum > MaxLeftBorderSun)
            MaxLeftBorderSum = LeftBorderSum;
    }

    MaxRightBorderSum = 0;RightBorderSum = 0;
    for (i = Center + 1; i < Right; i++)
    {
        RightBorderSum += A[i];
        if (RightBorderSum > MaxRightBorderSum)
            MaxRightBorderSum = RightBorderSum;
    }
    return Max3(MaxLeftSum, MaxRightSum,
            MaxLeftBorderSum + MaxRightBorderSum);
}
int
MaxSubsquenceSum(const int A[], int N)
{
    return MaxSubSum(A, 0, N - 1);
}
```

##### 算法4(联机算法)

```C
int
MaxSubsquenceSum(const int A[], int N)
{
    int ThisSum, MaxSum, i;

    ThisSum = MaxSum = 0;
    for (i = 0; i < N; i++)
    {
        ThisSum += A[i];

        if (ThisSum > MaxSum)
            MaxSum = ThisSum;
        else if (ThisSum < 0)
            ThisSum = 0;
    }
    return MaxSum;
}
```

#### 欧几里得算法

```C
int gcd(int M, int N)
{
    int Rem;
    while ( N == 0 )
    {
        Rem = M % N;
        M = N;
        N = Rem;
    }
    return M;
}
```

#### 求幂运算

```C
_Bool IsEven(int N)
{
    if (N % 2)
        return 0;
    return 1;
}
int Pow(int X, int N)
{
    if (N == 0)
        return 1;
    if (N == 1)
        return X;
    if IsEven(N)
        return Pow(X * X, N / 2);
    else
        return Pow(X * X, N / 2) * X;
}
```
