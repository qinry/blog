---
title: "多线程"
date: 2021-02-05T22:32:16+08:00
categories: "Java"
description: "java多线程编程基础，内容线程的创建启动与终止，休眠，死锁，线程间协作，线程池等"
tags : ["Java"]
---

*   [一、概述](#%E4%B8%80%E6%A6%82%E8%BF%B0)
*   [二、线程创建](#%E4%BA%8C%E7%BA%BF%E7%A8%8B%E5%88%9B%E5%BB%BA)
*   [三、线程休眠](#%E4%B8%89%E7%BA%BF%E7%A8%8B%E4%BC%91%E7%9C%A0)
*   [四、线程阻塞](#%E5%9B%9B%E7%BA%BF%E7%A8%8B%E9%98%BB%E5%A1%9E)
*   [五、线程终止](#%E4%BA%94%E7%BA%BF%E7%A8%8B%E7%BB%88%E6%AD%A2)
*   [六、线程安全](#%E5%85%AD%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8)
*   [七、死锁](#%E4%B8%83%E6%AD%BB%E9%94%81)
*   [八、多线程通信](#%E5%85%AB%E5%A4%9A%E7%BA%BF%E7%A8%8B%E9%80%9A%E4%BF%A1)
*   [九、线程的状态](#%E4%B9%9D%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%8A%B6%E6%80%81)
*   [十、Callable&lt;V&gt;和FutureTask&lt;V&gt;](#%E5%8D%81callablev%E5%92%8Cfuturetaskv)
*   [十一、线程池](#%E5%8D%81%E4%B8%80%E7%BA%BF%E7%A8%8B%E6%B1%A0)

### 一、概述

进程：是一个内存中运行的应用程序，每个进程都有一个独立空间

线程：是进程中的一个执行路径，共享一个内存空间，线程之间可以自由切换，并发执行，一个进程最少有一个线程；线程实际上是进程基础上的进一步划分，一个进程启动后，里面的若干执行路径又可以划分若干个线程

同步：排队执行，效率低但数据安全

异步：同时执行，效率高但数据不安全

并发：由两个或多个事件，在一个时间段内发生

并行：由两个或多个时间，在一个时间点上发生

线程调度：

1.  分时调度：所有线程轮流执行CPU的使用权，平均分配每个线程占用CPU的时间

2.  抢占式调度：优先级高的线程优先使用CPU，如果线程的优先级相同，那么随机选择一个（线程随机性），Java使用的抢占式调度。

    CPU使用抢占式调度模式在多个线程间进行高速切换。对于CPU的一个核心而言，某一个时刻，只能执行一个线程，而CPU的多个线程切换速度相对我们的感觉要快，看上去就是同一个时刻运行。其实，多线程程序并能提高程序的速度，但能够提高程序运行效率，让CPU的使用率更高。

### 二、线程创建

每一个线程都拥有自己的栈空间，共用一份堆空间

继承Thread类

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
           System.out.println(Thread.currentThread().getName()+":"+(i+i+i));
        }
    }
}
```

实现Runnable接口

```java
public class RunnableImpl implements  Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName()+":"+(i*i));
        }
    }
}
```

实现Runnable与继承Thread相比的优势:

1.  通过创建任务，然后给线程分配的方式实现多线程，更适合多线程同时执行相同的任务的情况；
2.  可以避免单继承带来的局限性
3.  线程与任务是分离的，提高了程序的健壮性

线程池技术，接受Runnable类型和Callable类型的任务（Thread是实现Runnable接口的类）

Thread启动使用的start方法而不是run方法，run方法是描述线程执行的任务

Thread.currentThread方法获取当前线程。可以通过setName和getName分别设置和访问线程的名称。setPriority和getPriority分别设置和访问线程优先级，有MIN_PRIORITY、MAX_PRIORIY和NORM_PRIORITY定义好的字段设置优先级。

stop方法已过时，stop方法会导致线程停止但资源未释放的问题，不安全，不建议使用。可以在线程定义某一个变量run方法取检测变量，当变量符合自己设好的停止条件，使用return语句通知线程停止。

Thread.sleep方法使线程休眠，传入long变量设置休眠的毫秒数（还可以再后加int变量设置纳秒数）。

setDaemon方法设置线程是否为守护线程，传入boolean变量来设定，true设置为守护线程，false设置为用户线程，必须在start方法前使用。守护线程依赖用户线程，当用户线程都结束时，守护线程即使没完成，也会被杀死结束。例如：

```java
public class Threading {
    public static void main(String[] args){
        // 所有用户线程完成，那么程序完成结束
        Runner runner = new Runner();
        Thread t = new Thread(runner);
        // 线程t为守护线程，当最后一个用户线程完成时，自动结束（可能还未完成）
        t.setDaemon(true);
        t.start();
        for (int i = 0; i < 5; i++) {
            try {
                  Thread.sleep(1000);
            } catch(InterruptedException e) {
				e.printStackTrace();
            }
            System.out.println(i);
        }
    }
}
class Runner implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            try {
                  Thread.sleep(1000);
            } catch(InterruptedException e) {
                System.out.println(Thread.currentThread().getName()+"提前结束");
				return;
            }
            System.out.println(i);
        }
    }
}
```

### 三、线程休眠

java.util提供的TimeUnit类可以使线程休眠：

```java
import java.util.concurrent.TimeUnit;
public class Threading {
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            try {
                TimeUnit.SECONDS.sleep(1);
//                Thread.sleep(1000);
            } catch(InterruptedException e) {
				e.printStackTrace();
            }
            System.out.println(i);
        }
    }
}

