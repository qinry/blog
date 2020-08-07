---
title: "优先队列的实现"
date: 2020-08-06T21:36:55+08:00
tags: ["algorithm"]
---

*参考于《算法》第四版第二章第四节*

## 基于堆的优先队列

API:
最大优先队列

`public class MaxPQ<Key extends Comparable<Key>>`

方法|功能描述
-|:-:
MaxPQ(int max)|创建一个初始容量为max的优先队列
void insert(Key x)|向优先队列插入一个元素
Key delMax()|删除最大元素并返回最大元素
boolean isEmpty()|返回队列是否为空
int size()|返回优先队列的元素个数

```java
public class MaxPQ<Key extends Comparable<Key>> {
    private Key[] pq;                    // 基于堆的完全二叉树
    private int N = 0;                   // 存储于pq[1..N]中，pq[0]没有使用

    public MaxPQ(int maxN) {
        pq = (Key[]) new Comparable[maxN + 1];
    }

    public boolean isEmpty() {
        return n == 0;
    }

    public int size() {
        return N;
    }

    public Key max() {
        if (isEmpty()) throw new NoSuchElementException("Priority queue underflow");
        return pq[1];
    }

    public void insert(Key x) {

        pq[++n] = x;
        swim(n);
    }

    public Key delMax() {
        Key max = pq[1]; // 从根结点得到最大元素
        exch(1, N--); // 将其和最后一个结点交换
        pq[n+1] = null; //防止对象游离
        sink(1); // 恢复堆的有序性
        return max;
    }

    // 辅助方法
    private void swim(int k) {
        while (k > 1 && less(k/2, k)) {
            exch(k, k/2);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= n) {
            int j = 2*k;
            if (j < n && less(j, j+1)) j++;
            if (!less(k, j)) break;
            exch(k, j);
            k = j;
        }
    }

    private boolean less(int i, int j) {
      return pq[i].compareTo(pq[j]) < 0;
    }

    private void exch(int i, int j) {
        Key swap = pq[i];
        pq[i] = pq[j];
        pq[j] = swap;
    }

}

```

最小优先队列
API:

`public class MinPQ<Key extends Comparable<Key>>`

方法|功能描述
-|:-:
MinPQ(int max)|创建一个初始容量为max的优先队列
void insert(Key x)|向优先队列插入一个元素
Key delMin()|删除最小元素并返回最小元素
boolean isEmpty()|返回队列是否为空
int size()|返回优先队列的元素个数

```java
public class MinPQ<Key extends Comparable<Key>> {
	 private Key[] pq;
   private int N=0;

    public MinPQ(int initCapacity) {
        pq = (Key[]) new Comparable[initCapacity + 1];
    }

    public Key delMin() {
        Key min = pq[1];
        exch(1, N--);
        pq[N+1] = null;     
        sink(1);
        return min;
    }

    private void swim(int k) {
        while (k > 1 && greater(k/2, k)) {
            exch(k, k/2);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= n) {
            int j = 2*k;
            if (j < n && greater(j, j+1)) j++;
            if (!greater(k, j)) break;
            exch(k, j);
            k = j;
        }
    }
    private boolean greater(int i, int j) {
      return (pq[i]).compareTo(pq[j]) > 0;
    }


    // isEmpty()、size()、insert()、exch()于MaxPQ的方法一样
}
```
索引最大优先队列
API:

`public class IndexMinPQ<Item extends Comparable<Item>>`

方法|功能描述
-|:-:
IndexMinPQ(int maxN)|创建一个初始容量为max的优先队列
void insert(int k, Item item)|插入一个元素，将它和索引k相关联
void change(int k, Item item)|将索引k的元素设备item
boolean contains(int k)|是否存在索引为k的元素
void delete(int k)|删去索引k及其相关联的元素
Item min()|返回最小元素
int minIndex()|返回最小元素的索引
int delMin()|删除最大元素并返回它的索引
boolean isEmpty()|返回队列是否为空
int size()|返回优先队列的元素个数

