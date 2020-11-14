---
title: "数组"
date: 2020-05-02T21:27:15+08:00
categories: "Java"
tags: [ "Java"]
---

数组的优势，在于它存储和执行的效率，支持随机访问元素。不过，数组对象使用前它的大小是固定，不可改变。这意味着创建固定大小的数组，以后要扩大其容量是不可能的，在多数情况下ArrayList是更有的选择，在效率和弹性开销的需求方面更令人满意，除非考虑非常高的性能问题，一般减少使用。数组随机访问有可能越界访问，越界会抛出RuntimeException。数组的创建是持有具体类型，不会将持有的对象当作Object看待，通过编译期检查防止插入错误的类型和抽取不当类型。

数组的标识符其实只是引用，所谓的安全指针，指向在堆中创建的一个真实对象，这个数组对象用于保存指向其他对象的引用，它有唯一的域length表示数组的大小，通过"[]"来随机访问数组的元素。

## 一维数组和多维数组

数组类型的声明：

```java
int[] direct; // 一维数组
int[][] flat ; // 二维数组
int[][][] body; // 三维数组
... 以此类推其他多维数组
```

一维数组的打印版，Arrays.toString(数组对象)；二维数组的打印版，Arrays.deepToString(数组对象)。

## 数组和泛型

不能实例化具有参数化类型的数组:

`Peel<Banana>[] peels = new Peel<Banana>[10]; // illegal`

擦除会移除参数类型信息，而数组必须知道它们所持有的确切类型，以强制保证类型安全。

可以通过创建非泛型数组转型为泛型数组, 如: `List< String >[] ls = (List< String >[])new List[10];`，这说明允许声明泛型数组的引用，不支持它的实例化，这里要所在方法定义的开头标注注解 `@SuppressingWarnings("unchecked")` 压制警告信息。

数组是协变类型，List< String > [ ] 也是Object[ ] ，故可以`Object[] objects = ls`，泛型数组可以向上转型为Object[ ]，不建议向上转型，因为如果类型转换有误不会在编译期抛出错误，在运行时类型检查才抛出错误，类型是不安全，泛型数组对Object[ ]来说是类型安全，毕竟持有具体泛型。

现实是泛型容器是比泛型数组更优的选择，泛型会进行编译期检查防止不合理的类型的加入和抽取来保证类型安全，不需要创建时非泛型容器转型为泛型容器。

## Arrays实用功能

以下参数中的数组一词指 *注：数组指基本数据类型数组或Object的数组*

- public static List< T > asList(T[] a) 将数组转变为List，但底层依旧是数组，不能增加或移除元素

### 填充

- public static void fill(数组 a, 数组 val) 只能用一个值进行填充，如果是对象其实是复制同一个对象填充

- public static void fill(数组 a, int fromIndex, int toIndex, Object val) 可以填充某一区域

---

### 复制

System.arraycopy(源数组, 源开始偏移量, 目标数组, 目标开始偏移量, 元素个数) 不支持自动包装和拆箱，两个数组必须具有相同确切的类型

从0开始，复制newLength个元素

- public static 基本数据类型数组 copyOf(基本数据类型数组 original,int newLength)

- public static < T > T[] copyOf(T[] original, int newLength)

- public static < T,U > T[] copyOf(U[] original,int newLength,Class<? extends T[]> newType) U是源数组的元素类型，T是返回数组的元素类型

从from开始，到to范围

- public static 基本数据类型数组 copyOfRange(基本数据类型数组 original, int from, int to)

- public static < T > T[] copyOfRange(T[] original, int from, int to)

- public static < T,U > T[] copyOfRange(U[] original, int from,int to, Class<? extends T[]> newType)

---

### 比较

- public static boolean equals(数组 a,数组 a2)  一维数组的比较，是否等价

- public static boolean deepEquals(Object[] a1, Object[] a2) 多维数组的比较

---

### 排序

- public static < T > void sort(T[] a,Comparator<? super T> c) 自定义类数组的排序

- public static < T > void sort(T[] a,int fromIndex,int toIndex,Comparator<? super T> c) 自定义类数组的排序，针对某一区域

- public static void sort(数组 a) 预定义类型数组的排序

- public static void sort(数组 a,int fromIndex,int toIndex) 预定义类型数组的排序，针对某一区域

---

### 查找

- public static int binarySearch(数组 a, 基本数据类型或Object key)

- public static int binarySearch(数组 a,int fromIndex,int toIndex,基本数据类型或Object key)

- public static < T > int binarySearch(T[] a, T key,Comparator<? super T> c) 自定义的数据类型，先根据所给比较器排序，再整个数组中查找键

- public static < T > int binarySearch(T[] a,int fromIndex,int toIndex, T key,Comparator<? super T> c) 自定义的数据类型，先根据所给比较器排序，再数组的某个区域中查找键

## 比较器

自定义的数据类型可能没实现Comparable泛型接口，数组的元素是该类型时，排序是要调用Comparable的compareTo()方法，所以排序无法进行。但是自建一个比较器Comparator来专门用于此类型的比较，弥补没实现Comparable泛型接口的类型，来用于排序操作。

用例:

```java
class CompType {
    int j;
    // ...
}

class CompTypeComparator implements Comparator<CompType> {
    public int compare(CompType left, CompType right) {
        return left.j < right.j ? -1 : ( left.j == right.j ? 0 : 1);
    }
}
```