```

休眠可能引发InterruptedException，要做异常的捕获。

### 四、线程阻塞

线程阻塞不只有线程休眠，还有可能是线程进行IO操作时，比如读取一个文件，线程等待输入完才会进入下一步。可以把线程阻塞，简单理解为线程中所有比较耗时的步骤。

### 五、线程终止

线程是否停止，应该由线程本身决定，而不应该外部杀死线程，如果线程涉及资源占用，外部强行杀死程序，导致资源占用却是未被回收的垃圾。

外部调用interrupt方法，给线程做好中断标记，线程内部捕获InterruptedException后处理，可以是线程自杀处理来完成线程终止，或者忽视掉继续执行。例如：
```java
public class Threading {
    public static void main(String[] args)  {
        Runner runner = new Runner();
        Thread t = new Thread(runner);
        t.start();
        for (int i = 0; i < 5; i++) {
            try {
                  Thread.sleep(1000);
            } catch(InterruptedException e) {
				e.printStackTrace();
            }
            System.out.println(i);
        }
        // 给线程t打中断标记
        t.interrupt();
    }
}
class Runner implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            try {
                  Thread.sleep(1000);
            } catch(InterruptedException e) {
                System.out.println(Thread.currentThread().getName()+"提前结束");
				return;
            }
            System.out.println(i);
        }
    }
}
```

### 六、线程安全

多线程同时访问共享的变量并修改，会导致数据错乱，面临数据不安全。

以下代码线程不安全：

```java
public class Threading {
    public static void main(String[] args)  {
        Runnable sellingTicket = new Runnable(){
            int count = 10;
            @Override
            public void run() {
                for (;;) {
                    if (count > 0) {
                        System.out.println("开始买票");
                    	try {
                        	Thread.sleep(1000);
                    	} catch(InterruptedException e) {
                        	e.printStackTrace();
                    	}
                    	count = count  - 1;
                   		System.out.println(Thread.currentThread().getName() + "出票完成，余票：" + count);
                    } else {
                        break;
                    }
                }
            }
        };
        Thread t1 = new Thread(sellingTicket);
        Thread t2 = new Thread(sellingTicket);
        Thread t3 = new Thread(sellingTicket);
        t1.start();
        t2.start();
        t3.start();
    }
}
```

给线程加锁，使线程排队执行。

同步代码块：

```java
public class Threading {
    public static void main(String[] args)  {
        Runnable sellingTicket = new Runnable(){
            private int count = 10;
            @Override
            public void run() {
                for (;;) {
                    synchronized (this) {
                        if (count > 0) {
                            System.out.println("开始卖票");
                            try {
                                Thread.sleep(1000);
                            } catch (InterruptedException e) {
                                e.printStackTrace();
                            }
                            count--;
                            System.out.println(Thread.currentThread().getName() + "出票完成，余票：" + count);
                        } else {
                            break;
                        }
                    }
                }

            }
        };
        Thread t1 = new Thread(sellingTicket);
        Thread t2 = new Thread(sellingTicket);
        Thread t3 = new Thread(sellingTicket);
        t1.start();
        t2.start();
        t3.start();
    }
}
```

同步方法：

```java
public class Threading {
    public static void main(String[] args)  {
        Runnable sellingTicket = new Runnable(){
            private int count = 10;
            @Override
            public void run() {
                for (;; ) {
                    boolean flag = sale();
                    if (!flag) {
                        break;
                    }
                }
            }
            public synchronized boolean sale() {
                if (count > 0) {
                	System.out.println("开始买票");
                    try {
                        Thread.sleep(1000);
                   	} catch(InterruptedException e) {
                        e.printStackTrace();
                    }
                    count = count  - 1;
                   	System.out.println(Thread.currentThread().getName() + "出票完成，余票：" + count);
                    return true;
                }
                return false;
            }
        };
        Thread t1 = new Thread(sellingTicket);
        Thread t2 = new Thread(sellingTicket);
        Thread t3 = new Thread(sellingTicket);
        t1.start();
        t2.start();
        t3.start();
    }
}
```

显示锁：

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
public class Threading {
    public static void main(String[] args)  {
       Runnable sellingTicket = new Runnable(){
            private int count = 10;
           //显示锁默认非公平锁，传入true则为公平锁
           //private Lock lock = new ReentrantLock(true);
            private Lock lock = new ReentrantLock();
            @Override
            public void run() {
                for (;;) {
                    lock.lock();
                    try {
                        if (count > 0) {
                            System.out.println("开始卖票");
                            try {
                                Thread.sleep(1000);
                            } catch (InterruptedException e) {
                                e.printStackTrace();
                            }
                            count--;
                            System.out.println(Thread.currentThread().getName() + "出票完成，余票：" + count);
                        } else {
                            break;
                        }
                    } finally {
                        lock.unlock();
                    }
                }
            }

        };
        Thread t1 = new Thread(sellingTicket);
        Thread t2 = new Thread(sellingTicket);
        Thread t3 = new Thread(sellingTicket);
        t1.start();
        t2.start();
        t3.start();
    }
}
```

