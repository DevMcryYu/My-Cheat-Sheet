# 《Android 源码设计模式-解析与实战》读书笔记
> 读者：[DevMcryYu](https://github.com/DevMcryYu)  
> 最后更新于：2019-02-26

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

> 

### 5. 接口隔离原则（InterfaceSegregation Principale）
### 6. 迪米特/最少知识原则（Law of Demeter/Least Knowledge Principle）
