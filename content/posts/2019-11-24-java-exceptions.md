---
title: "《Thinking in Java》 --  异常处理"
date: 2019-11-24T08:47:23+08:00
tags: [ "java"]
---

# 异常处理

## 异常抛出
创建一个异常对象，然后从当前环境对象抛出，阻止当前方法或作用域的执行。

语法: ```throw new NullPointerException```, 关键字`throw`之后，和创建普通对象一样创建异常对象, ```new <异常类>```。

## 异常捕获
try块中执行代码，遇到异常抛出，交给catch捕获, 然后进行异常处理。将try-catch语句可以放入while循环，由于try中抛出异常，会在catch中匹配，是不会回到原来的地方，在while帮助下，会恢复原来之前地方继续执行，这是一种恢复模型（不太实用，尽量少用，因为它会导致耦合），不然就是终止模型。

语法:
```Java
try {
    // 这里抛出异常
} catch(Exception e) {
    // 这里匹配后处理
}
```

## 异常说明
为了让调用者能知道方法可以抛出异常，以便于捕获它们，使用异常说明，作为方法声明的一部分,在参数列表之后，花括号之前。可以声明方法将抛出异常，实际不抛出，为异常先占个位子，对于定义抽象基类和接口时比较重要，其派生类或接口实现将能抛出预声明的异常

语法:
```Java
void f() throws Exception {
    /// 会抛出异常
}
```

## finally子句
用finally子句进行清理，不仅限于内存清理，还有资源恢复初始态，如把打开的文件关闭等等。常常将finally放在catch之后。

语法:
```Java
try {
    // 抛出异常
} catch(Exception e) {
    // 处理异常
} finally {
    // 清理
}
```
finally能保证总是执行的。在它之前有return，也会在方法结束前，执行finally子句。

注意：finally有缺陷，会导致一些异常被忽略,如下:
```Java
// 在某个类内部
static void f() throws FirstException {}
static void g() throws SecondException {}
public static void main(String[] args) {
    try {
        try {
            f();
        } finally {
            g();
        }
    } catch(Exception e) {

    }
}
```
这里导致 f( ) 的FirstException被忽略。

对于构造阶段可能抛出的异常，并且要清理的类，最安全的做法是嵌套的try。防止对象未创建部分被清理，如下:

```Java
public class Cleanup {
    public static void main(String[] args) {
        try {
            // 自定义的类,打开文件
            InputFile in = new InputFile("text.txt"); 
            try {
                String s;
                int i = 0;
                while ((s = in.getLine()) != null)
                    ; // 文件一行一行读到s中
            } catch(Exception e) {
                System.out.println("Caught Exception in main");
                e.printStackTrace(System.out); // 打印栈轨迹
            } finally {
                in.dispose(); // 清理
            }
        } catch(Exception e) {
            System.out.println("InputFile construction failed");
        }
    }
}
```

## 异常的限制
当覆盖方法的时候，只能抛出在基类方法的异常说明列出异常及其派生的异常。

注意：某个类继承它的基类，和实现它的接口，其中基类和接口有同名方法，分别有不同的异常说明，接口的不能改变基类方法的异常说明接口；异常限制对构造器不起作用，派生类构造器不能捕获基类构造器抛出的异常，所以派生构造器必须包括基类构造器的异常说明；一个出现在基类的异常说明不一定在派生类方法的异常说明出现，说明某个特定方法的“异常说明接口”可能是变小，这和类接口在继承的情况相反。


## 异常匹配
抛出异常后，会根据代码书写顺序找出最前的catch子句处理后，不再向下查找catch子句。还有不要求完全匹配，可以是基类异常。

注意:应该把派生类异常放在前，基类放在后面，否则报告错误。如下:

``` Java
// 这样做如果成功，会导致派生异常屏蔽，故不允许，报错.
try {
    throw new DerivedException();
} catch(BaseException e) {
    // ...
} catch(DerivedException e) {
    // ...
}
```

## 异常类的方法
Java标准库异常类的基类是Throwable。

方法如下:
```Java
// 获取详细信息，或本地语言表示信息
String getMessage()
String getLocalizedMessage()

// 对Throwable的简单描述，有详细信息就包括在内
String toString()

// 打印栈轨迹
void printStackTrace()
void printStackTrace(PrintStream)
void printStackTrace(java.io.PrintWriter)

// 在Throwable对象内部记录栈帧的当前(新)状态
Throwable fillInStackTrace() 
```

## 异常链
定义：捕获异常后抛出另一个异常，并把原始异常信息保存下来。

Throwable子类包括Error（报告系统错误）, Exception（最常用的异常）以及RuntimeException（运行时异常，又称未检查异常）的构造器，有接受Cause参数(表示原始异常对象)的构造器,把原始异常传递给新异常。有同等效果的一个方法,initCause()也能传递原始异常。

如下:
```Java
try {
    // ..
} catch(OneException e) {
    throw new RuntimeException(e);// 传递新异常，并抛出
}
// 在某个方法内
OneException e = new OneException(); // 自定义继承Exception的异常类
e.initCause(new NullPointerException());
throw e;
```

#### 把异常传递给控制台
给main()声明异常说明Exception, 把它传递个控制台

```Java
import java.io.*;
public class MainException {
    public static
    void main(String[] args) throws Exception {
        BufferedReader in = new BufferedReader(
            new InputStreamReader(System.in)
            );
        String s = in.readLine();
        System.out.println("s =  " + s);
    }
}
```
这样就不必在main()中写try-catch语句。

#### 把被检查异常转换为不检查类型
异常链保证把被检查异常的检查屏蔽掉，又不丢失它的信息。用getCause()找回原始异常类的信息。如下:
```Java
import java.io.*;

class WrapCheckedException {
    void throwRuntimeException(int type) {
        try {
            switch(type) {
                case 0: throw new FileNotFoundException();
                case 1: throw new IOException();
                case 2: throw new RumtimeException("Where am I？");
                default: return;
            }
        } catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
}

class SomeOtherException extends Exception {}

public class TurnOffChecking {
    public static void main(String[] args) {
        WrapCheckedException wce = new WrapCheckedException();
        wce.throwRuntimeException(3);
        for (int i = 0; i < 4; i++) 
            try {
                if (i < 3) 
                    wce.throwRuntimeException(i);
                else 
                    throw new SomeOtherException();
            } catch(SomeOtherException e) {
                System.out.println("SomeOtherException: " + e);
            } catch(RuntimeException e) {
                try {
                    throw e.getCause();
                } catch(FileNotFoundException e) {
                    System.out.println("FileNotFoundException: " + e);
                } catch(IOException e) {
                    System.out.println("IOException: " + e);
                } catch(Throwable e) {
                    System.out.println("Throwable: " + e);
                }
            }
    }
}

```

