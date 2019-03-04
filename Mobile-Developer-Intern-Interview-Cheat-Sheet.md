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
使用一维数组结构进行存储的线性表。存取元素时间复杂度为 _O_(1)，（又称为随机存取结构）
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
- **栈顶**（Top）：允许操作的一端
- **栈底**（Bottom）：不允许操作的一端
- **入栈**（Push）：插入元素的操作
- **出栈**（Pop）：删除元素的操作
- **空栈**：无元素的栈

**应用**：
- 实现嵌套、递归调用。
- Android 中的返回栈。
- ···

## 队列
插入和删除的位置**分别在线性表的两端进行**，是为**队列**。特点是**先进后出**。  
- **入队**（Enqueue/Offer）：插入元素的操作
- **出队**（Dequeue/Poll）：删除元素的操作
- **队首**（Front）：允许出队的一端
- **队尾**（Rear）：允许入队的一端

### 链式队列与顺序队列的对比：
- 顺序队列**出队**效率低（一次操作需要改变两个下标），链式队列效率高（头插入时间复杂度为 _O_(1)）
- 链式队列**入队**效率低（尾插入时间复杂度为 _O_(n)），顺序队列效率高。使用循环双链表实现入出队时间复杂度同为 _O_(1)（典型的**空间换取时间**）
- 使用顺序表实现的顺序队列存在**假溢出**（数组仍有空间，但下标越界引起的溢出）现象。但循环顺序表实现的队列不存在这个问题

**应用**：
- 排队等待问题，进程调度问题
- Android 中消息队列循环

## 数组和广义表
**多维数组**：
[参考资料](https://www.cnblogs.com/gaobig/p/6126776.html)

**矩阵**：
三角、对称、对角矩阵的压缩存储：
[参考资料](https://blog.csdn.net/qq_29721419/article/details/70482392)
稀疏矩阵的压缩存储：
- 稀疏矩阵三元组单链表
- 稀疏矩阵行列单链表
- 稀疏矩阵十字链表

**广义表**：
**定义**：广义表是 n（n ≥ 0）个数据元素 а0，a1，···，an-1 组成的有限序列。其中 ai（0 ≤ i ≤ n）是原子或子广义表。
> 原子：不可分解的数据元素

## 树
定义：由 n 个结点组成的有限集合且:
- 有一个特殊的结点称为**根**（Root）结点，它只有后继，没有前驱。
- 除根节点之外的其他结点分为 m 个互不相交的集合。每个集合也具有树结构，称为根的**子树**（Subtree）

**度**（Degree）：结点所拥有的子树的棵树。度为 0 的结点称为叶子。
**层次**（Level）：根的层次为 1，其他结点依次是其服务节点的层次加 1；
**高度**（Depth）：树中结点的最大层次数。

### 二叉树
由一个**根结点**和两颗互不相交、分别称为**左、右子树**的子二叉树构成。

> 高度为 h 的二叉树中，最多有 2^n-1 个结点

#### 遍历：
- 先根遍历：root -> left -> right
- 中根遍历：left -> root -> right
- 后根遍历：left -> right -> root

```Java
// 先根递归遍历 中根、后根大同小异
public void preorder(){
  preorder(this.root);
  System.out.println();
}
public void preorder(BNode<T> p){
  if (p != null) {
    System.out.println(p.data.toString());
    preorder(p.left);
    preorder(p.right);
  }
}

// 先根非递归遍历
public void preorder(){
  LinkedStack<BNode<T>> stack = new LinkedStack<>();
  BNode<T> p = this.root;
  while(p != null || !stack.isEmpty()){
    if (p != null) {
      System.out.print(p.data.toString());
      stack.push(p);
      p = p.left;
    } else {
      System.out.print("∧");
      p = stack.pop();
      p = p.right;
    }
    System.out.println();
  }
}

```

#### Huffman 树
资料来源[百度百科](https://baike.baidu.com/link?url=xF-oJ8V9IUVKYCchDDjkKqnEx5K1re8LTarEa0OGoewVpDgkJSf_pRw9n4DuuzRwAoiX46XshHqdQN2f5BgZ3fvQ5FaZPG_cVddxyjNs1uNPZMuX9zLP0Tjp-0dwELUt)。

## 图
**图**（Graph）是由**顶点**（Vertex）集合及顶点间的关系集合组成的一种数据结构。顶点之间的关系称为**边**（Edge）
- 无向图
- 有向图
- 完全图：边数达到最大值
- 带权图：边带有**权值**（Weight），又称**网络**（Network）

### 图的表示
- 邻接矩阵：二维数组存储
- 邻接表：链式存储行的后继。
- 邻接多重表：链式存储行、列的后继，即矩阵十字链表

### 图的遍历
#### 深度优先搜索（DFS）
详见[中文维基](https://zh.wikipedia.org/wiki/%E6%B7%B1%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2)
#### 广度优先搜索（BFS）
详见[中文维基](https://zh.wikipedia.org/wiki/%E5%B9%BF%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2)

### 最小生成树
**定义**：最小生成树是一副连通加权无向图中一棵权值最小的生成树，是最小权重生成树的简称
**MST 性质**：假设 N = （V，{ E }）是一个连通网，U 是顶点集 V 的一个非空子集。若（u , v ）是一条具有最小权值（代价）的边，其中u ∈ U， v∈ V - U，则必存在一棵包含边（u，v）的最小生成树。

#### 最小生成树的构造算法
[Prim 算法](https://zh.wikipedia.org/wiki/%E6%99%AE%E6%9E%97%E5%A7%86%E7%AE%97%E6%B3%95)：逐步求解，从图中某个顶点开始，每步选择一条满足 MST 性质且权值最小的边来扩充最小生成树。

[Kruskal 算法](https://zh.wikipedia.org/wiki/%E5%85%8B%E9%B2%81%E6%96%AF%E5%85%8B%E5%B0%94%E6%BC%94%E7%AE%97%E6%B3%95)：从权值最小的边开始，如果这条边连接的两个节点于图中不在同一个连通分量中，则添加这条边到图中，直至图中所有的节点都在同一个连通分量中。

### 最短路径
#### 单源最短路径
[Dijkstra 算法](https://zh.wikipedia.org/wiki/%E6%88%B4%E5%85%8B%E6%96%AF%E7%89%B9%E6%8B%89%E7%AE%97%E6%B3%95)：逐步求解，每步将一条最短路径扩充一条边形成下一条最短路径；并将其他路径替换为更短的。

#### 顶点间最短路径
[Flyod 算法](https://zh.wikipedia.org/wiki/Floyd-Warshall%E7%AE%97%E6%B3%95)

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
