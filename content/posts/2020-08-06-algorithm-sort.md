---
title: "排序"
date: 2020-08-06T08:00:11+08:00
categories: "算法"
tags: ["算法","排序"]
---

*参考于《算法》第四版第二章*

## 排序算法类的模板

```java
public class Example {
  public static void sort(Comparable[] a){
    // ... 见下面详解
  }
  private static boolean less(Comparable v, Comparable w) {
    return v.compareTo(w) < 0;
  }
  private static void exch(Comparable[] a, int i, int j) {
    Comparable t = a[i];a[i] = a[j];a[j] = t;
  }
  private static void show(Comparable[] a) {
    for(int i = 0; i < a.length; i++) {
      System.out.print(a[i] + " ");
    }
    System.out.println();
  }
  public static boolean isSorted(Comparable[] a) {
    for(int i = 1; i < a.length; i++){
      if(less(a[i], a[i-1])) return false;
    }
    return true;
  }
  public static void main(String[] args) {
    String[] a = {"bed", "bug", "dad", "yes", "zoo", "all", "bad", "yet"};
    sort(a);
    assert isSorted(a);
    show(a);
  }
}
```
## 成本模型

排序算法需要计算比较和交换的数量，对于不交换元素的算法则计算访问数组的次数。排序算法可能有额外的内存开销，因此有除了函数调用所需的栈和固定数目的实例变量外无需额外内存的排序算法称为原地排序算法和需要额外内存空间存储另一份数组副本的其他排序算法。

## 选择排序

```java
public class Selection {
  public static void sort(Comparable[] a) {
    int N = a.length;
    for(i = 0; i < N; i++) {
      int min = i;
      for(int j = i+1; j < N; j++)
        if(less(a[j], a[min])) min = j;
      exch(a, i, min);
    }
  }
  // less()、exch()、isSorted()和main()方法见“排序算法类模板”
}
```

对于长度为N的数组，选择排序需要大约N&sup2;/2次比较和N次交换。特点：1、运行时间和输入无关；2、数据移动是最少的。交换次数与数组大小成线性关系。

## 插入排序

```java
public class Insertion {
  public static void sort(Comparable[] a) {
    int N = a.length;
    for(i = 1; i < N; i++) {
      for(int j = i; j >= 1 && less(a[j], a[j-1]); j--)
        exch(a, j, j-1);
    }
  }
  // less()、exch()、isSorted()和main()方法见“排序算法类模板”
}
```

插入排序所需时间取决于输入中元素的初始顺序。对于随机排序的长度为N且主键不重复的数组，平均情况下插入排序需要~N&sup2;/4次比较以及~N&sup2;/4次交换。最坏情况下需要~N&sup2;/2次比较和~N&sup2;/2次交换，最好情况下需要N-1次比较和0次交换。插入排序适合**部分有序的数组**（数组中每个元素离它最终位置不愿、一个有序大数组接一个小数组、数组中只有几个元素的位置不正确）。

#### 改进

内循环中较大的元素都向右移动而不总是交换两个元素（这样访问数组的次数就能减半）
```java
public class InsertionX {
  public static void sort(Comparable[] a) {
		int n = a.length;

		int exchanges = 0;
		// 把最小值移动到左端，同时检查是否已有序
		for(int i = n - 1; i > 0; i--) {
			if(less(a[i], a[i-1])) {
				exch(a, i, i-1);
				exchanges++;
			}
		}
		if(exchanges == 0) return;

		for(int i = 2; i < n; i++) {
			Comparable v = a[i];
			int j = i;
			while(less(v, a[j-1])) {
				a[j] = a[j-1];
				j--;
			}
			a[j] = v;
		}
		assert isSorted(a);
	}
  // 其他方法见Insertion
}
```

## 希尔排序

```java
public class Shell {
  public static void sort(Comparable[] a) {
    int N = a.length;
    int h = 1;
    while(h < N/3) h = 3*h + 1;
    while(h >= 1) {
      for(int i = h; i < N; i++) {
        for(int j = i; j >= h && less(a[j], a[j-h]); j-=h)
          exch(a, j, j-h);
      }
      h = h/3;
    }
  }
  // less()、exch()、isSorted()和main()方法见“排序算法类模板”
}
```

