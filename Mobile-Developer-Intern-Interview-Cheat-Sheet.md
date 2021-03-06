# Android Intern Interview Cheat Sheet
> 作者：DevMcryYu
> 最后更新于：2019-03-04
----

[TOC]

# Java 篇
## 面向对象的三大特征：
- **封装**：将事物拥有的属性和动作隐藏起来，只保留特定的方法与外界联系。
- **继承**：从已有的类中派生出新的类（子类）继承父类的数据属性和行为，并能根据自己的需求扩展出新的行为。继承的用途不仅仅是代码重用，最重要的还是所谓**向上转型**，即**父类对象引用子类实例**。
- **多态**：在执行期间判断引用对象的实际类型，根据其实际的类型调用其相应的方法。**多态的基础是继承**。多态分为**编译时多态**（方法的重载）与**运行时多态**（只有在运行的时候才会知道引用变量所指向的具体实例对象）。

> 多态的特性：可替换性、可扩充性、接口性

## 使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变？
使用final关键字修饰一个变量时，是指引用变量不能变，引用变量所指向的对象中的内容 还是可以改变的。

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

## 线程的几大状态及其常用方法
- 初始态（NEW）  
实现 Runnable 接口和继承 Thread 可以得到一个线程类，new 一个实例出来，线程就进入了初始状态。

- 运行态（RUNNABLE）  
  - 就绪态（READY）  
  只是说你资格运行，调度程序没有挑选到你，你就永远是就绪状态。调用线程的 `start()` 方法，此线程进入就绪状态。当前线程 `sleep()` 方法结束，其他线程 `join()` 结束，等待用户输入完毕，某个线程拿到对象锁，这些线程也将进入就绪状态。当前线程时间片用完了，调用当前线程的 `yield()` 方法，当前线程进入就绪状态。锁池里的线程拿到对象锁后，进入就绪状态。

  - 运行中态（RUNNING）  
  线程调度程序从可运行池中选择一个线程作为当前线程时线程所处的状态。这也是线程进入运行状态的唯一一种方式。

- 阻塞态（BLOCKED） 
阻塞状态是线程阻塞在进入 synchronized 关键字修饰的方法或代码块（获取锁）时的状态。

- 等待态（WAITING）  
处于这种状态的线程不会被分配 CPU 执行时间，它们要等待被显式地唤醒，否则会处于无限期等待的状态。

- 超时等待（TIMED_WAITING） 
处于这种状态的线程不会被分配 CPU 执行时间，不过无须无限期等待被其他线程显示地唤醒，在达到一定时间后它们会自动唤醒。

- 终止态（TERMINATED） 
当线程的 `run()` 方法完成时，或者主线程的 `main()` 方法完成时，我们就认为它终止了。这个线程对象也许是活的，但是，它已经不是一个单独执行的线程。线程一旦终止了，就不能复生。
在一个终止的线程上调用 `start()` 方法，会抛出 java.lang.IllegalThreadStateException 异常。

