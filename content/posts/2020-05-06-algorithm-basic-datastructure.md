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

## 运用例子

### Dijkstra的双栈算术表达式求值算法

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

### 中序表达式转为后序表达式

#### 不考虑优先级

```java
import java.util.Scanner;
public class InfixToPostfix {
    public static void main(String[] args) {
        Stack<String> stack = new Stack<String>();
        Scanner stdin = new Scanner(System.in );
        while (stdin.hasNext()) {
            String s = stdin.next();
            if      (s.equals("+")) stack.push(s);
            else if (s.equals("*")) stack.push(s);
            else if (s.equals(")")) System.out.print(stack.pop() + " ");
            else if (s.equals("(")) System.out.print("");
            else System.out.print(s + " ");
        }
        System.out.println();
    }
}
```

#### 考虑优先级

```java
import java.util.Scanner;
import java.util.TreeMap;
public class InfixToPostfixWithPrecedence {
    public static void main(String[] args) {
        Scanner stdin = new Scanner(System.in);
        Stack<String> stack = new Stack<String>();

        TreeMap<String, Integer> precedence = new TreeMap<String, Integer>();
        precedence.put("(", 0);
        precedence.put(")", 0);
        precedence.put("+", 1);
        precedence.put("-", 1);
        precedence.put("*", 2);
        precedence.put("/", 2);

        while(stdin.hasNext()) {
            String s = stdin.next();
            if(!precedence.containsKey(s)) {
                System.out.print(s + " ");
                continue;
            }
            while(true) {
                if(stack.isEmpty() || s.equals("(")) {
                    stack.push(s);
                    break;
                } else if(s.equals(")")) {
                    System.out.print(stack.pop() + " ");
                    if(stack.peek().equals("(")) {
                        stack.pop();
                        break;
                    }
                } else if(precedence.get(s) > precedence.get(stack.peek())) {
                        stack.push(s);
                        break;
                } else {
                        System.out.print(stack.pop() + " ");
                }
            }
        }
    }
    while(!stack.isEmpty())
    System.out.print(stack.pop() + " ");
    stdin.close();
    }
}
```

### 求后序表达式值

```java
import java.util.Scanner;
public class EvaluatePostfix {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<Integer>();
        Scanner stdin = new Scanner(System.in );
        while (stdin.hasNext()) {
            String s = stdin.next();
            if(s.equals("+")) {
                int val = stack.pop();
                stack.push(stack.pop() + val);
            } else if (s.equals("*")) {
                int val = stack.pop();
                stack.push(stack.pop() * val);
            } else if(s.equals("-")) {
                int val = stack.pop();
                stack.push(stack.pop() - val);
            } else if(s.equals("/")) {
                int val = stack.pop();
                stack.push(stack.pop() / val);
            }
            else stack.push(Integer.parseInt(s));
        }
        StdOut.println(stack.pop());
    }
}
```

### 带有优先级的中序表达式求值

```java
import java.util.Scanner;
import java.util.TreeMap;
public class EvaluateDeluxe {
    public static double eval(String op, double val1, double val2) {
        if (op.equals("+")) return val1 + val2;
        if (op.equals("-")) return val1 - val2;
        if (op.equals("/")) return val1 / val2;
        if (op.equals("*")) return val1 * val2;
        throw new RuntimeException("Invalid operator");
    }

    public static void main(String[] args) {
        TreeMap<String, Integer> precedence = new TreeMap<String, Integer>();
        precedence.put("(", 0);
        precedence.put(")", 0);  
        precedence.put("+", 1);
        precedence.put("-", 1);
        precedence.put("*", 2);
        precedence.put("/", 2);

        Stack<String> ops  = new Stack<String>();
        Stack<Double> vals = new Stack<Double>();

        Scanner stdin = new Scanner(System.in);
        while (stdin.hasNext()) {

            String s = stdin.next();

            // 处理数字
            if (!precedence.containsKey(s)) {
                vals.push(Double.parseDouble(s));
                continue;
            }

            // 处理符号
            while (true) {
                if (ops.isEmpty() || s.equals("(") || (precedence.get(s) > precedence.get(ops.peek()))) {
                    ops.push(s);
                    break;
                }

                // 计算表达式
                String op = ops.pop();

                if (op.equals("(")) {
                    assert s.equals(")");
                    break;
                }

                else {
                    double val2 = vals.pop();
                    double val1 = vals.pop();
                    vals.push(eval(op, val1, val2));
                }
            }
        }

        while (!ops.isEmpty()) {
            String op = ops.pop();
            double val2 = vals.pop();
            double val1 = vals.pop();
            vals.push(eval(op, val1, val2));
        }

        System.out.println(vals.pop());
        assert vals.isEmpty();
        assert ops.isEmpty();
    }
}
```

### 约瑟夫问题

```java
public class Josephus {
    public static void main(String[] args) {
        int arg1 = Integer.parseInt(args[0]);
        int arg2 = Integer.parseInt(args[1]);
        Queue<Integer> q = new Queue<Integer>();
        for(int i = 0; i < arg1; i++) {
            q.enqueue(i);
        }

        while(!q.isEmpty()) {
            for(int i = 1; i < arg2; i++)
                q.enqueue(q.dequeue());
            System.out.print(q.dequeue() + " ");
        }
    }
}

```

### 平衡括号

```java
import java.util.Scanner;
public class Parentheses {
    private static final char LEFT_PAREN     = '(';
    private static final char RIGHT_PAREN    = ')';
    private static final char LEFT_BRACE     = '{';
    private static final char RIGHT_BRACE    = '}';
    private static final char LEFT_BRACKET   = '[';
    private static final char RIGHT_BRACKET  = ']';

    public static boolean isBalanced(String s) {
        Stack<Character> stack = new Stack<Character>();
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == LEFT_PAREN)   stack.push(LEFT_PAREN);
            if (s.charAt(i) == LEFT_BRACE)   stack.push(LEFT_BRACE);
            if (s.charAt(i) == LEFT_BRACKET) stack.push(LEFT_BRACKET);

            if (s.charAt(i) == RIGHT_PAREN) {
                if (stack.isEmpty())           return false;
                if (stack.pop() != LEFT_PAREN) return false;
            }

            else if (s.charAt(i) == RIGHT_BRACE) {
                if (stack.isEmpty())           return false;
                if (stack.pop() != LEFT_BRACE) return false;
            }

            else if (s.charAt(i) == RIGHT_BRACKET) {
                if (stack.isEmpty())             return false;
                if (stack.pop() != LEFT_BRACKET) return false;
            }
        }
        return stack.isEmpty();
    }


    public static void main(String[] args) {
        Scanner stdin = new Scanner(System.in);
        String s = stdin.nextLine().trim();
        System.out.println(isBalanced(s));
    }
}

```

### 十进制转二进制

```java
public class DecToBin {
    public static void main(String[] args) {
        Stack<Integer> s = new Stack<Integer>();
        while (n > 0) {
            s.push(n % 2);
            n = n / 2;
        }
        while (!s.isEmpty())
            System.out.print(s.pop());
        System.out.println();
    }
}
```