公平锁：锁释放后，先来的线程先得到锁

非公平锁：锁释放后，所有线程竞争锁，谁抢到谁获得锁。

**隐式锁（synchronized锁）与显式锁（Lock）的区别？**

（1）底层实现不同。

*   synchronized锁是由JVM维护的，是JVM层的锁。**通过 monitorenter 进行加锁**（底层是通过 monitor 对象来完成的，其中的wait/notify等方法也是依赖于 monitor 对象的。只有在同步代码块或者同步方法中才可以调用wait/notify等方法。因为只有在同步代码块或者是同步方法中，JVM才会调用 monitor对象）；**通过 monitorexit 来退出锁**。
*   Lock是JDK5推出的具体类。使用它是通过调用对应的API方法来获取锁和释放锁，是API层面的锁

（2）用法不同

*   synchronized关键字，能够自动获取和释放锁。执行完这个关键字相关的代码后，系统自动释放程序占用锁，它是系统维护的，没有程序的问题，是不会死锁的。可以在方法前加上此关键字修饰为同步方法，或者此关键则后跟圆括号里面放入锁对象，然后带有花括号的代码块，里面代码就是要同步的。锁对象可以是任意类型的实例对象，不过多线程应该使用同一个对象当作锁对象，这样才有线程同步的效果
*   Lock显示锁，需要手动调用lock方法和unlock方法，来获取和释放锁。如果没有释放锁可能会导致死锁。要配合try-finally语句。

（3）中断与否

*   隐式锁不可中断，只能正常运行或抛出异常
*   显示锁可以中断，Lock对象调用lockInterruptibly方法放到代码块中，然后调用interrupt()方法可以中断

（4）公平与否

