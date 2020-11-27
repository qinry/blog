---
title: "并查集的实现"
date: 2020-07-06T07:34:59+08:00
categories: "Algorithm"
tags : ["Algorithm"]
---

*参考于《算法》第4版第一章第五节*

问题描述：输入一对整数p、q，程序判断它们是否相连，如果相连，忽略这对数并处理下对数；否则将它们相连。p和q相连具有一种等价关系,说明：

1. p和p本身相连；

2. 如果p和q相连，那么q和p也相连；

3. 如果p和q相连且q和r相连，那么p和r也相连。

这个问题是个动态连通性问题，常应用于计算机网络上表示结点的连接，可能需要处理数百万的对象和数十亿的连接。

### 数据结构

解决问题用到了数学抽象的集合，为了简化问题，用整数来表示对象，而不是用整数标识符和任意名称关联表示对象。将p和q是否相连问题转换为p和q是否在同一个集合。

#### 使用术语

对象用*触点*表示，整数对用*连接*表示。等价类，即相连的一系列对象称为*连通分量*，甚至可以为*分量*。

定义抽象数据类型 —— UF（union-find，并查集）

API：

|方法|描述|
|--|--|
|UF(int N)|以整数标识(0~N-1)初始化N个触点|
|void union(int p, int q)|在p和q之间添加一个连接|
|int find(int p)|p所在的分量标识符|
|boolean connected(int p, int q)|p和q是否相连，是返回true|
|int count()|连通分量的数量|

初步实现：

```java
public class UF {
  private int[] id; // 分量id（以触点作为索引）
  private int count; // 分量数量

  public UF(int N) {
    count = N;
    id = new int[N];
    for(int i = 0; i < N; i++)
      id[i] = i;
  }

  public int count() {
    return count;
  }
  public boolean connected(int p, int q) {
    return find(p) == find(q);
  }
  // 以下方法，不同算法，实现各不同
  public int find(int p)
  public void union(int p, int q)

}
```

不同算法实现并查集，性能各不同，因为在数据存放形式和数据的操作上有所不一样，或许是细微的差别。有简单的算法及其改进的算法，如：quick-find算法、quick-union算法、加权quick-union算法、路径压缩的加权quick-union算法。

### quick-find算法

```java
public class UF {
  // ... 其他方法见上的初步实现
  public int find(int p) {
    return id[p];
  }
  public void union(int p, int q) {
    // p和q归并到相同的分量中
    int pID = find(p);
    int qID = find(q);

    // 如果p和q相连，不采取任何动作
    if(pID == qID) return;

    for(int i = 0; i < id.length; i++)
      if(id[i] == pID) id[i] = qID;
    count--;
  }
}
```

分析：find()操作对数组直接操作一次，访问数组id[]一次，速度很快，这个算法无法处理大型问题，由于每一对输入union()都扫描整个数组。find()访问数组操作是常数级别的；union()是线性级别，归并时会调用2次find()，检查id数组的N个元素，还有改变元素的个数在1到N-1，总共访问数组N+3~2N+1之间。（方法主要开销在数组的访问，用数组访问操作作为算法分析的基准）用quick-find算法解决动态连通性问题且最后仅得一个分量，至少调用N-1次union()，那么至少(N+3)(N-1)~N^2次数组访问，说明quick-find解决此问题是平方级别的。

### quick-union算法

```java
public class UF {
  // ... 其他方法见上的初步实现
  public int find(int p) {
    // 找出分量名称
    while(p!=id[p]) p = id[p];
    return p;
  }
  public void union(int p, int q) {
    // 将p和q的根节点统一
    int pRoot = find(p);
    int qRoot = find(q);
    if(pRoot == qRoot) return

    id[pRoot] = qRoot;
    count--;
  }
}
```

分析：数据结构用森林表示，节点与另一个节点的相连为链接，一节点指向其父节点，最终都是朝向根节点，用根节点标识分量，id[]数组是用父链接的形式表示的森林。find()操作从节点顺着链接找到根节点，在最好的情况下，访问数组一次完成操作，最坏也不过是2N+1次访问。该方法访问数组的次数为1加上两倍的所访问节点深度；union()操作假设两节点不在同一个森林，其中一个节点的根节点成为另一个根节点的父节点，接着两个树归并为一树。该方法访问数组次数是find()的两倍。quick-union最坏情况下运行时间是平方级别的，有赖于输入的特性。

### 加权quick-union算法

```java
public class UF {
  private int[] id; // 父链接数组（由触点索引）
  private int[] sz; // （由触点索引）各个根节点所对应的分量的大小
  private int count; // 连通分量的数量
  public UF(int N) {
    count = N;
    id = new id[N];
    for(int i = 0; i < N; i++)
      id[i] = i;
    sz = new sz[N];
    for(int i = ; i < N; i++)
      sz[i] = 1;
  }
  public int find(int p) {
    while(p!= id[p]) p = id[p];
    return p;
  }
  public void union(int p, int q) {
    int i = find(p);
    int j = find(q);
    if(i = j) return;
    // 将小树的根节点连接到大树的根节点
    if(sz[i] < sz[j]){
      id[i] = j;
      sz[j] += sz[i];
    }
    else {
      id[j] = i;
      sz[i] += sz[j];
    }
    count--;
  }
  // 其他方法见初步实现
}
```

分析：记录两棵树的大小，将小树连接到较大的树下，只需增加一个数组记录每个根节点的分量大小。访问节点所在的深度，有N个触点的话，深度最多lgN，最坏的情况下find()、union()的成本的增长数量级为lgN。改进quick-union算法的不足，保证运行时间是对数级别。

### 路径压缩的加权quic-union算法
```java
public class UF {
	private int[] id;
	private int[] sz;
	private int count;

  // 其他方法参考加权quick-union算法和初步实现

	public int find(int p) {
		int root = id[p];
		while(root != id[root]) {
			root = id[root];
		}
		while(root != id[p]) {
			int newp = id[p];
			id[p] = root;
			p = newp;
		}
		return p;
	}

```

分析：在加权quick-union的基础上，再进一步改进，所访问的触点向上找到根节点的同时把检查的所有触点直接与根节点连接，使得树更加扁平，可以说是最优算法了，union()和find()最坏情况下存在N触点的增长数量级非常接近1但未到的程度。
