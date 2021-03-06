# 《RxJava 2.x 实战》读书笔记
> 读者：[DevMcryYu](https://github.com/DevMcryYu)  
> 最后更新于：2019-03-20

[TOC]

## 1. RxJava 简介（2019-02-24）

### 1.1 编程思想：
- [面向对象编程（OOP）](https://zh.wikipedia.org/zh-hans/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1)
- [面向切面编程（AOP）](https://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E4%BE%A7%E9%9D%A2%E7%9A%84%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1)
- [响应式编程（Reactive Programming，RP）](https://zh.wikipedia.org/wiki/%E5%93%8D%E5%BA%94%E5%BC%8F%E7%BC%96%E7%A8%8B)：异步编程
- [函数式编程（Functional Programming，FP）](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B)：函数是“第一等公民”（Firstc Class）。可以作为参数传入另外一个函数，或是作为别的函数的返回值。
- [函数响应式编程（FRP）](https://en.wikipedia.org/wiki/Functional_reactive_programming)

### 1.2 Rx 的定义
> **Rx = Observables + LINQ + Schedulers**

Rx 是一个编程模型，目标是提供一致的编程接口，帮助开发者更方便的处理异步数据流。在[这里](http://reactivex.io)了解更多。
RxJava 是其在 JVM 上的一个实现。通过使用观察者序列来构建异步、基于事件的程序。它通过抽象的方式屏蔽了底层的多线程实现、同步、线程安全、并发数据结构、非阻塞 I/O 等逻辑。

## 2. RxJava 基础知识（2019-02-24）
RxJava 的使用通常需要三步：
- 创建 `Observable`（被观察者）
- 创建 `Observer`（观察者）
- 使用 `subscribe()`（订阅）

> Tips：只有使用了 `subscribe()` 方法，被观察者才会开始发送数据

### 2.1 RxJava 的五种观察者模式
| 类型 | 描述 |
| ------ | ------ |
| `Observable` | 能够发送 0 或 n 个数据，并以成功或错误事件终止 |
| `Flowable` | 能够发送 0 或 n 个数据,并以成功或是错误事件终止。支持背压，可以控制数据源发送的速度 |
| `Single` | 只发射单个数据或错误事件 |
| `Completable` | 从不发射数据，只处理 `onComplete()` 和 `onError()` 事件，可以看成 Rx 的 Runnable |
| `Maybe` | 能够发射 0 或者 1 个数据，要么成功，要么失败。类似于 `Optional` |

### 2.2 do 操作符
do 操作符可以给 Observable 的生命周期的各个阶段加上一系列的回调监听。

| 操作符 | 用途 |
| ------ | ------ |
| `doOnSubscribe()`| 一旦观察者订阅了 Observable 就会调用 |
| `doOnLifecycle()` | 在观察者订阅之后，设置是否取消订阅 |
| `doOnNext()` | 在 Observable 每发射一项数据就调用一次，`Consumer` 接受发射的数据项。用于在 `subscribe()` 之前对数据进行处理|
| `doOnEach()` | 在 Observable 每发射一项数据就调用一次，包括 `onNext()` 、`onError()` 和 `onComplete()` |
| `doAfterNext()` | 在 `onNext()` 之后执行 |
| `doOnComplete()` | 在正常终止调用 `onComplete()` 时调用 |
| `doFinally()` | 在 Observable 终止之后调用，无论正常终止还是异常终止，优先于 `doAfterTerminate()` 调用 |
| `doAfterTerminate()` | 注册一个 `Action` 当 Observable 调用 `onComplete()` 或 `onError()` 时触发 |

### 2.3 Hot Observable 和 Cold Observabale
**Hot Observable**：无论是否有观察者订阅，事件始终都会发生。与订阅者间关系为**一对多**。
**Cold Observable**： 只有观察者订阅后才开始执行发射数据流的代码。与 Observer 只能是**一对一**关系。
Observable 的 `just`、`create`、`range`、`fromXXX` 等操作符都能生成 Cold Observable。
#### Cold Observable 转换为 Hot Observable
- 使用 `publish` 操作符将 Observable 转换成 ConnectableObservable。

> Tips：生成的 ConnectableObservable 需要调用 `connect()` 才能真正执行

- 使用 Subject/Processer。二者作用相同，Processer 是 RxJava 2.x 新增的类，继承自 Flowable，因此支持背压（Back Presure），而 Subject 不支持。
Subject 既是 Observable 又是 Observer（Subscriber）。作为观察者，Subject 订阅 Cold Observable，同时也作为 Observable 转发或发送新的事件。从而实现 Cold Observable 到 Hot Observable 的转化。

> Subject 并不是线程安全的，如需要其线程安全，需调用 `toSerialized()`方法

#### Hot Observable 转换为 Cold Observable
- ConnectableObservable 的 `refCount` 操作符

```java
Observable<Long> observable = connectableObservable.refCount();
```

- Observable 的 `share` 操作符
`share` 操作符封装了 `publish().refCount()` 调用。可以在其源码中查看。

### 2.4 Flowable
Flowable 支持非阻塞的背压，同时实现 Reactive Streams 的 Publisher 接口。Flowable 所有的操作符强制支持背压。  
使用 Flowable 的场景：
- 处理以某种方式产生超过 10 KB 的元素
- 文件读取与分析
- 读取数据库记录，也是一个阻塞的和基于拉取的模式
- 网络 I/O 流
- 创建一个响应式非阻塞接口
### 2.5 Single、Completable 和 Maybe
- **Single**：只有 `onSuccess` 和 `onError` 事件。其中 `onSuccess` 用于发射数据，且只能发射一个数据，后面即使再发射数据也不会做任何处理。

> Tips：可以通过 toXXX 方法转换成 Observable、Flowable、Completeable 及 Maybe

- **Completable**：在创建后不会发送任何数据，只有 `onComplete` 和 `onError` 事件，同时 Completable 并没有 `map`、`flatMap` 等操作符。经常结合 `andThen` 操作符使用。

> Tips：可以通过 toXXX 方法转换成 Observable、Flowable、Single 及 Maybe

- **Maybe**：可以看作是 Single 和 Completable 的结合。和 Single 一样通过 `onSuccess` 方法发射数据。Maybe 也只能发射 0 或 1 个数据，即使发送多个，后面的数据也不会处理。在没有数据发射时，`subscribe` 会调用 MaybeObservable 的 `onComplete()`。如果有数据发射或调用了 `onError`，则不执行。

> Tips：可以通过 toXXX 方法转换成 Observable、Flowable 和 Single

### 2.6 Subject 和 Processor
#### Subject
（**Subject 并非线程安全的，使用时需谨慎。**）
- **AsyncSubject**：Observer 会接收其 `onComplete()` 之前的最后一个数据

> Tips: `subject.onComplete()` 必须调用才会开始发射数据。

- **BehaviorSubject**：Observer 会先接收其被订阅之前的最后一个数据，如果订阅之前没有发送任何数据，则会发送一个默认数据。

```java
BehaviorSubject<String> subject = BehaviorSubject.createDefault("DefaultSubject");
```

- **ReplaySubject**：发射所有来自原始 Observable 的数据给观察者，无论它们何时订阅。

> Tips：将 `create()` 改成 `createWithSize(1)`，表示只缓存订阅前最后发送的一条数据。

- **PublishSubject**：Observer 只接收其被订阅之后发送的数据。

#### Processor
在 RxJava 2.0 引入，它是一个接口，继承自 Subcriber、Publisher。支持背压，也是其与 Subject 的区别。

## 3. 创建操作符（2019-02-24）

| 操作符 | 用途 |
| ------ | ------ |
| `just()` | 将一个或多个对象转换成发射这些对象的一个 Observable |
| `from()` | 将一个 Iterable、一个 Future 或者一个数组转换成一个 Observable |
| `create()` | 使用一个函数从头创建一个 Observable |
| `defer()` | 只有当订阅者订阅时才创建 Observable，为每个订阅创建一个新的 Observable |
| `range()` | 创建一个发射指定范围的整数序列的 Observable |
| `interval()` | 创建一个按照给定的时间间隔发射整数序列的 Observable |
| `timer()` | 创建一个在给定延时之后发射单个数据的 Observable |
| `empty()` | 创建一个什么都不做直接通知完成的 Observable |
| `error()` | 创建一个什么都不做直接通知错误的 Observable |
| `nerver()` | 创建一个不发射任何数据的 Observable |


## 4. RxJava 的线程操作（2019-02-24）
### Scheduler
RxJava 对线程控制器的一个抽象，从而使用多线程来操作 RxJava。
| Scheduler | 作用 |
| ------ | ------|
| single | 使用定长为 1 的线程池（`new Scheduled Thread Pool(1)`），重复利用这个线程 |
| newThread | 每次都启用新线程，并在新线程中执行操作 |
| computation | 使用的固定的线程池（Fixed Scheduler Pool），大小为 CPU 核数，适用于 CPU 密集型计算 |
| io | 适合 I/O 操作（读写文件、读取数据库、网络信息交互等）所使用的 Scheduler。行为模式和 newThread() 差不多，区别在于 io() 的内部实现是用一个无数量上限的线程池，可以重用空闲线程，因此多数情况下，io() 比 newThread() 更有效率 |
| trampoline | 直接在当前线程运行，如果当前进程有其他任务正在执行，则会先暂停其他任务 |
| Schedulers.from | 将 java.util.concurrent.Executor 转换成一个调度器实例，即可以自定义一个 Executor 来作为调度器 |

### 线程调度
- `subscribeOn`：指定对数据的处理运行在特定的 Scheduler 上。多次执行只有第一次起作用。（RxJava 的链式操作中，数据的处理是自下而上的）
- `observeOn`：指定下游操作运行在特定的 Scheduler 上。多次执行则每次都会起作用。

### TestScheduler
一种特殊的非线程安全的专门用于测试的调度器，只有被调用了时间才会继续。因此允许手动推进虚拟时间。
- `advanceTimeTo`：将调度器的时间移动到特定时刻。
- `advanceTimeBy`：将调度器的时间按特定时刻移动。
- `TriggerActions`：执行计划中但是未启动的任务。

## 5. 变换操作符和过滤操作符（2019-03-20）
### 变换操作符
| 操作符 | 用途 |
| ------ | ------ |
| `map()` | 对序列的每一项都用一个函数来变换 Observable 发射的数据序列 |
| `flatMap()`、`concatMap()`、`flatMapIterable()` | 将 Observable 发射的数据集合变换为 Observables 集合，然后将这些 Observable 发射的数据平坦化的放进一个单独的 Observable 中 |
| `switchMap()` | 将 Observable 发射的数据集合变换为 Observables 集合，然后只发送这些 Observables 最近发射过的数据 |
| `scan()` | 对 Observable 发射的每一项数据应用一个函数，然后按照顺序依次发射每个值 |
| `groupBy()` | 将 Observable 拆分成 Observable 集合，将原始数据按 Key 分组，按组发送不同数据 |
| `buffer()` | 定期从 Observable 收集数据到一个集合，然后把这些数据集合打包发射 |
| `window()` | 定期将来自 Observable 的数据拆分成一些 Observable 窗口，然后发射这些窗口 |
| `cast()` | 在发射之前强制将 Observable 发射的所有数据转换为指定类型 |