希尔排序是对插入排序的简单改进，加快排序速度，交换不相邻的元素以对数组的局部进行排序，最终用插入排序将局部有序数组排序。其思想先是使数组以任意整数h间隔的元素有序，随着h以某个倍数（这里是3倍）递减至1时，最后一步的排序就为插入排序局部有序数组。

## 归并排序

自顶向下的归并排序
```java
public class Merge {
	private Merge() {}

	private  static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
    // 将a[lo..mid]和a[mid+1..hi]归并

		assert isSorted(a, lo, mid);
		assert isSorted(a, mid+1, hi);

		for(int k = lo; k <= hi; k++) // 将a[lo..hi]复制到aux[lo..hi]
			aux[k] = a[k];

		int i = lo, j = mid + 1;
		for(int k = lo; k <= hi; k++) { // 归并回到a[lo..hi]
			if(i > mid) a[k] = aux[j++];
			else if(j > hi) a[k] = aux[i++];
			else if(less(aux[j], aux[i])) a[k] = aux[j++];
			else a[k] = aux[i++];
		}
		assert isSorted(a, lo, hi);
	}


	private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
		if(hi <= lo) return;
		int mid = lo + (hi - lo)/2;
		sort(a, aux, lo, mid);
		sort(a, aux, mid+1, hi);
		merge(a, aux, lo, mid, hi);
	}

	public static void sort(Comparable[] a) {
		Comparable[] aux = new Comparable[a.length];// 辅助数组
		sort(a, aux, 0, a.length - 1);
		assert isSorted(a);
	}
  // less()、isSorted()和main()方法见“排序算法类模板”
}
```

对于长度为N的任意数组，自顶向下的归并排序需要1/2NlgN至NlgN次比较，最多需要访问数组6NlgN次。


自底向上的归并排序

```java
public class MergeBU {
  // merge()方法代码参见Merge的merge()
  public static void sort(Comparable[] a) {
    int N = a.length;
    Comparable[] aux = new Comparable[N];
    for(int sz = 1; sz < N; sz = sz+sz) // sz:子数组大小
      for(int lo = 0; lo < N-sz; lo += sz+sz) // lo:子数组索引
        merge(a, aux, lo, lo+sz-1, Math.min(lo+sz+sz-1, N-1));
  }
}
```

对于长度为N的任意数组，自底向上的归并排序需要1/2NlgN至NlgN次比较，最多需要访问数组6NlgN次。

#### 改进

1、小规模子数组使用插入排序；2、测试数组是否已有序，有序则跳过`merge()`方法；3、不将重复元素复制到辅助数组，通过在递归调用的每个层次交换输入数组和辅助数组的角色实现。

```java
public class MergeX {
    private static final int CUTOFF = 7;  // cutoff to insertion sort

    private static
    void merge(Comparable[] src, Comparable[] dst, int lo, int mid, int hi) {

	        // precondition:
          // src[lo .. mid] and src[mid+1 .. hi] are sorted subarrays
	        assert isSorted(src, lo, mid);
	        assert isSorted(src, mid+1, hi);

	        int i = lo, j = mid+1;
	        for (int k = lo; k <= hi; k++) {
	            if      (i > mid)              dst[k] = src[j++];
	            else if (j > hi)               dst[k] = src[i++];
	            else if (less(src[j], src[i])) dst[k] = src[j++];   // to ensure stability
	            else                           dst[k] = src[i++];
	        }

	        // postcondition: dst[lo .. hi] is sorted subarray
	        assert isSorted(dst, lo, hi);
	    }

      private static
      void sort(Comparable[] src, Comparable[] dst, int lo, int hi) {
	        // if (hi <= lo) return;
	        if (hi <= lo + CUTOFF) {
	            insertionSort(dst, lo, hi);
	            return;
	        }
	        int mid = lo + (hi - lo) / 2;
	        sort(dst, src, lo, mid);
	        sort(dst, src, mid+1, hi);

	        // if (!less(src[mid+1], src[mid])) {
	        //    for (int i = lo; i <= hi; i++) dst[i] = src[i];
	        //    return;
	        // }

	        // using System.arraycopy() is a bit faster than the above loop
	        if (!less(src[mid+1], src[mid])) {
	            System.arraycopy(src, lo, dst, lo, hi - lo + 1);
	            return;
	        }

	        merge(src, dst, lo, mid, hi);
	    }

	    /**
	     * Rearranges the array in ascending order, using the natural order.
	     * @param a the array to be sorted
	     */
	    public static void sort(Comparable[] a) {
	        Comparable[] aux = a.clone();
	        sort(aux, a, 0, a.length-1);  
	        assert isSorted(a);
	    }

	    // sort from a[lo] to a[hi] using insertion sort
	    private static void insertionSort(Comparable[] a, int lo, int hi) {
	        for (int i = lo; i <= hi; i++)
	            for (int j = i; j > lo && less(a[j], a[j-1]); j--)
	                exch(a, j, j-1);
	    }

      // exch()、isSorted()、less()、show()和main()方法见于“排序算法类模板”
}
```

