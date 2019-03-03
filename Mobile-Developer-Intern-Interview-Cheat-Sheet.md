# Android Intern Interview Cheat Sheet
> 作者：DevMcryYu
> 最后更新于：2019-03-03
----

# Java 篇
## 面向对象的三大特征：
- **封装**：将事物拥有的属性和动作隐藏起来，只保留特定的方法与外界联系。
- **继承**：从已有的类中派生出新的类（子类）继承父类的数据属性和行为，并能根据自己的需求扩展出新的行为。继承的用途不仅仅是代码重用，最重要的还是所谓**向上转型**，即**父类对象引用子类实例**。
- **多态**：在执行期间判断引用对象的实际类型，根据其实际的类型调用其相应的方法。**多态的基础是继承**。多态分为**编译时多态**（方法的重载）与**运行时多态**（只有在运行的时候才会知道引用变量所指向的具体实例对象）。

> 多态的特性：可替换性、可扩充性、接口性

## 虚拟机是如何实现多态的
动态绑定技术(dynamic binding)，执行期间判断所引用对象的实际类型，根据实际类型调用对应的方法。
## 接口的意义
- 规范
- 扩展
- 回调

## 静态变量和实例变量的区别
静态变量存储在方法区，属于类所有。实例变量存储在堆当中，其引用存在当前线程栈。

## java 创建对象的几种方式
- new 实例化
- 反射机制
- `clone` 方法
- `Serializeable` 序列化（Android 中 `Parcelable`）

## Object 中有哪些公共方法?
- `equals()`
- `clone()`
- `getClass()`
- `notify()`、`notifyAll()`、`wait()`
- `toString`

## Java 当中的四种引用
- **强引用**：如果一个对象具有强引用，它就不会被垃圾回收器回收。即使当前内存空间不足，JVM 也不会回收它，而是抛出 `OutOfMemoryError` 错误，使程序异常终止。如果想中断强引用和某个对象之间的关联，可以显式地将引用赋值为 null，这样一来的话，JVM 在合适的时间就会回收该对象。

- **软引用**：在使用软引用时，如果内存的空间足够，软引用就能继续被使用，而不会被垃圾回收器回收，只有在内存不足时，软引用才会被垃圾回收器回收。

- **弱引用**：具有弱引用的对象拥有的生命周期更短暂。因为当 JVM 进行垃圾回收，一旦发现弱引用对象，无论当前内存空间是否充足，都会将弱引用回收。不过由于垃圾回收器是一个优先级较低的线程，所以并不一定能迅速发现弱引用对象。

- **虚引用**：顾名思义，就是形同虚设，如果一个对象仅持有虚引用，那么它相当于没有引用，在任何时候都可能被垃圾回收器回收。

## final有哪些用法
1. 被 final 修饰的类不可以被继承
2. 被 final 修饰的方法不可以被重写
3. 被 final 修饰的变量不可以被改变。如果修饰引用，那么表示引用不可变，引用指向的内容可变。
4. 被 final 修饰的方法，JVM 会尝试将其内联，以提高运行效率
5. 被 final 修饰的常量，在编译阶段会存入常量池中。

## 如何判断一个对象是否应该被回收
- 引用计数法
- 可达性分析算法

## 进程、线程、多线程
**进程** 是程序运行和资源分配的基本单位，一个程序至少有一个进程，一个进程至少有一个线程。进程在执行过程中拥有独立的内存单元，而多个线程共享内存资源，减少切换次数，从而效率更高。
**线程** 是进程的一个实体，是 CPU 调度和分派的基本单位，是比程序更小的能独立运行的基本单位。同一进程中的多个线程之间可以并发执行。  

多线程的实现：  
- 继承 Thread
- 实现 `Runnable` 接口
- 实现 `Callable` 接口
- 线程池 ThreadPool

----

# 数据结构
## 线性表  
### 顺序表
使用一维数组结构进行存储的线性表。存取元素时间复杂度为 O(1)，（又称为随机存取结构）
- 特点：存储单元地址连续，通过下标识别元素。

### 链式表
用若干地址分散的存储单元存储数据元素，逻辑相邻的元素物理位置上不一定相邻。链式表的存储单元称为结点（node），结点至少包含一个**数据域**与一个**地址域**。分别用来存储**数据元素**和**地址域存储前驱或后继元素地址**。

#### 单链表
遍历操作：
```Java
Node<T> current = head;

while(current != null){
  doSomething();
  current = current.next;
}
```
插入操作：
```Java
// 空插入或头插入
head = new Node<>(x,head);

// 中间插入
precursor.next = new Node<>(x,precursor.next);
```

删除操作：
```Java
// 头删除
head = head.next;
// 中间或尾删除
if(precursor.next != null)
  precursor.next = precursor.next.next;
```

翻转操作：
```Java
while(current != null){
  current.next = precursor; // current.next 结点指向 precursor
  // 依次向后移动
  precursor = current;
  current = succeed;
  succeed = succeed.next;
}
```

**循环单链表**：单链表最后一个结点的后继结点指向头结点。

#### 双链表
在单链表的结点结构上增加一个地址域，使其指向前驱结点。

**循环双链表**：双链表的最后一个结点的后继结点指向头结点，头结点的前驱结点指向最后一个结点。

