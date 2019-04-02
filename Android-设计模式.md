# 《Android 源码设计模式-解析与实战》读书笔记
> 读者：[DevMcryYu](https://github.com/DevMcryYu)  
> 最后更新于：2019-04-02

了解更多信息，详见[设计模式中文 Wiki](https://zh.wikipedia.org/wiki/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F_(%E8%AE%A1%E7%AE%97%E6%9C%BA))

## 第一章 面向对象的六大原则

### 1. 单一职责原则（Single Responsibility Principle）

> 就一个类而言，应该仅有一个引起它变化的原因

通俗地讲就是，一个类中应该是一组相关性很高的函数、数据的封装。

### 2. 开闭原则（Open Close Principle）

> 软件中的对象（类、模块、函数等）对于拓展是开放的，对于修改是封闭的

遵循开闭原则的最好手段就是抽象。如果每次添加新的功能时都要修改原有的方法，这会使原有方法逻辑越来越复杂。通过将功能抽象提取成接口，继承并实现，就可以达到拓展的目的而又不修改原有方法。

### 3. 里氏替换原则（Liskov Substitution Principle）

> 所有引用基类的地方必须能透明地使用其子类的对象

里氏替换依赖于继承、多态两大特性。只要是父类能出现的地方子类也可以出现并且不会产生错误。反之则不然。

### 4. 依赖倒置原则（Dependence Inversion Principle）

> 模块间的依赖通过抽象发生，实现类之间不发生直接的依赖关系，而是通过接口或抽象类产生的

面向接口编程、面向抽象编程。  
拓展：[依赖注入](https://zh.wikipedia.org/zh/%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)

### 5. 接口隔离原则（InterfaceSegregation Principale）

> 类间的依赖关系应建立在最小的接口上

更直观的说法就是让客户端依赖的接口尽可能得小，从而更容易重构、更改。

### 6. 迪米特/最少知识原则（Law of Demeter/Least Knowledge Principle）

> 一个对象应该对其他对象有最少的了解

类与类之间的关系越密切则耦合度越大，因此调用者与依赖者没必要了解其内部的具体实现。隐藏细节才能有效降低耦合性。

## 第二章 单例模式
> 👍：减少内存开支、性能开销；避免对资源的多重占用。
> 👎：一般没有接口，拓展困难；单例如果持有 Context 容易引发内存泄漏，此时需要注意传递给其的 Context 最好是 Application Context。  

**定义**：确保某一个类只有一个实例，且自行实例化并向整个系统提供这个实例。
**场景**：不需要创建多个对象、创建对象消耗的资源过多时。

### 单例的实现方式
- **懒汉方式**
```java
public class Singleton {
  private static Singleton instance;

  private Singleton() {
    // do something
  }

  public static synchronized getInstance() {
    if (instance == null)
      instance = new Singleton();
    return instance;
  }
}
```
懒汉方式下仅在第一次调用 `getInstance()` 方法时进行初始化。

- **饿汉方式**
```java
public class Singleton {
  private static final Singleton instance = new Singleton();

  private Singleton() {
    // do something
  }

  public static Singleton getInstance() {
    return instance;
  }
}
```
饿汉模式下则在声明静态对象时就已经初始化。

- **Double Check Lock（DCL）方式**
```java
public class Singleton {
  private volatile static Singleton instance = null;

  private Singleton() {
    // do something
  }

  public static Singleton getInstance() {
    if (instance == null){
      synchronized (Singleton.class)
        if (instance == null)
          instance = new Singleton();
    }
    return instance;
  }
}
```
DCL 方式实现的单例对 instance 进行两次判空，第一次是为了避免不必要的同步，第二次的判空才是为了在 null 的情况下创建实例。
- **静态内部类方式**
```java
public class Singleton {
  private Singleton() {
    // do something
  }

  public static Singleton getInstance() {
    return SingletonHolder.intance;
  }

  private static class SingletonHolder {
    private static final Singleton instance = new Singleton();
  }
}
```
第一次调用 `getInstance()` 时开始初始化，从而使虚拟机加载 SingletonHolder 类，这种方式不仅确保线程安全，也能够保证单例对象的唯一性，延迟了单例的实例化。所以是推荐使用的单例模式。
- **枚举方式**
```java
public enum SingltonEnum {
  INSTACNE;
  public void doSomething() {
    // do somethinga
  }
}
```
枚举单例最大优点是写法简单，而且默认枚举实例的创建是线程安全的。上述其它的单例模式在反序列化时会出现重新创建实例的情况。如果要避免这样的情况发生，那么必须加上如下方法：
```java
  private Object readResolve() throws ObjectStreamException {
    return instance;
  }
```
而枚举单例则不存在这个问题。
- **使用容器实现**
通过将多种单例类型注入到一个统一的管理类中，在使用时根据 key 获取对象对应类型的对象。从而管理多种类型的单例，还可以统一接口进行操作。代码省略。

### Android 源码中的运用
使用 `LayoutInflater.from(context)` 方法获取 LayoutInflater 服务  

## 第三章 Builder 模式
待整理
## 第四章 原型模式
待整理
## 第五章 工厂方法模式
待整理
## 第十二章 观察者模式
待整理
