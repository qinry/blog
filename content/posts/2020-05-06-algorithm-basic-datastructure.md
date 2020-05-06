---
title: "背包、栈、队列实现"
date: 2020-05-06T08:24:50+08:00
tags: ["algorithm"]
---

API:

背包

`public class Bag<Item> implements Iterable<Item>`

方法|功能描述
-|:-:
Bag()|创建一个空包
void add(Item item)|添加一个元素
boolean isEmpty()|背包是否为空
int size()|背包中的元素数量

---

先进先出队列(FIFO)

`public class Queue<Item> implements Iterable<Item>`

方法|功能描述
-|:-:
Queue()|创建一个空队列
void enqueue(Item item)|添加一个元素
Item dequeue()|删除最早添加的元素
boolean isEmpty()|队列是否为空
int size()|队列中的元素数量

---
下压栈(LIFO)

`public class Stack<Item> implements Iterable<Item>`

方法|功能描述
-|:-:
Stack()|创建一个空栈
void push(Item item)|添加一个元素
Item pop()|删除最近添加的元素
boolean isEmpty()|栈是否为空
int size()|栈中的元素数量

## 一、栈

### 1.数组实现

#### 1.1定容栈

```java
import java.lang.reflect.Array;
import java.util.Scanner;
import java.util.Iterator;

public class FixedCapacityOfStack<Item> implements Iterable<Item> {
    private Item[] a; // stack entries
    private int N; // size
    @SuppressWarnings("unchecked")
    public FixedCapacityOfStack(Class<Item> type, int size) {
        a = (Item[])Array.newInstance(type, size);  
    }
    @SuppressWarnings("unchecked")
    public FixedCapacityOfStack(int size) {
        a = (Item[])new Object[size];
    }
    public boolean isEmpty() { return N == 0;}
    public int size() { return N; }
    public void push(Item item) {
        a[N++] = item;
    }
    public Item pop() {
        Item item = a[--N];
        a[N] = null;// 防止对象流离
        return item;
    }
    public Iterator<Item> iterator() {
        return new Iterator<Item>() {
            private int i = N;
            public boolean hasNext() {
                return i > 0;
            }
            public Item next()  {
                if(i == 0) throw new NoSuchElementException();
                return a[--i];
            }
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
    public static void main(String[] args) {
        FixedCapacityOfStack<String> s =
            new FixedCapacityOfStack<String>();
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            String item = in.next();
            if(!item.equals("-"))
                s.push(item);
            else if(!s.isEmpty()) System.out.print(s.pop() + " ");
        }
        System.out.println("(" + s.size() + " left on stack)");
    }
}
```

#### 1.2动态调整数组大小的实现

```java
import java.util.Scanner;
import java.util.Iterator;
import java.util.NoSuchElementException;
public class ResizingArrayStack<Item> implements Iterable<Item> {
    @SuppressWarnings("unchecked")
    private Item[] a = (Item[])new Object[1]; // stack entries
    private int N = 0; // size
    @SuppressWarnings("unchecked")
    private void resize(int max) {
        Item[] temp = (Item[])new Object[max];
        for(int i = 0; i < N; i++)
            temp[i] = a[i];
            a = temp;
    }
    public boolean isEmpty() { return N == 0;}
    public int size() { return N; }
    public void push(Item item) {
        if(N == a.length) resize(2*a.length);
        a[N++] = item;
    }
    public Item pop() {
        Item item = a[--N];
        a[N] = null;
        if(N > 0 && N == a.length/4) resize(a.length/2);
        return item;
    }
    public Iterator<Item> iterator() {
        return new Iterator<Item>() {
            private int i = N;
            public boolean hasNext() {
                return i > 0;
            }
            public Item next()  {
                if(i == 0) throw new NoSuchElementException();
                return a[--i];
            }
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
    public static void main(String[] args) {
        ResizingArrayStack<String> s =
            new ResizingArrayStack<String>();
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            String item = in.next();
            if(!item.equals("-"))
                s.push(item);
            else if(!s.isEmpty()) System.out.print(s.pop() + " ");
        }
        System.out.println("(" + s.size() + " left on stack)");
    }
}
```

### 2.链表实现

```java
import java.util.Scanner;
import java.util.Iterator;
import java.util.NoSuchElementException;
public class Stack<Item> implements Iterable<Item> {
    private Node first; // the top of stack
    private int N;// size
    private class Node {
        Item item;
        Node next;
    }
    public boolean isEmpty() {return first == null; }
    public int size() { return N; }
    public void push(Item item) {
        Node oldfirst = first;
        first = new Node();
        first.item = item;
        first.next = oldfirst;
        N++;
    }
    public Item pop() {
        Item item = first.item;
        first = first.next;
        N--;
        return item;
    }
    @Override
    public Iterator<Item> iterator() {
        return new Iterator<Item>() {
            private Node current = first;
            public boolean hasNext() {
                return current != null;
            }
            public Item next() {
                if(current == null) throw new NoSuchElementException();
                Item item = current.item;
                current = current.next;
                return item;
            }
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
    public static void main(String[] args) {
        Stack<String> s = new Stack<String>();
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            String item = in.next();
            if(!item.equals("-"))
                s.push(item);
            else if(!s.isEmpty()) System.out.print(s.pop() + " ");
        }
        System.out.println("(" + s.size() + " left on stack)");
    }
}
```

### 3.Dijkstra的双栈算术表达式求值算法

