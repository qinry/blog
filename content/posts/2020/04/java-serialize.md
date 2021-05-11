---
title: "Java对象序列化"
date: 2020-04-28T16:01:24+08:00
categories: "笔记"
description: "Java编程思想读书笔记——序列化和反序列化"
tags: [  "笔记"]
---

## 持久性

如果对象能够在程序不运行还能保存其状态信息。可以通过将信息写入文件或数据库来达到程序下次执行时，对象重建后与上次拥有一致的信息。在Java中一切都是对象的思想下，把对象声明为“持久性”，可以省去一些细节，对程序员是种方便。对象的序列化，可以理解为把对象导入到文件中，通过`Serialize`接口实现。通过导入文件能够反序列化还原对象，达到持久性的目的。该特性支持计算机的远程方法调用，还有帮助Java Bean在设计阶段保存配置信息，并在启动程序后恢复配置信息。如果要把一个对象序列化，实现`Serialize`接口就可以了，此接口只是标记接口，不含方法。

## 序列化写入文件

```java
//: io/StoreCADState.java
// Saving the state of a pretend CAD system.
import java.io.*;
import java.util.*;

abstract class Shape implements Serializable {
    public static final int RED = 1, BLUE = 2, GREEN = 3;
    private int xPos, yPos, dimension;
    private static Random rand = new Random(47);
    private static int counter = 0;
    public abstract void setColor(int newColor);
    public abstract int getColor();
    public Shape(int xVal, int yVal, int dim) {
        xPos = xVal;
        yPos = yVal;
        dimension = dim;
    }
    public String toString() {
        return getClass() +
        "color[" + getColor() + "] xPos[" + xPos +
        "] yPos[" + yPos + "] dim[" + dimension + "]\n";
    }
    public static Shape randomFactory() {
        int xVal = rand.nextInt(100);
        int yVal = rand.nextInt(100);
        int dim = rand.nextInt(100);
        switch(counter++ % 3) {
        default:
        case 0: return new Circle(xVal, yVal, dim);
        case 1: return new Square(xVal, yVal, dim);
        case 2: return new Line(xVal, yVal, dim);
        }
    }
}

class Circle extends Shape {
    private static int color = RED;
    public Circle(int xVal, int yVal, int dim) {
        super(xVal, yVal, dim);
    }
    public void setColor(int newColor) { color = newColor; }
    public int getColor() { return color; }
}

class Square extends Shape {
    private static int color;
    public Square(int xVal, int yVal, int dim) {
        super(xVal, yVal, dim);
        color = RED;
    }
    public void setColor(int newColor) { color = newColor; }
    public int getColor() { return color; }
}

class Line extends Shape {
private static int color = RED;
public static void
serializeStaticState(ObjectOutputStream os)
throws IOException { os.writeInt(color); }
public static void
deserializeStaticState(ObjectInputStream os)
throws IOException { color = os.readInt(); }
public Line(int xVal, int yVal, int dim) {
    super(xVal, yVal, dim);
}
public void setColor(int newColor) { color = newColor; }
public int getColor() { return color; }
}

public class StoreCADState {
    public static void main(String[] args) throws Exception {
        List<Class<? extends Shape>> shapeTypes =
        new ArrayList<Class<? extends Shape>>();
        // Add references to the class objects:
        shapeTypes.add(Circle.class);
        shapeTypes.add(Square.class);
        shapeTypes.add(Line.class);
        List<Shape> shapes = new ArrayList<Shape>();
        // Make some shapes:
        for(int i = 0; i < 10; i++)
        shapes.add(Shape.randomFactory());
        // Set all the static colors to GREEN:
        for(int i = 0; i < 10; i++)
        ((Shape)shapes.get(i)).setColor(Shape.GREEN);
        // Save the state vector:
        ObjectOutputStream out = new ObjectOutputStream(
        new FileOutputStream("CADState.out"));
        out.writeObject(shapeTypes);
        Line.serializeStaticState(out);
        out.writeObject(shapes);
        // Display the shapes:
        System.out.println(shapes);
    }
}
```

ObjectOutputStream的`writeObject(Object)`该方法先检查传入的Serialize对象是否实现了`private writeObject(ObjectOutputStream) throws IOException`方法(可以在其内部调用OjbectOutputStream的`defaultWriteObject()`)有会自动调用，接着序列化到ObjectOutputStream关联的文件上,如果要序列化保存`static`域，要自己手动创建一个方法来实现操作，如上的serializeStaticState()方法,还要注意维护对象写入序列化文件的顺序。在有部分不需要序列化的域标记为`transient`即可,它要在构造器初始化;想全部不需要所有的域可以实现`Externalizable`(继承了Serialize，没有transient那么的灵活控制不想序列化的域)，必须实现`writeExternal(ObjectOutput) throws IOException`和`readExternal(ObjectInput) throws IOException, ClassNotFoundException`这两个方法。

Output:

    [class Circlecolor[3] xPos[58] yPos[55] dim[93]
    , class Squarecolor[3] xPos[61] yPos[61] dim[29]
    , class Linecolor[3] xPos[68] yPos[0] dim[22]
    , class Circlecolor[3] xPos[7] yPos[88] dim[28]
    , class Squarecolor[3] xPos[51] yPos[89] dim[9]
    , class Linecolor[3] xPos[78] yPos[98] dim[61]
    , class Circlecolor[3] xPos[20] yPos[58] dim[16]
    , class Squarecolor[3] xPos[40] yPos[11] dim[22]
    , class Linecolor[3] xPos[4] yPos[83] dim[6]
    , class Circlecolor[3] xPos[75] yPos[10] dim[42]
    ]

## 读取文件反序列化

```java
//: io/RecoverCADState.java
// Restoring the state of the pretend CAD system.
// {RunFirst: StoreCADState}
import java.io.*;
import java.util.*;

public class RecoverCADState {
    @SuppressWarnings("unchecked")
    public static void main(String[] args) throws Exception {
        ObjectInputStream in = new ObjectInputStream(
        new FileInputStream("CADState.out"));
        // Read in the same order they were written:
        List<Class<? extends Shape>> shapeTypes =
        (List<Class<? extends Shape>>)in.readObject();
        Line.deserializeStaticState(in);
        List<Shape> shapes = (List<Shape>)in.readObject();
        System.out.println(shapes);
    }
}
```

Output:

    [class Circlecolor[1] xPos[58] yPos[55] dim[93]
    , class Squarecolor[0] xPos[61] yPos[61] dim[29]
    , class Linecolor[3] xPos[68] yPos[0] dim[22]
    , class Circlecolor[1] xPos[7] yPos[88] dim[28]
    , class Squarecolor[0] xPos[51] yPos[89] dim[9]
    , class Linecolor[3] xPos[78] yPos[98] dim[61]
    , class Circlecolor[1] xPos[20] yPos[58] dim[16]
    , class Squarecolor[0] xPos[40] yPos[11] dim[22]
    , class Linecolor[3] xPos[4] yPos[83] dim[6]
    , class Circlecolor[1] xPos[75] yPos[10] dim[42]
    ]

ObjectInputStream的`readObject()`会检查恢复的Serialize对象是否实现`private readObject() throws IOException, ClassNotFoundException`，有则自动调用, 接着返回要恢复的对象，根据序列化的顺序一样的次序，反序列化，注意类型的转换，`static`域的恢复也是要手动实现。