## 快速排序

```java
import java.util.*;
public class Quick {

  Random random = new Random(System.currentTimeMillis());
  private static int uniform(int n) {
        if (n <= 0) throw new IllegalArgumentException("argument must be positive: " + n);
        return random.nextInt(n);
  }

  private static void shuffle(Comparable[] a) {
    if(a == null) throw new IllegalArgumentException("argument is null");
    int n = a.length;
        for (int i = 0; i < n; i++) {
            int r = i + uniform(n-i);     //  i ~ n-i-1
            Comparable temp = a[i];
            a[i] = a[r];
            a[r] = temp;
        }
  }

  public static void sort(Comparable[] a) {
    shuffle(a); // 消除输入的依赖
    sort(a, 0, a.length -1);
  }
  private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo) return;
    int j = partition(a, lo, hi); // 切分
    sort(a, lo, j-1); // 将左半部分排序
    sort(a, j+1, hi); // 将右半部分排序
  }
  private static int partition(Comparable[] a, int lo, int hi) {
    // 将数组切分为a[lo..i-1], a[i], a[i+1..hi]
    int i = lo, j = hi+1; // 左右扫描指针
    Comparable v = a[lo]; // 切分元素
    while(true) { // 扫描左右，检查扫描是否结束并交换元素
      while (less(a[++i], v)) if(i == hi) break;
      while(less(v, a[--j])) if(j == lo) break;
      if(i >= j) break;
      exch(a, i, j);
    }
    exch(a, lo, j);
    return j;
  }
  // less()、exch()和main()方法见“排序算法类模板”
}
```

将长度为N的无重复数组排序，快速排序平均需要~2NlnN次比较以及1/3NlnN交换，最多需要约N&sup2;/2次比较，但随机打乱数组能预防此情况。注意：1、原地切分；2、别越界；3、保持随机性；4、终止循环；5、处理切分元素值有重复的情况；6、终止递归。性能特点：1、切分方法内循环只进行数组元素比较不进行交换，故一般速度比希尔排序、归并排序快；2、排序效率依赖切分数组的效果，这依赖切分元素的值。

#### 改进
1、对于小数组切换到插入排序；2、三取样切分,使用子数组的一小部分元素的中位数来切分数组；3、熵最优的排序，我的理解是是停止对重复元素的切分的快速排序。

三向切分的快速排序
```java
public class Quick3way{
  private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo) return;
    int lt = lo, i = lo+1, gt = hi;
    Comparable v = a[lo];
    while(i <= gt) {
      int cmp = a[i].compareTo(v);
      if(cmp < 0) exch(a,lt++, i++);
      else if(cmp > 0) exch(a, i, gt--);
      else i++;
    } // a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi]
    sort(a, lo, lt - 1);
    sort(a, gt + 1, hi);
  }
  // 其他方法参见Quick
}
```

改进快速排序的完整代码

