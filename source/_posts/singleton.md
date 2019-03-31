---
title: 关于单例模式的几种变体形式
date: 2019-03-22 21:14:01
tags: Design
categories: 设计模式
---

![](http://posw9yxeh.bkt.clouddn.com/static/images/singleton-1.jpg)
<!-- more -->

单例模式是一种简单的的设计模式。虽然简单，但是应用却是十分的广泛。所有有必要针对单例模式的知识点进行梳理。

### 单例模式

>确保类只有一个实例，并自动实例化向整个系统提供全局的访问点。从定义上可以推断出单例模式更倾向于节约资源，利于资源的重用从而节约时间。所以应用单例模式的对象应该具有以下特点：

+ 频繁地创建、销毁且性能无法优化
+ 消耗大量资源（读取配置、产生依赖对象），可以在应用启动时直接产生一个单例对象
+ 避免资源的多重占用 
+ 设置全局的访问点，优化和共享资源访问

### 应用场景

### 单例模式设计的几点考虑
+ 延迟加载
+ 线程安全
+ 并发性能
+ 序列化/反序列化安全


| name | 延迟加载 | 线程安全 | 并发性能 | 序列化/反序列化安全 |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| 饿汉式 | N | Y | Y | N |
| 懒汉式(不加锁) | Y | N | Y | N |
| 懒汉式(加锁) | Y | Y | N | N |
| DCL | Y | Y | Y | N |
| 静态内部类 | Y | Y | Y | N |
| 枚举 | N | Y | Y | Y |


### 单例模式实现
1. 饿汉式

类加载时完成初始化

- 类加载慢

- 获取对象速度快


```
public class Singleton {
    private static Singleton singleton = new Singleton();

    private Singleton(){}

    public static Singleton getInstance(){
        return singleton;
    }
}
```
2. 懒汉式

获取对象的时候完成初始化，实现懒加载。但是线程不安全，高并发下会出现多个实例。
```
public class Singleton {
    private static Singleton instance = null;

    private Singleton(){}

    public static Singleton getInstance(){
        if( instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```

3. 懒汉式（加锁）

线程安全的懒汉式单例模式。

```
public class SafeSingleton {

    private static SafeSingleton instance = null;

    private SafeSingleton(){}

    public static synchronized SafeSingleton getInstance(){
        if(instance == null){
            instance = new SafeSingleton();
        }

        return instance;
    }
}
```
4. 双重检查DCL

- synchronized同步代码块保证只创建一个对象。

- 条件判断使对象创建过后，不在进入同步块，减少锁的粒度。

- volatitle防止指令重排序(否则出现一个线程创建一般的对象呗另一个线程访问，造成空指针)。


```
public class Singleton {
    private volatile static Singleton instance;

    private Singleton(){}

    public static Singleton getInstance(){
        if(instance == null){
            synchronized (Singleton.class){
                if(instance == null){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```


4. 静态内部类

- 利用ClassLoader的机制保证了线程安全

- 相比饿汉式单例模式，实现懒加载，只有显示调用SingleHolder才会触发初始化。

```
public class Singleton {

    private static class SingletonHolder{

        private static final Singleton instance = new Singleton();

    }

    private Singleton(){}

    public static final Singleton getInstance(){
        return SingletonHolder.instance;
    }
}
```

5. 枚举

- 线程安全（虚拟机加载枚举的时候，会使用ClassLoader的loadClass方法，这个方法使用同步代码块保证线程安全）

* 解决反序列化破坏单例的问题:
   - 普通序列化过程中，通过反射调用默认构造函数来初始化对象，反序列化的对象是新建的
   - 枚举的反序列化不是通过构造函数实现，因此不会发生于由于反序列化导致的单例破坏问题


```
public enum Singleton {
    INSTANCE;

    public void getInstance(){

    }
}
```



### 如何设计优秀的单例模式

### 引申
1. 双重校验DCL为什么使用volatile
2. synchronized与volatile的区别



