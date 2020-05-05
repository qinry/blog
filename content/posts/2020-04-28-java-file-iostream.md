---
title: "文件路径与I/O流"
date: 2020-04-28T17:42:29+08:00
tags: ["java"]
---

## File类

`File`类作用和它的名字不太相符，File除了可以代表一个特定的文件，还可以表示一个目录（包含一组文件），最准确认为是“路径”。还一个与之相关的接口`FilenameFilter`，用来筛选路径的目录或文件。它们在java.io包中。

用例：

```java
import java.util.regex.*;
import java.util.*;
import java.io.*;
public class DirList {
    public static void main(String[] args) {
        File path = new File("."); // .表示当前工作路径
        String[] list;
        if(args.length == 0)
            list = path.list();
        else
            list = path.list(new DirFilter(args[0]));
        Arrays.sort(list, String.CASE_INSENSITIVE_ORDER);
        for(String dirItem : list)
            System.out.println(dirItem)
    }
}

class DirFilter implements FilenameFilter {
    private Pattern pattern;
    public DirFilter(String regex) {
        pattern = Pattern.compile(regex);
    }
    public boolean accept(File dir, String name) {
        return pattern.matcher(name).matches();
    }
}
```

File的list(FilenameFilter)或者list()方法产生工作目录下的文件名的数组，本质是String数组,还有类似的listFiles(FilenameFilter)或listFiles()方法工作目录的生成File数组。

## File的一些方法

方法名/构造器|作用
-|-
File(String parent, String child)|构造File类
File(String pathname) | 构造File类
boolean isFile()| 此路径的目标是否为文件
boolean isDirectory()| 此路径的目标是否为目录
boolean delete()|删除文件或目录
boolean mkdirs()|创建目录路径，包括一些不存在的父路径
String getAbsolutePath()|返回绝对路径名
String getParent()|获得绝对路径上的上级路径名
String getPath()|获得相对路径名
boolean canRead()|是否可读
boolean canWrite()|是否可写
boolean getName()|文件或目录的路径名
long lastModified()|获得上次修改文件的时间
boolean renameTo(File)|更改文件名
long length()|获得文件长度

## I/O流

流代表有能力产出数据的数据对象，或者有能力接受数据的接收端对象，流屏蔽了I/O设备处理数据的细节。数据来源有字节数组、String对象、文件、管道、多种流组合的序列、其他数据源(如:Internet连接等)；数据的去向有字节数组(非String)、文件或管道。

面向字节的流有InputStream、OutputStream，面向字符的流有Reader、Writer。有专门的适配器将字节流转换为字符流。常用的流对象和过滤器如下：

|Java1.0|Java1.1|
|:-:|:-:|
InputStream(基类)|Reader(基类)
(空)|InputStreamReader，将InputStream转换为Reader
OutputStream(基类)|Writer(基类)
(空)|OutputStreamWriter，将OutputStream转换为Writer
FileInputStream|FileReader
FileOutputStream|FileWriter
StringBufferInputStream(弃用)|StringReader
(无)|StringWriter
ByteArrayInputStream|CharArrayReader
ByteArrayOutputStream|CharArrayWriter
PipedInputStream|PipedReader
PipedOutputStream|PipedWriter

过滤器用于包装上面的类，使之增加额外的功能，如缓冲、打印、数据格式化等等

过滤器|
|:-:|:-:|
FilterInputStream(基类)|FilterReader
FilterOutputStream(基类)|FilterWriter(抽象类，无子类)
BufferedInputStream|BufferedReader
BufferedOutputstream|BufferedWriter
PrintStream|PrintWriter

过滤器还有读写基本数据类型的过滤器DataInputStream和DataOutputStream

非常适合文件有搜寻方法的RandomAccessFilem,同时具备读写基本数据类型和UTF-8字符能力。

### 常用的读写方法

InputStream或Reader都有`int read()`方法,OutputStream或Writer都有`void write(int)`。

基本读写操作:

1. 缓冲输入: BufferedReader的`String readLine()`(读取一行字符串，除去换行符'\n')
2. 数据格式化输入: DataInputStream、RandomAccessFile的`byte readByte()`、`String readUTF()`等
3. 打印输出:PrintWriter、PrintStream的`print()`、`println()`、`void printf()`的各种重载方法
4. 数据格式化输出:DataOutputStream、RandomAccessFile的`writeDouble(double)`、`writeUTF(String)`等

### RandomAccessFile类

其构造器第一个参数文件路径名,第二参数是"r"表示只读或"rw"读写。`long getFilePointer()`获得文件位置,`seek(long)`移动文件位置,`long length()`表示文件最大尺寸。它绝大功能已被nio存储映射文件取代。

## 标准I/O

简单说,在操作系统中的程序与系统的交互方式。程序的输入来自标准输入流,程序输出到标准输出流可以作为另一个程序的输入流,还有用于打印错误消息的标准错误流。System.in、System.out、System.err分别是标准输入流、标准输出流和标准错误流。System.out和System.err是PrintStream且已经被包装过了,所以可以直接使用。而System.in使用前必须进行包装

### 重定向

静态方法支持标准输入输出错误流重定向:`setOut(PrintStream)`、`setErr(PrintStream)`、`setIn(InputStream)` ,明显重定向操纵字节流。

#### 进程控制

操作系统非常见到Java程序调用另一个程序,还用控制当中的输入输出,输出是发送到控制台的。简单地可以使用`ProcessBuilder`类实现该操作。

用例:

```java
import java.io.*;
public class StartProcess {
    public static void main(String[] args) {
        Process process = 
            new ProcessBuilder(args.split(" "))
                .start();
        BufferedReader results = new BufferedReader(
            new InputStreamReader(process.getInputStream()));
        String s;
        System.out.println("output:");
        while((s = results.readLine()) != null) {
            System.out.println(s);
        }
        BufferedReader err = new BufferedReader(
            new InputStreamReader(process.getErrorStream()));
        System.out.println("error");
        while((s = readLine()) != null) {
            System.out.println(s);
        }
    }
}
```