考虑未省略括号的表达式，以求简单，省略括号的表达式要添加处理相应的优先级规则

```java
import java.util.Scanner;
public class Evaluate {
public static void main(String[] args) {
        Stack<String> ops = new Stack<String>(); // 操作符栈
        Stack<Double> vals = new Stack<Double>(); // 操作数栈
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            // Windows下，扫描完成时ctrl + z，才会返回结果
            // Linux下，扫描完成时ctrl + d, 才返回结果
            String s = in.next();
            if(s.equals("(")) continue;
            else if(s.equals("+"))    ops.push(s);
            else if(s.equals("-"))    ops.push(s);
            else if(s.equals("*"))    ops.push(s);
            else if(s.equals("/"))    ops.push(s);
            else if(s.equals("sqrt")) ops.push(s);
            else if(s.equals(")")) {
                String op = ops.pop();
                double v = vals.pop();
                if(op.equals("+"))         v = vals.pop() + v;
                else if(op.equals("-"))    v = vals.pop() - v;
                else if(op.equals("*"))    v = vals.pop() * v;
                else if(op.equals("/"))    v = vals.pop() / v;
                else if(op.equals("sqrt")) v = Math.sqrt(v);
                vals.push(v);
            }
            else vals.push(Double.parseDouble(s));
        }
       System.out.println(vals.pop());
    }
}

```

## 二、队列

### 1. 动态调整大小的数组实现

```java
import java.util.Iterator;
import java.util.Scanner;
public class ResizingArrayQueue<Item> implements Iterable<Item> {
    @SuppressWarnings("unchecked")
    private Item[] a = (Item[])new Object[1];
    private int head = 0, tail = 0;
    private int N = 0; // size
    @SuppressWarnings("unchecked")
    private void resize(int max) {
        Item[] temp = (Item[])new Object[max];
        int j = head;
        for(int i = 0; i < N; i++, j++) {
            if(j == a.length )
                j = 0;
            temp[i] = a[j];
        }
        a = temp;
        head = 0;
        tail = N - 1;
    }
    public int size() { return N;}
    public boolean isEmpty() { return N == 0; }
    public void enqueue(Item item) {
        if(N == a.length) resize(2 * a.length);
        a[tail++] = item;
        if(tail == a.length)
            tail = 0;
        N++;
    }
    public Item dequeue() {
        Item item = a[head];
        a[head++] = null;// 防止对象琉璃
        if(head == a.length)
            head = 0;
        N--;
        if(N > 0 && N == a.length/4) resize(a.length/2);
        return item;
    }
    public Iterator<Item> iterator() {
        return new Iterator<Item>() {
            private int i = head;
            public boolean hasNext() {
                return i != tail;
            }
            public Item next() {
                if(i == a.length)
                    i = 0;
                return a[i++];
            }
            public void remove() {}
        };
    }
    public static void main(String[] args) throws Exception {
        ResizingArrayQueue<String> q =
            new ResizingArrayQueue<String>();
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            String item = in.next();
            if(!item.equals("-"))
                q.enqueue(item);
            else if(!q.isEmpty()) System.out.print(q.dequeue() + " ");
        }
        System.out.println("(" + q.size() + " left on queue)");
    }
}
```

### 2. 链表实现

```java
import java.util.Scanner;
import java.util.Iterator;
import java.util.NoSuchElementException;
public class Queue<Item> implements Iterable<Item> {
    private Node first;
    private Node last;
    private int N;
    private class Node {
        Item item;
        Node next;
    }
    public boolean isEmpty() { return first == null; }
    public int size() { return N; }
    public void enqueue(Item item) {
        Node oldlast = last;
        last = new Node();
        last.item = item;
        last.next = null;
        if(isEmpty()) first = last;
        else oldlast .next = last;
        N++;
    }
    public Item dequeue() {
        Item item = first.item;
        first = first.next;
        if(isEmpty()) last = null;
        N--;
        return item;
    }
    @Override
    public Iterator<Item> iterator() {
        return new Iterator<Item>() {
            private Node current = first;
            public boolean hasNext() {
                return current != null;
            }
            public Item next() {
                if(current == null)
                    throw new NoSuchElementException();
                Item item = current.item;
                current = current.next;
                return item;
            }
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
    public static void main(String[] args) {
        Queue<String> q = new Queue<String>();
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            String item = in.next();
            if(!item.equals("-"))
                q.enqueue(item);
            else if(!q.isEmpty()) System.out.print(q.dequeue() + " ");
        }
        System.out.println("(" + q.size() + " left on queue)");
    }
}
```

## 三、背包

```java
import java.util.Iterator;
import java.util.NoSuchElementException;
public class Bag<Item> implements Iterable<Item> {
    private Node first;
    private int N;
    private class Node {
        Item item;
        Node next;
    }
    public void add(Item item) {
        Node oldfirst = first;
        first = new Node();
        first.item = item;
        first.next = oldfirst;
        N++;
    }
    public boolean isEmpty() { return first == null; }
    public int size() { return N; };
    @Override
    public Iterator<Item> iterator() {
        return new Iterator<Item>() {
            private Node current = first;
            public boolean hasNext() {
                return current != null;
            }
            public Item next() {
                if(current == null)
                    throw new NoSuchElementException();
                Item item = current.item;
                current = current.next;
                return item;
            }
            public void remove() {
                throw new UnsupportedOperationException();
            }
        };
    }
}
```