```java
public class IndexMinPQ<Item extends Comparable<Item>>{
	private int N; // PQ中元素数量
	private int[] pq; // 索引二叉堆，由1开始
	private int[] qp; // 逆序：qp[pq[i]] = pq[qp[i]] = i
	private Item[] items; // 有优先级之分的元素
	public IndexMinPQ(int maxN) {
		items = (Item[])new Comparable[maxN + 1];
		pq = new int[maxN + 1];
		qp = new int[maxN + 1];
		for(int i = 0; i <= maxN; i++) qp[i] = -1;
	}

	public boolean isEmpty() {
		return N == 0;
	}

	public int size() {
		return N;
	}

	public boolean contains(int k) {
		return qp[k] != -1;
	}

	public void insert(int k, Item item) {
		N++;
		qp[k] = N;
		pq[N] = k;
	  items[i] = item;
		swim(N);
	}

	public Item min() {
		return items[pq[1]];
	}

	public int delMin() {
		int indexOfMin = pq[1];
		exch(1, N--);
		sink(1);
		items[pq[N+1]] = null;
		qp[pq[N+1]] = -1;
		return indexOfMin;
	}

	public int minIndex() {
		return pq[1];
	}

	public void change(int k, Item item) {
		items[k] = item;
    swim(qp[k]);
    sink(qp[k]);
	}


	public void delete(int k) {
		int index = qp[k];
		exch(index, N--);
		swim(index);
		sink(index);
		items[k] = null;
		qp[k] = -1;
	}

	private void swim(int k) {
		while(k > 1 && greater(k/2, k)) {
			exch(k, k/2);
			k = k/2;
		}
	}

	private void sink(int k) {
		while(2*k <= n) {
			int j = 2*k;
			if(j < n && greater(j, j + 1)) j++;
			if(!greater(k, j)) break;
			exch(k, j);
			k = j;
		}
	}

	private boolean greater(int i, int j) {
		return items[pq[i]].compareTo(items[pq[j]]) > 0;
	}

	private void exch(int i, int j) {
		int swap = pq[i];
		pq[i] = pq[j];
		pq[j] = swap;
		qp[pq[i]] = i;
		qp[pq[j]] = j;
	}
```

索引最大优先队列
API:

`public class IndexMaxPQ<Item extends Comparable<Item>>`

方法|功能描述
-|:-:
IndexMaxPQ(int maxN)|创建一个初始容量为max的优先队列
void insert(int k, Item item)|插入一个元素，将它和索引k相关联
void change(int k, Item item)|将索引k的元素设备item
boolean contains(int k)|是否存在索引为k的元素
void delete(int k)|删去索引k及其相关联的元素
Item max()|返回最小元素
int maxIndex()|返回最小元素的索引
int delMax()|删除最大元素并返回它的索引
boolean isEmpty()|返回队列是否为空
int size()|返回优先队列的元素个数

```java
public class IndexMaxPQ<Item extends Comparable<Item>> {
	private int N;
	private int[] pq;
	private int[] qp;  
	private Item[] items;

	public IndexMaxPQ(int maxN) {
		items = (Item[])new Comparable[maxN + 1];
		pq = new int[maxN + 1];
		qp = new int[maxN + 1];
		for(int i = 0; i <= maxN; i++)
			qp[i] = -1;
	}

	public int maxIndex() {
		return pq[1];
	}

	public Item max()) {
		return items[pq[1]];
	}

	public int delMax() {
		int indexMax = pq[1];
		exch(1, N--);
		sink(1);
		items[pq[N+1]] = null;
		qp[pq[N+1]] =  -1;
		return indexMax;
	}

  private void swim(int k) {
      while (k > 1 && less(k/2, k)) {
          exch(k, k/2);
          k = k/2;
      }
  }

  private void sink(int k) {
      while (2*k <= n) {
          int j = 2*k;
          if (j < n && less(j, j+1)) j++;
          if (!less(k, j)) break;
          exch(k, j);
          k = j;
      }
  }

	private boolean less(int i, int j) {
		return items[pq[i]].compareTo(keys[pq[j]]) < 0;
	}

  // insert()、change()、contains()、delete()、isEmpty()、size()、和exch()与IndexMinPQ的一样
}
```