*   隐式锁一定非公平，线程都是会互相竞争锁，不是排队获取
*   显示锁，通过传入构造器的参数决定公平与否，默认是非公平的，传入true则为公平锁。

（5）绑定多个Condition对象与否

*   隐式锁:没有。要么随机唤醒一个线程；要么是唤醒所有等待的线程。
*   显示锁:用来实现分组唤醒需要唤醒的线程，可以精确的唤醒，而不是像显示锁不能精确唤醒线程。

### 七、死锁

两个线程先是各自持有一部分锁，互相之间等待某一个线程释放锁，所有线程都陷入等待，无法继续执行下去。

比如：线程A的执行要先获取锁1，后获取锁2。线程B的执行要先获取锁2，后获取锁1。如果线程A和线程B都同时获取了自己的第一个锁，那么获取第二个锁时，导致了死锁。

以下示例，有可能死锁。

```java
class Culprit {
    public synchronized void say(Police p) {
        System.out.println("罪犯：你放了我，我放了人质");
        p.fun();
    }
    public synchronized void fun() {
        System.out.println("罪犯放了人质，自己跑了");
    }
}

class Police {
    public synchronized void say(Culprit c) {
        System.out.println("警察：你放了人质，我放过你");
        c.fun();
    }
    public synchronized void fun() {
        System.out.println("警察救了人质，罪犯跑了");
    }
}

public class Threading {
    public static void main(String[] args)  {
		// 死锁
        Culprit c = new Culprit();
        Police p = new Police();
        Thread t = new Thread(new Runnable(){
            private Police police;
            private Culprit culprit;
            {
                police = p;
                culprit = c;
            }
            @Override
            public void run() {
                police.say(culprit);
            }
        });
        t.start();
        c.say(p);
    }
}
```

输出：


```java
罪犯：你放了我，我放了人质
警察：你放了人质，我放过你
```

防止线程同时获取多个锁，比如嵌套的同步代码块等，可以避免死锁。

### 八、多线程通信

生产者与消费者，线程间的协作。生产者线程执行一次循环后等待，消费者线程执行一次循环后先唤醒生产者，自己等待被唤醒。

生产者被唤醒，再执行一次循环后唤醒消费者，自己等待，消费者又像上次执行。如此生产者与消费者交替反复执行，直到循环结束。

Object中的wait方法让当前线程等待，notifyAll方法唤醒所有等待此对象监视器的线程，notify方法只唤醒一个线程比较少用。以上三种方法，必须在同步方法或者同步代码块内执行。wait方法和notifyAll方法会释放锁，不同于过时的stop方法、suspend方法、resume方法，还有sleep方法都不会在释放锁。

示例：

```java
import java.util.concurrent.TimeUnit;

public class Communiticate {

    public static void main(String[] args) {
        Food food = new Food();
        Thread thread1 = new Thread(new Cook(food));
        Thread thread2 = new Thread(new Waiter(food));
        thread1.start();
        thread2.start();

    }

    static class Food {
        private String name;
        private String taste;
        // true：厨师做饭，false：服务员端菜
        private boolean flag = true;
        public synchronized void produce(String name, String taste) {
            if (flag) {
                this.name = name;
                try {
                    TimeUnit.MILLISECONDS.sleep(300);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                this.taste = taste;
                flag = false;
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                notifyAll();
            }
        }

        public synchronized void cost() {
            if (!flag) {
                System.out.println("服务员端菜，菜品是"+name+"，味道是"+taste);
                flag = true;
                notifyAll();
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

    }

    static class Cook implements Runnable {
        private Food food;
        public Cook(Food f) {
            this.food = f;
        }

        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                if ((i & 1) == 0) {
                    food.produce("扬州炒饭", "咸的");
                } else {
                    food.produce("水煮鱼", "麻辣的");
                }
            }
        }
    }

    static class Waiter implements Runnable {
        private Food food;
        public Waiter(Food f) {
            this.food = f;
        }
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                try {
                    TimeUnit.MILLISECONDS.sleep(300);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                food.cost();
            }
        }
    }
}
```

输出：