```java
public class QuickBentleyMcIlroy {
  	private static final int INSERTION_SORT_CUTOFF = 8;

    // 三取样切分临界值
    private static final int MEDIAN_OF_3_CUTOFF = 40;

    // 该类不能实例化
    private QuickBentleyMcIlroy() { }

    Random random = new Random(System.currentTimeMillis());
    private static int uniform(int n) {
      if (n <= 0) throw new IllegalArgumentException("argument must be positive: " + n);
      return random.nextInt(n);
    }

    private static void shuffle(Comparable[] a) {
      if(a == null) throw new IllegalArgumentException("argument is null");
      int n = a.length;
      for (int i = 0; i < n; i++) {
          int r = i + uniform(n-i);     // between i and n-1
          Comparable temp = a[i];
          a[i] = a[r];
          a[r] = temp;
      }
    }

    public static void sort(Comparable[] a) {
      shuffle(a);
      sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
      int n = hi - lo + 1;

      // 切换到插入排序的临界判断
      if (n <= INSERTION_SORT_CUTOFF) {
          insertionSort(a, lo, hi);
          return;
      }

      // 三取样中位数为切分元素
      else if (n <= MEDIAN_OF_3_CUTOFF) {
          int m = median3(a, lo, lo + n/2, hi);
          exch(a, m, lo);
      }

      // 使用三组中位数的中位数为切分元素
      else  {
          int eps = n/8;
          int mid = lo + n/2;
          int m1 = median3(a, lo, lo + eps, lo + eps + eps);
          int m2 = median3(a, mid - eps, mid, mid + eps);
          int m3 = median3(a, hi - eps - eps, hi - eps, hi);
          int ninther = median3(a, m1, m2, m3);
          exch(a, ninther, lo);
      }

      // 三向切分
      int i = lo, j = hi+1; // 左右扫描指针
      int p = lo, q = hi+1; // 指向与v相等的指针
      Comparable v = a[lo];
      while (true) {
          while (less(a[++i], v))
              if (i == hi) break;
          while (less(v, a[--j]))
              if (j == lo) break;

          // 指针相遇
          if (i == j && eq(a[i], v))
              exch(a, ++p, i);
          if (i >= j) break;

          exch(a, i, j);
          if (eq(a[i], v)) exch(a, ++p, i);
          if (eq(a[j], v)) exch(a, --q, j);
      }


      i = j + 1;
      for (int k = lo; k <= p; k++)
          exch(a, k, j--);
      for (int k = hi; k >= q; k--)
          exch(a, k, i++);

      sort(a, lo, j);
      sort(a, i, hi);
    }


    // a[lo]到a[hi]使用插入排序
    private static void insertionSort(Comparable[] a, int lo, int hi) {
      for (int i = lo; i <= hi; i++)
          for (int j = i; j > lo && less(a[j], a[j-1]); j--)
              exch(a, j, j-1);
    }


    //返回a[i], a[j], and a[k]中位数的索引
    private static int median3(Comparable[] a, int i, int j, int k) {
        return (less(a[i], a[j]) ?
               (less(a[j], a[k]) ? j : less(a[i], a[k]) ? k : i) :
               (less(a[k], a[j]) ? j : less(a[k], a[i]) ? k : i));
    }


    // v < w ?
    private static boolean less(Comparable v, Comparable w) {
        if (v == w) return false;    // optimization when reference equal
        return v.compareTo(w) < 0;
    }

    // v == w ?
    private static boolean eq(Comparable v, Comparable w) {
        if (v == w) return true;    // optimization when reference equal
        return v.compareTo(w) == 0;
    }

    // a[i]与a[j]交换
    private static void exch(Object[] a, int i, int j) {
        Object swap = a[i];
        a[i] = a[j];
        a[j] = swap;
    }


    private static boolean isSorted(Comparable[] a) {
        for (int i = 1; i < a.length; i++)
            if (less(a[i], a[i-1])) return false;
        return true;
    }

    private static void show(Comparable[] a) {
      for(int i = 0; i < a.length; i++) {
        System.out.print(a[i] + " ");
      }
      System.out.println();
    }
}
```

### 堆排序

堆的实现参见[优先队列的实现](../2020-08-06-algorithm-priority-queue)

堆排序算法实现

```java
public class Heap {
	public static void sort(Comparable[] a) {
    // 下沉操作构造堆
		int n = a.length;
		for(int k = n/2; k >= 1; k--)
			sink(a, k, n);
    // 排序
		int k = n;
		while(k > 1) {
			exch(a, 1, k--);
			sink(a, 1, k);
		}
	}

	private static void sink(Comparable[] a, int k, int n) {
		while(2*k <= n) {
			int j = 2*k;
			if(j < n && less(a, j, j+1)) j++;
			if(!less(a, k, j)) break;
			exch(a, k, j);
			k = j;
		}
	}

  private static boolean less(Comparable[] a, int i, int j) {
		return a[i-1].compareTo(a[j-1]) < 0;
	}

  private static void exch(Comparable[] a, int i, int j) {
		Comparable swap = a[i-1];a[i-1] = a[j-1];a[j-1] = swap;
	}
	// isSorted()、show()和main()参见“排序算法类模板”
}

```

将N个元素使用堆排序，只需少于(2NlgN+2N)次比较（以及一半次数的交换）。
