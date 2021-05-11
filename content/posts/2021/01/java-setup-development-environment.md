---
title: "Java开发环境搭建与介绍"
date: 2021-01-16T19:11:45+08:00
categories: "Java"
description: "本文介绍Java在Windows10中开发环境如何搭建，以及创建第一个Java工程，如何实现自动关机，最后讲讲Java语言概念特性及技术体系"
tags : ["Java"]
---

文章目录

*   [一、环境搭建](#%E4%B8%80%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA)

    *   [1.1 JDK的安装](#11-jdk%E7%9A%84%E5%AE%89%E8%A3%85)

        *   [1.1.1 下载JDK](#111-%E4%B8%8B%E8%BD%BDjdk)
    *   [1.1.2 安装JDK](#112-%E5%AE%89%E8%A3%85jdk)

    *   [1.2 eclipse的安装](#12-eclipse%E7%9A%84%E5%AE%89%E8%A3%85)

        *   [1.2.1 下载eclipse](#121-%E4%B8%8B%E8%BD%BDeclipse)
    *   [1.2.2 安装eclipse](#122-%E5%AE%89%E8%A3%85eclipse)

    *   [1.3 配置环境变量](#13-%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)

        *   [为什们配置JAVA_HOME](#%E4%B8%BA%E4%BB%80%E4%BB%AC%E9%85%8D%E7%BD%AEjava_home)

    *   [1.4 创建Java工程](#14-%E5%88%9B%E5%BB%BAjava%E5%B7%A5%E7%A8%8B)

        *   [1.4.1 HelloWorld工程](#141-helloworld%E5%B7%A5%E7%A8%8B)
    *   [1.4.2 自动关机程序](#142-%E8%87%AA%E5%8A%A8%E5%85%B3%E6%9C%BA%E7%A8%8B%E5%BA%8F)

*   [二、介绍](#%E4%BA%8C%E4%BB%8B%E7%BB%8D)

    *   [2.1 Java重要发展历程](#21-java%E9%87%8D%E8%A6%81%E5%8F%91%E5%B1%95%E5%8E%86%E7%A8%8B)
    *   [2.2 Java技术体系](#22-java%E6%8A%80%E6%9C%AF%E4%BD%93%E7%B3%BB)
    *   [2.3 Java特点](#23-java%E7%89%B9%E7%82%B9)
    *   [2.4 GC](#24-gc)

### 一、环境搭建

开发Java工程离不开JDK（Java Development Kit Java开发工具包），这个是最基本的必须下载和安装，还有为了方便开发者编写、运行和调试程序出现了IDE（Integrated Development Environment 集成开发环境）。用于开发Java程序的IDE有很多，其中知名有开源的eclipse，还有别的IDE，如：Intellij IDEA，由于是商业软件，需要收费。下面主要说明JDK与eclipse的安装(Windows10来说明)。

#### 1.1 JDK的安装

##### 1.1.1 下载JDK

官网地址：https://www.oracle.com，下载操作如下一系列图所示

*   进入官网，点击Products进入下一步

![picture](../../../../images/posts/2021/01/java-setup-development-environment/1.png)

*   点击Java，进入下一步

![picture](../../../../images/posts/2021/01/java-setup-development-environment/2.png)

*   下拉到Java，点击Oracle JDK 进入下一步

![picture](../../../../images/posts/2021/01/java-setup-development-environment/3.png)

*   选择JDK的版本，选择Java SE 11(LTS) ，点击JDK Download并进入下一步

![picture](../../../../images/posts/2021/01/java-setup-development-environment/4.png)

*   找到适合的JDK安装包，点击下载

![picture](../../../../images/posts/2021/01/java-setup-development-environment/5.png)

![picture](../../../../images/posts/2021/01/java-setup-development-environment/6.png)

*   下载前需要登入Oracle账号，有账号登入即可，没有可以申请。申请遇到问题，可以上[百度](https://www.baidu.com)寻求答案

![picture](../../../../images/posts/2021/01/java-setup-development-environment/7.png)

##### 1.1.2 安装JDK

*   双击运行jdk-11.0.9_windows-x64_bin.exe

![picture](../../../../images/posts/2021/01/java-setup-development-environment/8.png)

*   连续点击默认的下一步即可

![picture](../../../../images/posts/2021/01/java-setup-development-environment/9.png)

![picture](../../../../images/posts/2021/01/java-setup-development-environment/10.png)

*   最后安装完成，点击关闭

![picture](../../../../images/posts/2021/01/java-setup-development-environment/11.png)

#### 1.2 eclipse的安装

##### 1.2.1 下载eclipse

官网地址：http://www.eclipse.org

*   进入官网，点击Download

![picture](../../../../images/posts/2021/01/java-setup-development-environment/12.png)

*   点击Download Packages，进入下一步

![picture](../../../../images/posts/2021/01/java-setup-development-environment/13.png)

*   选择安装压缩包，进入下一步

![picture](../../../../images/posts/2021/01/java-setup-development-environment/14.png)

*   点击Download，下载

![picture](../../../../images/posts/2021/01/java-setup-development-environment/15.png)

*   之后会出现donate页面，有能力的可以捐献，没有的默默点击关闭吧！

![picture](../../../../images/posts/2021/01/java-setup-development-environment/16.png)

##### 1.2.2 安装eclipse

*   解压压缩包eclipse-jee-2020-12-R-win32-x86_64.zip到任何喜欢的目录即可，这里我选择在用户的目录![picture](../../../../images/prepare/17.png)

*   为eclipse.exe创建桌面快捷方式或着固定到任务栏，方便我们打开eclipse

*   打开eclipse

![picture](../../../../images/posts/2021/01/java-setup-development-environment/18.png)

![picture](../../../../images/posts/2021/01/java-setup-development-environment/19.png)

#### 1.3 配置环境变量

在windows10中，右键此电脑点击属性，点击高级系统设置，点击环境变量，创建环境变量JAVA_HOME，设置为JDK的家目录

![picture](../../../../images/posts/2021/01/java-setup-development-environment/20.png)

再设置变量PATH，在原有变量值的基础上追加英文分号`;`，还有`%JAVA_HOME%/bin`即可，后面全部确定完成环境变量的配置。

##### 为什们配置JAVA_HOME

因为变量PATH系统帮我们创建好的，它的值关联到系统中其他命令的工作，修改可能会影响它们的工作，所以我们应该避免频繁的修改。如果创建了JAVA_HOME变量，只需将PATH追加一次`%JAVA_HOME%/bin`，以后需要切换JDK版本，只改JAVA_HOME就可以，避免修改PATH可能带来的风险。

#### 1.4 创建Java工程

##### 1.4.1 HelloWorld工程

点击菜单 File，拉到new中的project并点击

![picture](../../../../images/posts/2021/01/java-setup-development-environment/21.png)

选择Java Project，点Next

![picture](../../../../images/posts/2021/01/java-setup-development-environment/22.png)

项目命名HelloWorld，JRE选择Use a project specific JRE的jdk-11.0.9，最后点Finish

![picture](../../../../images/posts/2021/01/java-setup-development-environment/23.png)

点Don&rsquo;tCreate

![picture](../../../../images/posts/2021/01/java-setup-development-environment/24.png)

在HelloWorld项目下右键New-&gt;Class，完成源文件创建

![picture](../../../../images/posts/2021/01/java-setup-development-environment/25.png)

![picture](../../../../images/posts/2021/01/java-setup-development-environment/26.png)

编写第一个Java程序并运行

![picture](../../../../images/posts/2021/01/java-setup-development-environment/27.png)

Java源文件名与public类名是一致的，main方法是程序启动的入口，没有main方法Java程序是无法运行的

Java语法中分有结构定义语句和逻辑功能语句（class关键字紧跟着后面的花括号内的就是结构定义语句所在的区域，方法的花括号内是逻辑功能语句所在区域）

可以在编辑区右键在Run As中点击1 Java Application运行或在工具栏点击![picture](../../../../images/prepare/28.png)

![picture](../../../../images/posts/2021/01/java-setup-development-environment/29.png)

运行结果如下：

![picture](../../../../images/posts/2021/01/java-setup-development-environment/30.png)

##### 1.4.2 自动关机程序

![picture](../../../../images/posts/2021/01/java-setup-development-environment/31.png)

在eclipse中语句下存在红色波浪线，说明存在异常，鼠标在语句停留几秒会出现处理的建议，这里我们抛出异常，加入异常说明即可，将异常处理交给JVM

![picture](../../../../images/posts/2021/01/java-setup-development-environment/32.png)
正确代码如下：

<div class="highlight">

    <span style="color:#f92672">package</span> io.github.qinry<span style="color:#f92672">;</span>

    <span style="color:#f92672">import</span> java.io.IOException<span style="color:#f92672">;</span>

    <span style="color:#66d9ef">public</span> <span style="color:#66d9ef">class</span> <span style="color:#a6e22e">HelloWorld</span> <span style="color:#f92672">{</span>

    	<span style="color:#66d9ef">public</span> <span style="color:#66d9ef">static</span> <span style="color:#66d9ef">void</span> <span style="color:#a6e22e">main</span><span style="color:#f92672">(</span>String<span style="color:#f92672">[]</span> args<span style="color:#f92672">)</span> <span style="color:#66d9ef">throws</span> IOException <span style="color:#f92672">{</span>
    <span style="color:#75715e">//		System.out.println(&#34;Hello World&#34;);
    </span><span style="color:#75715e"></span>		Runtime<span style="color:#f92672">.</span><span style="color:#a6e22e">getRuntime</span><span style="color:#f92672">().</span><span style="color:#a6e22e">exec</span><span style="color:#f92672">(</span><span style="color:#e6db74">&#34;shutdown -s -t 6000&#34;</span><span style="color:#f92672">);</span>
    <span style="color:#75715e">//		Runtime.getRuntime().exec(&#34;shutdown -a&#34;);
    </span><span style="color:#75715e"></span>	<span style="color:#f92672">}</span>

    <span style="color:#f92672">}</span>

</div>

获取Runtime实例，允许Java程序与运行程序环境进行交互，可以执行cmd命令，关机名命令为`shutdown -s -t [时间/秒]`

取消关机命令为`shutdown -a`

自动关机和取消关机执行结果如下：

![picture](../../../../images/posts/2021/01/java-setup-development-environment/33.png)

![picture](../../../../images/posts/2021/01/java-setup-development-environment/34.png)

### 二、介绍

#### 2.1 Java重要发展历程

1991.04 —— Java的前身Oak，最初开发一种能够在各种消费性电子产品上运行的程序架构

1995.05 —— Sun将Oak更名为Java，在SunWorld大会上正式发布Java 1.0，提出&quot;Write Once, Run Anywhere&quot;的口号

1996.01 —— JDK1.0发布，技术代表有JVM、applet、AWT等

1997.02 —— JDK1.1技术代表有JAR文件格式、JDBC、JavaBeans、RMI等，内部类和反射的出现

1998.12 —— JDK1.2将技术体系拆分为面向桌面的J2SE、面向企业的J2EE、面向移动端的J2ME

1999.04 —— HotSpot VM诞生

2004.09 —— JDK5发布，增添了自动装箱、泛型、动态注解、枚举、可变长参数、遍历循环等，而且提供JUC并法包

2009.04 —— Oracle收购Sun

2013.09 —— JDK8发布，增加了Lambda表达式的支持，拥有函数式表达能力，更新了时间和日期的API，目前企业当中使用比较多的版本

2018.09 —— JDK11发布，是最新的LTS版本的JDK

#### 2.2 Java技术体系

组成部分：

*   Java程序设计语言

*   各大硬件平台的JVM实现

*   Class文件格式

*   Java类库API

    一些概念的解释：

    JDK —— Java开发工具集，包含了Java程序设计语言、Java虚拟机、Java类库

    JRE —— Java 运行时环境，包含了Java虚拟机、和Java类库API中的JavaSE API子集

    JVM —— Java虚拟机，一个可以执行Java字节码的虚拟计算机系统，有解释器组件帮助Java字节码与操作系统的交互，不同平台，它的实现不同，而且屏蔽底层运行平台的差别，实现跨平台。

#### 2.3 Java特点

*   跨平台 由于虚拟机的存在，不同平台实现有所不同，屏蔽了底层运行差别，能够”一次编写，随处运行“
*   健壮性 有异常处理机制，能处理错误与异常的输入和操作
*   安全性 有垃圾回收机制、引用代替指针（可以简单理解引用是智能指针），所以程序员不需要手动释放垃圾内存，大大减少了内存泄漏的风险
*   面向对象 Java是纯面向对象的编程语言，在Java中一切都是对象

#### 2.4 GC

不再使用的内存空间应当进行回收-垃圾回收。

在 C/C++等语言中，由程序员负责回收无用内存。

Java 语言消除了程序员回收无用内存空间的责任：

JVM 提供了一种系统线程跟踪存储空间的分配情况。并在 JVM 的空闲时，检查并释放那些可以被释放的存储空间。

垃圾回收器在 Java 程序运行过程中自动启用，程序员无法精确控制和干预