```java
服务员端菜，菜品是扬州炒饭，味道是咸的
服务员端菜，菜品是水煮鱼，味道是麻辣的
服务员端菜，菜品是扬州炒饭，味道是咸的
服务员端菜，菜品是水煮鱼，味道是麻辣的
服务员端菜，菜品是扬州炒饭，味道是咸的
服务员端菜，菜品是水煮鱼，味道是麻辣的
服务员端菜，菜品是扬州炒饭，味道是咸的
服务员端菜，菜品是水煮鱼，味道是麻辣的
服务员端菜，菜品是扬州炒饭，味道是咸的
服务员端菜，菜品是水煮鱼，味道是麻辣的
```

### 九、线程的状态

![picture](../../../../images/posts/2021/02/java-concurrent/1.png)

*   `NEW`
尚未启动的线程处于此状态。
*   `RUNNABLE`
在Java虚拟机中执行的线程处于此状态。
*   `BLOCKED`
被阻塞等待监视器锁定的线程处于此状态。
*   `WAITING`
无限期等待另一个线程执行特定操作的线程处于此状态。
*   `TIMED_WAITING`
正在等待另一个线程执行最多指定等待时间的操作的线程处于此状态。
*   `TERMINATED`
已退出的线程处于此状态

### 十、Callable&lt;V&gt;和FutureTask&lt;V&gt;

```java
public class Caller<V> implements Callable<V> {
    private V val;

    public Caller(V val) {
        this.val = val;
    }

    @Override
    public V call() throws Exception {
        TimeUnit.SECONDS.sleep(3);
        return val;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Integer> future = new FutureTask<>(new Caller<Integer>(47));
        Thread thread = new Thread(future);
        thread.start();
        TimeUnit.SECONDS.sleep(4);
        if (future.isDone()) {
            System.out.println(future.get());
        } else {
            System.out.println("中断");
            future.cancel(true);
        }
    }
}
```

输出：

```java
47
```

### 十一、线程池

线程池的好处：

*   降低资源消耗
*   提高响应速度
*   提高线程的管理性

shutdown方法启动有序关闭，其中先前提交的任务将被执行，但不会接受任何新任务。 shutdownNow方法尝试停止所有正在执行的任务，停止等待任务的处理，并返回等待执行的任务列表。execute方法可以执行Runnable类型任务，submit可以执行Runnable类型和Callable类型任务。

缓存线程池

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
public class UseCachedThreadPool {
    public static void main(String[] args) {
        ExecutorService exec = Executors.newCachedThreadPool();
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.shutdown();
    }
}
```

输出：

```java
pool-1-thread-3 Hello World
pool-1-thread-2 Hello World
pool-1-thread-1 Hello World
pool-1-thread-1 Hello World
```

定长线程池

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class UseFixedThreadPool {
    public static void main(String[] args) {
        ExecutorService exec = Executors.newFixedThreadPool(2);
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
                try {
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
                try {
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });

        exec.shutdown();
    }
}

```

输出：

```java
pool-1-thread-1 Hello World
pool-1-thread-2 Hello World
pool-1-thread-2 Hello World
```

单线程池

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class UseSingleThreadPool {
    public static void main(String[] args) {
        ExecutorService exec = Executors.newSingleThreadExecutor();
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.execute(new Runnable(){
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+" Hello World");
            }
        });
        exec.shutdown();
    }
}
```

输出：

```java
pool-1-thread-1 Hello World
pool-1-thread-1 Hello World
pool-1-thread-1 Hello World
pool-1-thread-1 Hello World

```

周期性定长线程池

```java
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class UseScheduledThreadPool {
    public static void main(String[] args) {
        ScheduledExecutorService exec = Executors.newScheduledThreadPool(2);
        // 任务 延迟时长 单位
        exec.schedule(new Runnable(){
            @Override
            public void run() {
                System.out.println("Hello World");
            }
        }, 5, TimeUnit.SECONDS);

        // 任务 延迟时长 周期时长 单位
        exec.scheduleAtFixedRate(new Runnable() {
            @Override
            public void run() {
                System.out.println("Write once, Run Anywhere");
            }
        }, 5, 1, TimeUnit.SECONDS);
        exec.shutdown();
    }
}
```

输出：

```java
Write once, Run Anywhere
Hello World
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
Write once, Run Anywhere
```