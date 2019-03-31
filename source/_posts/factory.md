---
title: 工厂模式|如何优雅的new对象
date: 2019-03-22 21:14:01
tags: Design
categories: 设计模式
---

![](http://posw9yxeh.bkt.clouddn.com/images/factory/factory-1.jpg)
<!-- more -->

## 写在前面

在面向对象编程（OOP）中，通过new去实例化一个对象是再正常不过的事情。但是每当new一个对象，后面都会跟一个具体类，这使得代码与具体类过度耦合，让我们陷入面向实现编程的漩涡里面。每当有新的需求变更，我们代码的扩展性就比较差。

手动new对象有哪些问题
+ 与具体类耦合，每当有新的变化，就必须改变代码（不符合对修改关闭）
+ 无法借助接口、多态的力量去隔离变化（与面向接口编程相违背）

如何才能优雅的new对象
+ 对扩展开放，应对后期的各种需求
+ 对修改关闭，对于新的需求，不改变原有的代码
+ 不关心构造对象的细节和复杂过程，轻松获取对象实例

可以通过工厂模式来构造我们想要的对象。

>工厂模式是一种常用的创建型设计模式，在基类中定义了创建对象的接口，让子类来决定实例化哪个类。工厂方法让一个类的实例化延迟到子类中进行。

工厂模式包括我们所熟知的简单工厂模式、工厂方法模式、抽象工厂工厂模式。其中简单工厂模式在一定程度上只算是一种编程思想，还谈不上设计模式。但它作为工厂方法模式和抽象工厂模式的基石，还是有必要深入了解一下。

## 造车案例来了解一下不同的工厂模式
1. 客户想要一辆奔驰车，客户需要奔驰车的设计图纸相应的汽车零件来自己制造。当客户想要悍马车，手上的设计图纸和汽车零件已经毫无用处，需要获取悍马车的设计图纸以及汽车零件（不符合对修改关闭）
2. 客户不需要自己生产汽车，由工厂来提供服务，客户需要什么车，工厂就生产什么车。但是每当客户有新的需求的时候，工厂就得想尽办法去找相关的图纸和零件来交付生产。---简单工厂模式（不符合对修改关闭）
3. 客户的需求太多，一个工厂完全应付不过来，所以工厂就开始建立新的场地，有些场地只生产宝马车，有些场地只生产悍马车，这样客户想要什么样的车就找对应的车工厂去提货。--- 工厂方法模式
4. 客户的需求逐渐升级，不同型号的车具有不同配置，所以每一个具体工厂应该具备生产不同型号车辆的能力，奥迪的工厂不仅能生产A4，也能生产A6、A8。所以一个工厂应该具备生产不同型号车辆的生产车间---抽象工厂模式

## 几种工厂模式的实现
### 简单工厂
>定义一个工厂类，根据传入的参数不同返回不同的实例。
```
public interface Shape {
    void draw();
}
```
```
public class RectShape implements Shape {
    @Override
    public void draw() {
        System.out.println("draw rectangle");
    }
}
```
```
public class CircleShape implements Shape {
    @Override
    public void draw() {
        System.out.println("draw circle");
    }
}
```
```
public class ShapeFactory {
    public static Shape getShape(String type){
        Shape shape = null;
        if("circle".equalsIgnoreCase(type)){
            shape = new CircleShape();
        }else if("rectangle".equalsIgnoreCase(type)){
            shape = new RectShape();
        }
        return shape;
    }

    public static void main(String[] args) {
        Shape circle = ShapeFactory.getShape("circle");
        circle.draw();

        Shape rectangle = ShapeFactory.getShape("rectangle");
        rectangle.draw();
    }
}
```
+ 应用场景：
   * 只有一个工厂类，对于创建对象不多的案例比较适合
   * 工厂类封装了对象的创建过程，客户端不需要关心对象的创建过程。

### 工厂方法
> 简单工厂的深入化， 通过创建不同的对象工厂来取代统一的工厂。让子类来决定哪一个类实例化，让一个类的实例化延迟到子类。

案例：有一个图片加载器，可以加载jpg、png、gif等图片格式，

```
/**
 * 图片加载器接口
 */
public interface Reader {
    void read();
}
```
```
/**
 *
 * gif 图片加载器
 */
public class GifReader implements Reader{
    @Override
    public void read() {
        System.out.println("gif picture reader");
    }
}
```
```
/**
 *
 * png 图片加载器
 */
public class PngReader implements Reader{
    @Override
    public void read() {
        System.out.println("png picture reader");
    }
}
```
```
/**
 *
 * jpg 图片加载器
 */
public class JpgReader implements Reader{
    @Override
    public void read() {
        System.out.println("jpg picture reader");
    }
}
```
```
/**
 * gif 图片加载器工厂
 */
public class GifReaderFactory implements ReaderFactory{
    @Override
    public Reader getReader() {
        return new GifReader();
    }
}
```
```
/**
 * jpg 图片加载器工厂
 */
public class JpgReaderFactory implements ReaderFactory{
    @Override
    public Reader getReader() {
        return new JpgReader();
    }
}
```
```
/**
 * png 图片加载器工厂
 */
public class PngReaderFactory implements ReaderFactory {
    @Override
    public Reader getReader() {
        return new PngReader();
    }
}
```
```
/**
 * 客户端
 */
public class Client {
    public static void main(String[] args) {
        GifReaderFactory gifReaderFactory = new GifReaderFactory();
        Reader gifReader = gifReaderFactory.getReader();
        gifReader.read();

        PngReaderFactory pngReaderFactory = new PngReaderFactory();
        Reader pngReader = pngReaderFactory.getReader();
        pngReader.read();

        JpgReaderFactory jpgReaderFactory = new JpgReaderFactory();
        Reader jpgReader = jpgReaderFactory.getReader();
        jpgReader.read();

    }
}
```
工厂方法模式优点
+ 良好的封装性。调用者需要创建一个产品对象，只需要知道产品工厂的类名就可以了，不要了解对象创建过程，降低模块间的耦合。
+ 良好的扩展性。当有新增产品类的需求变化，只要适当扩展一个工厂类就可以完成拥抱变化
+ 屏蔽产品类。产品类如何变化，调用者不需要关心---切换数据源（数据库从MySQL切换到Oracle,需要改动的只是切换一下驱动名称）
+ 典型的解耦框架。
   * 高层模块需要知道产品抽象类，不关心实现类---迪米特法则
   * 只依赖产品类的抽象 --- 依赖倒置原则
   * 产品子类替换产品父类 --- 里氏替换原则

应用场景
+ 所以生成对象的地方都可以使用工厂方法模式，但是要权衡增加工厂类进行管理带来的代码复杂度
+ 需要灵活、可扩展的框架是，可以采用工厂方法模式
+ 客户端不需要知道它所创建对象的类，只需要知道创建的工厂名就可以完成创建过程

### 抽象工厂
> 为创建一组相关或相互依赖的对象提供一个借口，而无需指定他们的具体类。

案例：设计一个兼容Android、ios、Wp三个操作系统的游戏，每个系统都有一套操作控制和界面控制器

```
/**
 * 界面控制器
 */
public interface UIController {
    void display();
}
```
```
/**
 * 操作控制器
 */
public interface OperationController {
    void control();
}
```
```
/**
 * wp ui controller
 */
public class WpUIController implements UIController {
    @Override
    public void display() {
        System.out.println("wp ui controller");
    }
}
```
```
/**
 * ios 界面控制器
 */
public class IosUIController implements UIController {
    @Override
    public void display() {
        System.out.println("ios ui controller");
    }
}
```
```
/**
 * 安卓界面控制器
 */
public class AndroidUIController implements UIController{
    @Override
    public void display() {
        System.out.println("android ui controller");
    }
}
```
```
/**
 * wp 操作控制器
 */
public class WpOperationController implements OperationController {
    @Override
    public void control() {
        System.out.println("wp operation controller");
    }
}
```
```
/**
 * ios 操作控制器
 */
public class IosOperationController implements OperationController {
    @Override
    public void control() {
        System.out.println("ios operation controller");
    }
}
```
```
/**
 * 安卓操作控制器
 */
public class AndroidOperationController implements OperationController{
    @Override
    public void control() {
        System.out.println("android opration controller");
    }
}
```
```
public interface SystemFactory {
    OperationController createOperationController();
    UIController createUIController();
}
```
```
/**
 * ios 工厂
 */
public class IosFactory implements SystemFactory {
    @Override
    public OperationController createOperationController() {
        return new IosOperationController();
    }

    @Override
    public UIController createUIController() {
        return new IosUIController();
    }
}
```
```
/**
 * wp 工厂
 */
public class WpFactory implements SystemFactory{
    @Override
    public OperationController createOperationController() {
        return new WpOperationController();
    }

    @Override
    public UIController createUIController() {
        return new WpUIController();
    }
}
```
```
/**
 * 安卓工厂
 */
public class AndroidFactory implements SystemFactory {
    @Override
    public OperationController createOperationController() {
        return new AndroidOperationController();
    }

    @Override
    public UIController createUIController() {
        return new AndroidUIController();
    }
}
```
```
public class Client {
    public static void main(String[] args) {
        AndroidFactory androidFactory = new AndroidFactory();
        OperationController androidOperation = androidFactory.createOperationController();
        androidOperation.control();

        UIController androidUI = androidFactory.createUIController();
        androidUI.display();

        IosFactory iosFactory = new IosFactory();
        OperationController iosOperation = iosFactory.createOperationController();
        iosOperation.control();

        UIController iosUI = iosFactory.createUIController();
        iosUI.display();

        WpFactory wpFactory = new WpFactory();
        OperationController wpOperation = wpFactory.createOperationController();
        wpOperation.control();

        UIController wpUI = wpFactory.createUIController();
        wpUI.display();
    }
}
```
优点
+ 封装性。高层模块不需要关心产品实现类

缺点
+ 产品族扩展困难,当要新增一个控制器，所有的实现类都要改变---违反开闭原则，改变了契约，所有与契约有关系的代码都要改变

应用场景
+ 不关心对象创建过程
+ 需要一组对象功能完成某种功能
+ 系统结构稳定，不会频繁的增加产品族功能，增加产品族功能就回修改原有代码，不符合开闭原则。

注意
产品族扩展困难(增加控制器)，但是产品扩展容易(增加小米系统的支持)，也就是纵向扩展困难，横向扩展容易，从产品横向扩展来说，抽象工厂模式是符合开闭原则的

参考文档

[https://juejin.im/entry/58f5e080b123db2fa2b3c4c6](https://juejin.im/entry/58f5e080b123db2fa2b3c4c6)