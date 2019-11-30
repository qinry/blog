---
title: "《Thinking in Java》--  对象"
date: 2019-10-18T20:41:27+08:00
tags: [ "java"]
---
# 对象

## 对象及内存分布
1. 在Java中一切都被视为对象，但操纵的标识符实际上是对象的“引用”

2. 字符串可以用带引号的初始值， 如： `String s = "sdf";`

3. 关键字 **new** 用来创建一个新对象，存在于堆中

4. 对象在内存中的分布 
    - 寄存器
    最快存储区（Java中是按需在这里分配，程序员不直接管理）

    - 堆栈
    位于通用RAM。比较快速有效的分配内存，次于寄存器 

    - 堆
    一种通用的内存池。（大部分对象存储在这），有较高的灵活性

    - 常量储存
    常量直接存用程序代码内部；有时在嵌入式系统，常量与其他部分分离，一般存于ROM中

    - 非RAM存储
    数据存活于之外。常例：流对象、持久化对象

---
## 数据类型

### 基本类型
基本数据类型
 `boolean`, `char`, `byte`, `short`, `int`, `long`, `float`, `double` , `void`

注： boolean 有两个字面量常量true和false；
基本类型有对应的包装器类型，并在堆上创建对象以表示相应的基本类型。两者可以相互转换（自动包装）
</font>

#### 2个高精度的类似包装器类型的计算类（没有有对应的基本类型）： BigInteger 和 BigDecimal
* 运算速度相较于其他基本算术类型及其相应的包装器类型要慢，但精度高支持任何精度
* BigInteger  支持整数
* BigDecimal  支持定点数（不是浮点数），可用于货币运算

#### 数组
Java中数组被确保初始化， 且不能越界访问，为此增加了开销换来安全性和效率的提高。如果数组存放对象，实际上数组为一个引用数组（C++是不允许的），元素引用初始值为null，未指定对象；存放的是基本数据类型，初始化为0

关键字 **null**, 表示引用未指向任何对象  注： 未指定对象的引用不可使用；使用指定null的引用，运行是报错

#### 作用域
作用域由花括号（{}）位置决定， 与C，C++类似。不过Java不允许屏蔽外部作用域变量，若在内部作用域重用外部作用域变量，编译器报告变量已定义，以防止程序混乱.

对象在作用域中的可见，可存活于作用域外。若对象引用唯一，在作用域外，尽管对象存在，却无法访问，Java会有垃圾回收机制，自动清理对象，但不保证一定发生。清理动作发生，会释放所在内存空间，这就避免像C++的内存泄漏问题

#### 关键字 **class** 
class 创建一个类， 来引入自定义新类型并指明类型名，用 **new** 创建


### 自定义数据类型 --- 类 
包含字段（数据成员）和方法（成员函数）两个部分， 抽象实际对象的外观和行为


注意： Java确保默认初始化，但不适合局部变量，即在某个方法定义的变量


方法组成 = 名称+参数列表+返回值+方法体

+ 描述定义： `ReturnType methodName( Argument list) { /* 方法体 */ }`
+ 描述调用：`objectName.methodName(arg1, arg2, arg3); // 向对象发送消息 `

参数列表要指定传递对象类型和名称，看似传对象实则传递是引用

关键字 **Return** ， 在方法体中，表示停止方法和返回值，如果返回类型void，只有退出方法

---
### 名字可见性
使用Internet域名避免命名冲突（org, net. com, edu等）;包用小写；列如： net.mindview.utility.foibles(句点表示子目录划分)

### import 导入类（预定义好的类）

+ 一次导入 ， 如： import java.util.*;
+ 一个个导入, 如： import java.util.ArrayList;

### 关键字 **static** 修饰的成员
* 所有对象共享成员在同一个类中，独立于所有对象
* 当声明**static**时，就意味着域或方法不会与包含它的类的任何对象实例相关联。当创建对象，也可以调用类的**static**方法或访问**static**域
* 类数据和类方法只作用于整个类，不是类的特定实例对象而存在。对类来说， 只有一份存储空间
* 对主类的main() 很重要，因为能在不创建对象的情况下调用**static**方法
</font>

## Java程序


```Java
import java.util.*; // 每个程序文件开头，必须声明import，以便引入文件
                    // 代码中需要用到的额外类    
public class HelloDate {
    public static void main(String[] args) {
        System.out.println("Hello, it's"); // java.lang 会自动Java源文件
        System.out.println(new Date());
    }
}
```


使用 *javac* 和 *java* 工具命令编译和运行Java程序 或 *ant* 构建 Java项目（最近流行*Maven*和*Gradle*）

---
## 注释与嵌入式文档
注释形式: (1). 嵌入式HTML; (2). 文档标签

3种类型注释文档在类、域和方法后。

javadoc， 用于提取注释为html文档的工具。doclet操作javadoc处理过的信息。javadoc 使用 -private 来提取private和包访问权限成员注释
    
### javadoc 文档标签示例


```
@see className /fully-qualified-className /fully-qualified-className#methodName
引用其它类（See Also）

@author author-information 表示作者信息，javadoc使用-author，生成作者信息

@since 用来指定JDK版本

@param parameter-name description 参数列表描述

@return description 返回类型描述

@throws fully-qualified-class-name description 异常处理

@version version-information javadoc使用-version，生成HTML中特别提取该信息

@deprecated 用于指出一些就特性已由新的取代建议不使用旧特性，因为在不久的将来他们很可能删除

{@link package.class#methodlabel} 只用于行内，“label”为超链接文本

{@docRoot} 产生文档根目录的相对路径， 用于文档树页面的显式超链接

{@inheritDoc} 从当前类的最直接基类的继承文档

```


## 编码风格 --- 驼峰风格
驼峰风格： 类名首字母大写；有几个单词组成要并在一起，每个单词首字母大写(如： HelloDate)；标识符第一个字母小写，其他与类名相同 (如：toUpperCase)