![](https://github.com/DevMcryYu/My-Cheat-Sheet/blob/master/Image/java-thread.png)

## 多线程的实现：  
- 继承 Thread  
  - 定义 Thread 类的子类，并重写该类的 `run()` 方法，该 `run()` 方法的方法体就代表了线程要完成的任务。因此把 `run()` 方法称为执行体。
  - 创建 Thread 子类的实例，即创建了线程对象。
  - 调用线程对象的 `start()` 方法来启动该线程。
- 实现 `Runnable` 接口  
  - 定义 Runnable 接口的实现类，并重写该接口的 `run()` 方法，该 `run()` 方法的方法体同样是该线程的线程执行体。
  - 创建 Runnable 实现类的实例，并依此实例作为 Thread 的 target 来创建 Thread 对象，该 Thread 对象才是真正的线程对象。
  - 调用线程对象的 `start()` 方法来启动该线程。
- 实现 `Callable` 接口  
  - 创建 Callable 接口的实现类，并实现 `call()` 方法，该 `call()` 方法将作为线程执行体，并且有返回值。
  - 创建 Callable 实现类的实例，使用 FutureTask 类来包装 Callable 对象，该 FutureTask 对象封装了该 Callable 对象的 `call()` 方法的返回值。
  - 使用 FutureTask 对象作为 Thread 对象的 target 创建并启动新线程。
  - 调用 FutureTask 对象的 `get()` 方法来获得子线程执行结束后的返回值。
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
### 二分法查找
**分治策略**，采用二分法查找的条件是：顺序存储、数据元素排序
```Java
while(begin <= end){
  int mid = (begin + end)/2;
  if(key.compareTo(value[mid]) == 0)
    return mid;
  if(key.compareTo(value[mid]) < 0)
    end = mid - 1;
  else
    begin = mid + 1;
}
return -1;
```

### 基于索引表的分块查找
建立**索引**（Index）也是一种有效的分治策略。为存储数据元素的主表创建索引表，其中的索引项（Index Item）保存元素的关键字信息及存储位置。**以空间换取时间**
- 完全索引表：保存所有元素的索引信息
- 不完全索引：保存部分元素的索引信息

### 散列表
**散列**（Hash）表是一种按照关键字编址的存储和查找技术，散列表根据元素的关键字来确定元素的存储位置。其操作效率接近于 _O_(1)。
- 设计散列函数：建立一种从**元素关键字**到**元素存储位置**的映射关系
- 解决冲突：两个关键字的散列地址相同，即**不同关键字的多个元素映射到同一个存储位置**，则为**同义词冲突**。

#### 解决冲突的两种方法
- 开放定址法：发生冲突时，将元素存储到下一个空的地址。会产生**非同义词冲突**。也破坏了散列函数的规则。
- 链地址法：用链表存储发生同义词冲突的元素

### 散列映射
散列映射（Hash Map）是指使用散列表存储元素实现的映射，关键字没有次序。

### 二叉排序树
[中文维基](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E6%90%9C%E5%B0%8B%E6%A8%B9)
**性质**：
- 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值
- 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值
- 任意节点的左、右子树也分别为二叉排序树
- 没有键值相等的节点

### 二叉平衡树
[平衡二叉树](https://zh.wikipedia.org/wiki/%E5%B9%B3%E8%A1%A1%E4%BA%8C%E5%85%83%E6%90%9C%E5%B0%8B%E6%A8%B9)：是一种结构平衡的二叉搜索树，即叶节点高度差的绝对值不超过 1，并且左右两个子树都是一棵平衡二叉树。它能在 _O_(_log_ n) 内完成插入、查找和删除操作。

常见的平衡二叉搜索树有：
- [AVL 树](https://zh.wikipedia.org/wiki/AVL%E6%A0%91)：任一节点对应的两棵子树的最大高度差为 1 ，因此它也被称为高度平衡树。
- [**黑红树**](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)：红黑树是每个节点都带有颜色属性的二叉查找树，颜色为红色或黑色。在二叉查找树强制一般要求以外，对于任何有效的红黑树我们增加了如下的额外要求：
  - 节点是红色或黑色。
  - 根是黑色。
  - 所有叶子都是黑色（叶子是 NIL 节点）。
  - 每个红色节点必须有两个黑色的子节点。（从每个叶子到根的所有路径上不能有两个连续的红色节点。）
  - 从任一节点到其每个叶子的所有简单路径都包含相同数目的黑色节点。
- [Treap](https://zh.wikipedia.org/wiki/%E6%A0%91%E5%A0%86)：是有一个随机附加域满足堆的性质的二叉搜索树，其结构相当于以随机数据插入的二叉搜索树。
- [结点大小平衡树]

## 排序算法
**稳定性**：不稳定排序算法可能会在相等的键值中改变纪录的相对次序，但是稳定排序算法从来不会如此。

### 插入排序
对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

#### [**直接插入排序**](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95)  
![直接插入排序动画演示](https://upload.wikimedia.org/wikipedia/commons/2/25/Insertion_sort_animation.gif)

```Java
public void insertionSort(int[] array) {
		for (int i = 1; i < array.length; i++) {
			int key = array[i];
			int j = i - 1;
			while (j >= 0 && array[j] > key) {
				array[j + 1] = array[j];
				j--;
			}
			array[j + 1] = key;
		}
	}
```

#### [**希尔排序**](https://zh.wikipedia.org/wiki/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F)  
![希尔排序动画演示](https://upload.wikimedia.org/wikipedia/commons/d/d8/Sorting_shellsort_anim.gif)

又称递减增量排序算法，是插入排序的一种更高效的改进版本。希尔排序是非稳定排序算法。

```Java
public static void shellSort(int[] array) {
    int number = array.length / 2;
    int i;
    int j;
    int temp;
    while (number >= 1) {
        for (i = number; i < array.length; i++) {
            temp = array[i];
            j = i - number;
            while (j >= 0 && array[j] < temp) { //需要注意的是，这里array[j] < temp将会使数组从大到小排序。
                array[j + number] = array[j];
                j = j - number;
            }
            array[j + number] = temp;
        }
        number = number / 2;
    }
}
```

### 交换排序
#### [**冒泡排序**](https://zh.wikipedia.org/wiki/%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F)  
![冒泡排序动画演示](https://upload.wikimedia.org/wikipedia/commons/3/37/Bubble_sort_animation.gif)

```Java
public static void bubbleSort(int[] arr) {
    boolean exchange = true;
    for (int i = 1 ;i < keys.length && exchange ;i++) {
      exchange = false;
      for (int j = 0 ; j < keys.length ; j++) {
        if (keys[j] > keys[j+1]) {
          int temp = keys[j];
          keys[j] = keys[i];
          keys[i] = temp;
          exchange = true;
        }
      }
    }
  }
```

#### [**快速排序**](https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F)  
![快速排序动画演示](https://upload.wikimedia.org/wikipedia/commons/6/6a/Sorting_quicksort_anim.gif)

```Java
//递归实现
private static void quickSort(int[] keys,int begin,int end){
  if (begin >= 0 && begin < keys.length && end >= 0 && end < keys.length && begin < end) {
    int i = begin , j = end;
    int vot = keys[i];
    while(i!=j){
      while(i<j && keys[j] >= vot)
        j--;
      if (i < j)
        keys[i++] = keys[j];
      while(i < j && keys[i] <= vot)
        i++;
      if (i < j)
      keys[j--]=keys[i];
    }
    keys[i] = vot;
    quickSort(keys,begin,j-1);
    quickSort(keys,i+1,end);
  }
}
```

### 选择排序
#### [**直接选择排序**](https://zh.wikipedia.org/wiki/%E9%80%89%E6%8B%A9%E6%8E%92%E5%BA%8F)
![直接选择排序动画演示](https://upload.wikimedia.org/wikipedia/commons/b/b0/Selection_sort_animation.gif)

首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

```Java
public static void selectionSort(int[] arr) {
	    int min, temp;
        for (int i = 0; i < arr.length; i++) {
            // 初始化未排序序列中最小数据数组下标
            min = i;

            for (int j = i+1; j < arr.length; j++) {
                // 在未排序元素中继续寻找最小元素，并保存其下标
                if (arr[j] < arr[min]) {
                    min = j;
                }
            }

            // 将未排序列中最小元素放到已排序列末尾
            temp = arr[min];
            arr[min] = arr[i];
            arr[i] = temp;
        }
    }
```

#### [**堆排序**](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%8E%92%E5%BA%8F)
![堆排序动画演示](https://upload.wikimedia.org/wikipedia/commons/1/1b/Sorting_heapsort_anim.gif)

利用堆这种数据结构所设计的一种排序算法。堆是一个近似完全二叉树的结构，并同时满足堆积的性质：即子节点的键值或索引总是小于（或者大于）它的父节点。

### [**归并排序**](https://zh.wikipedia.org/wiki/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F)
![归并排序动画演示](https://upload.wikimedia.org/wikipedia/commons/c/c5/Merge_sort_animation2.gif)

```Java
static void merge_sort_recursive(int[] arr, int[] result, int start, int end) {
	if (start >= end)
		return;
	int len = end - start, mid = (len >> 1) + start;
	int start1 = start, end1 = mid;
	int start2 = mid + 1, end2 = end;
	merge_sort_recursive(arr, result, start1, end1);
	merge_sort_recursive(arr, result, start2, end2);
	int k = start;
	while (start1 <= end1 && start2 <= end2)
		result[k++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
	while (start1 <= end1)
		result[k++] = arr[start1++];
	while (start2 <= end2)
		result[k++] = arr[start2++];
	for (k = start; k <= end; k++)
		arr[k] = result[k];
}
```

### 横向对比
![](https://github.com/DevMcryYu/My-Cheat-Sheet/blob/master/Image/MDIIC-sort.png)

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
