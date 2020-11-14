---
title: "新I/O"
date: 2020-04-28T20:16:54+08:00
categories: "Java"
tags: [ "Java"]
---

通道是数据存储的地方，缓冲器充当着通道与外部数据交换的媒介

## 通道Channel

FileChannel可以通过FileOutputStream、FileInputStream、RandomAccessFile三个类的getChannel()获得，这些都是字节操纵流。在通道上有读写，访问通道大小,关闭通道，更改所在位置等操作。

FileChannel api:

- public  int read(ByteBuffer dst) throws IOException 通道读给缓冲器

- public  int write(ByteBuffer src) throws IOException 缓冲器数据写进通道

- public  long size() 通道上文件当前大小

- public  long position() throws IOException  通道的文件位置

- public void close() throws IOException 关闭通道

## 缓冲器Buffer

最基本的缓冲器ByteBuffer，可以从某个特定的基本数据类型的视窗查看底层的ByteBuffer。视窗缓冲器有CharBuffer、DoubleBuffer、IntBuffer等等，通过 **as数据类型Buffer()** 转换相应数据类型视图的Buffer

Buffer有四个索引：mark(标记)、position(位置)、limit(界限)、capacity(容量)

不同数据类型的Buffer api:

读写操作前的准备

- public final Buffer clear() 清理缓冲器，等待从通道读到数据

- public final Buffer flip() 将limit设置position值，position设置为0,mark丢弃，等待写数据到通道

---
关于Buffer索引的操作

- public final int capacity() 返回缓冲器的容量

- public final Buffer rewind() position设为0，mark丢弃

- public final Buffer mark() 将mark设置为postion值

- public final Buffer position(int newPosition) position设置newPostion值

- public final int position() 返回postion

- public final Buffer reset() postion设置mark值

- public final int remaining() 返回(limit-postion)

- public final boolean hasRemaining() position与limit之间有元素，返回true

---
不同的Buffer数据传到不同的数组

- 数据类型的数组 array() 可选的

- 数据类型的Buffer get(数据类型的数组)

- 数据类型的Buffer get(数据类型的数组, 起始位置, 长度) 读到数组相应的位置和长度

---

- public  数据类型 get() 返回position上的数据类型的值

- public  数据类型 get(int index) 返回绝对位置上的数据类型值

- public  数据类型的Buffer put(数据类型) 在position上加入数据类型的值

- public  数据类型的Buffer put(数据类型, int index) 在绝对位置上加入数据类型的值

- public  数据类型的Buffer put(数据类型的数组) 加入一些列数据类型的值

- public  数据类型的Buffer put(数据类型的Buffer) 加入一些列数据类型的值

- public  数据类型的Buffer put(数据类型的数组, 起始位置, 长度) 加入一些列数据类型的值

---
静态方法产生Buffer

- public static 数据类型的Buffer wrap(数据类型的数组)

- public static 数据类型的Buffer allocate(int capacity)

## 内存映射文件MappedByteBuffer

以下是创建MappedByteBuffer的方法，内存映射文件开一个创建和修改太大的文件，可以方便想数组一样访问文件，如put(byte data)加入数据，get(int index)访问数据，它继承ByteBuffer，拥有父类所有的方法。

```java
MappedByteBuffer buff =
    new RandomAccessFile(文件名, "rw").getChannel()
        .map(FileChannel.MapMode.READ_WRITE, POSITION, SIZE);
```

## 字符集CharSet

CharSet api:

- public static Charset forName(String charsetName) 返回字符集对象

- public final Set< String > aliases() 包含字符集的别名的集合

- public final CharBuffer decode(ByteBuffer bb) 解码 (ByteBuffer -> CharBuffer)

- public final ByteBuffer encode(CharBuffer cb)) 编码 (CharBuffer -> ByteBuffer)

- public abstract CharsetDecoder newDecoder()

- public abstract CharsetEncoder newEncoder()

---
CharsetDecoder

- public final CharBuffer decode(ByteBuffer bb) 解码 (ByteBuffer -> CharBuffer)

---
CharsetEncoder

- public final ByteBuffer encode(CharBuffer cb)) 编码 (CharBuffer -> ByteBuffer)

## 文件加锁FileLock

以下方式给文件加锁,release()可以释放suo

```java
FileOutputStream fos = new FileOutputStream(文件名);
FileLock fl = fos.tryLock();
if(fl != null)
    fl.release();
fos.close();
```

- public FileLock tryLock(long position, long size, boolean shared) 对文件部分加非阻塞(共享/独享)锁，还有阻塞式的lock(),二者区别是lock()一定要获取锁，那怕阻塞别的线程，tryLock()是尝试获取。

## 字节存放的次序

Big endian - 高位优先，最重要的字节存放到地址最低的存储单元(ByteOrder.BIG_ENDIAN), 常用在网络的数据传输

Little endian - 低位优先，最重要的字节存放到地址最高的存储单元(ByteOrder.LITTLE_ENDIAN)

- public final ByteBuffer order(ByteOrder bo) 编辑Buffer上的存放字节顺序

- public final ByteBuffer order() 默认高位优先的字节排序