## 串
### 串的模式匹配
定义：在目标串中查找与模式串相等的一个子串并确定该子串位置的操作。
#### Brute-Force 算法
简介：暴力算法。从目标串的首位开始与模式串按位进行比较。如不匹配，则从目标串的下一位开始重新与模式串按位比较。直到目标串末位停止。
缺点：算法回溯，没有利用前一次匹配的比较结果，算法中存在极多的重复比较，导致时间效率不高。
#### KMP 算法
简介：无回溯算法，利用计算得出的**部分匹配表**，当匹配失败时，跳过相应的位数然后再进行比较，从而提高算法效率。详见[这里](https://zh.wikipedia.org/wiki/%E5%85%8B%E5%8A%AA%E6%96%AF-%E8%8E%AB%E9%87%8C%E6%96%AF-%E6%99%AE%E6%8B%89%E7%89%B9%E7%AE%97%E6%B3%95)。
## 栈
插入和删除的位置**只允许在线性表的一端进行**，是为**栈**。栈是一种特殊的线性表，特点是**先进先出**。
## 队列
插入和删除的位置**分别在线性表的两端进行**，是为**队列**。特点是**先进后出**。
## 树
## 图
## 查找算法
## 排序算法

----

# Android 篇
## Android 四大基本组件
- Activity
- Service
- BroadcastReceiver
- ContentProvider

## Activity
### 生命周期
详见 [The Android Lifecycle cheat sheet](https://medium.com/androiddevelopers/the-android-lifecycle-cheat-sheet-part-i-single-activities-e49fd3d202ab)

### 启动模式
- Standard：默认的启动模式。**每次启动一个 Activity 都会又一次创建一个新的实例入栈，无论这个实例是否存在**
- SingleTop：要创建的 Activity 已经处于栈顶时，此时会**直接复用栈顶的 Activity**。不会再创建新的 Activity；若须要创建的 Activity 不处于栈顶，此时**创建一个新的 Activity 入栈**
- SingleTask：要创建的Activity已经处于栈中时，此时不会创建新的 Activity，而是将**栈中的 Activity 上面的其他 Activity 都销毁，使它成为栈顶**。
- SingleInstance：全局单例模式，只要**系统中创建过此 Activity 的实例便不再创建**。具有此模式的 Activity 单独位于一个任务栈中。这个经常使用于系统中的应用，比如 Launcher

### 使用方法
- 在 `AndroidMainfest.xml` 中指定 Activity 启动模式

```xml
<activity android:name=".MainActivity" android:launchMode="singleTask"/>
```

- 启动 Activity 时，在 `Intent` 中指定启动模式去创建 Activity

```Java
Intent intent = new Intent();
       intent.setClass(context, MainActivity.class);
       intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
       context.startActivity(intent);
```

差别：
- 优先级：动态指定方式比第一种优先级要高，若两者同时存在，以动态指定方式为准。
- 限定范围：AndroidMainfest 方式无法为 Activity 直接指定 `FLAG_ACTIVITY_CLEAR_TOP` 标识，Intent 方式无法为 Activity 指定 **SingleInstance** 模式。

> **Activity 的 FLAG**
> - `FLAG_ACTIVITY_NEW_TASK` 指定 **SingleTask** 启动模式。与 AndroidMainfest.xml 指定效果同样
> - `FLAG_ACTIVITY_SINGLE_TOP` 指定 **SingleTop** 启动模式，与 AndroidMainfest.xml 指定效果同样
> - `FLAG_ACTIVITY_CLEAN_TOP` 具有此标记位的 Activity，启动时会将位于其上的 Activity 出栈。一般与 **SingleTask** 启动模式一起出现。但事实上 **SingleTask** 启动模式默认具有此标记
> - `FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS` 具有此标记位的 Activity 不会出现在历史 Activity 的列表中，它等同于在 xml 中的属性：`android:excludeFromRecents="true"`

### 显、隐式 Intent 启动 Activity
**显式 Intent**

```Java
// 第一种方式
Intent intent = new Intent(this, SecondActivity.class);  
startActivity(intent);

//第二种方式
ComponentName componentName = new ComponentName(this, SecondActivity.class);  
// 或者ComponentName componentName = new ComponentName(this, "devmcryyu.SecondActivity");  
// 或者ComponentName componentName = new ComponentName(this.getPackageName(), "com.example.SecondActivity");  
Intent intent = new Intent();  
intent.setComponent(componentName);  
startActivity(intent);

//第三种方式
Intent intent = new Intent();  
intent.setClass(this, SecondActivity.class);  
// 或者intent.setClassName(this, "devmcryyu.SecondActivity");  
// 或者intent.setClassName(this.getPackageName(), "devmcryyu.SecondActivity");        
startActivity(intent);  
```

**隐式 Intent**

首先在 AndroidManifest.xml 中为 Activity 添加 `intent-filter` 标签
```xml
<activity  
    android:name="devmcryyu.SecondActivity">  
    <intent-filter>  
        <action android:name="action_name"/>  
        <category android:name="android.intent.category.DEFAULT"/>  
    </intent-filter>  
</activity>
```
然后在 Activity 中调用
```Java
Intent intent = new Intent();  
intent.setAction("action_name");  
//或者直接 Intent intent = new Intent("action_name");  
startActivity(intent);
```
通过设置 Action 的值表明意图，然后由系统解析并找到能够处理此 Intent 的 Activity 启动。
