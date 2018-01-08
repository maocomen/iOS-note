[TOC]

## 开篇

线程 （ thread ）是构成进程的子单元，操作系统调度器对线程进行单独的调度。包括 GCD 和 NSOperation 在内的并发编程 API 都是基于线程构建的。NSThread 是 Objective-C 对 [^pthread] 的一个封装，这样对使用者而言就更面向对象一些，我们可以直接操控线程对象，非常直观和方便，但是，它的生命周期还是需要我们去手动管理。

线程：指代一个独立执行的代码路径

进程：指代一个可执行程序，可以包含多个线程

## 如何创建

NSThread 应该如何使用呢？我们如何去创建一个线程？

Objective-C中提供了几种创建 NSThread 的 API ：

```objective-c
//通过 block 的方式创建一个线程，线程会自动执行，不返回实例对象。
+ (void)detachNewThreadWithBlock:(void (^)(void))block API_AVAILABLE(macosx(10.12), ios(10.0), watchos(3.0), tvos(10.0));

//通过传入 target 和 selector 的方式创建一个线程，线程会自动执行，不返回实例对象。
+ (void)detachNewThreadSelector:(SEL)selector toTarget:(id)target withObject:(nullable id)argument;

//通过传入 target 和 selector 的方式创建一个线程，会返回实例对象，线程不会自动执行，需要调用 start 方法。
- (instancetype)initWithTarget:(id)target selector:(SEL)selector object:(nullable id)argument NS_AVAILABLE(10_5, 2_0);

//通过传入 block 的方式创建一个线程，会返回实例对象，线程不会自动执行，需要调用 start 方法。
- (instancetype)initWithBlock:(void (^)(void))block API_AVAILABLE(macosx(10.12), ios(10.0), watchos(3.0), tvos(10.0));

//创建并返回一个线程，线程不会自动执行，需要调用 start 方法。（用该方式创建的线程，没有入口函数，需要在子类里的 main 函数中实现）
- (instancetype)init NS_AVAILABLE(10_5, 2_0) NS_DESIGNATED_INITIALIZER;

/****NSObject (NSThreadPerformAdditions) NSObject分类*****/
//在主线程中执行一个 selector，wait 表示是否阻塞主线程，modes 中传入的是 runloop 的运行模式。默认为 Common modes
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(nullable id)arg waitUntilDone:(BOOL)wait modes:(nullable NSArray<NSString *> *)array;

- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(nullable id)arg waitUntilDone:(BOOL)wait;

//在指定的线程中执行一个 selector，该 selector 必须拥有一个 runloop，wait 表示是否阻塞指定线程，modes 中传入的是 runloop 的运行模式。默认为 default mode。
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(nullable id)arg waitUntilDone:(BOOL)wait modes:(nullable NSArray<NSString *> *)array NS_AVAILABLE(10_5, 2_0);

- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(nullable id)arg waitUntilDone:(BOOL)wait NS_AVAILABLE(10_5, 2_0);

//在后台执行
- (void)performSelectorInBackground:(SEL)aSelector withObject:(nullable id)arg NS_AVAILABLE(10_5, 2_0);
```

## 入口函数

必须创建一个 autoreleasepool，因为多线程不会自动创建，如果线程中需要创建大量的对象，可以在 Run Loop 的每次迭代出再创建一个 autoreleasepool

如果要捕获异常，可以在线程入口处使用 try{}catch{}

如果多线程处理一次即结束，则无需设置 Run Loop；如果要循环处理事件，则使用 Run Loop是最好的一种方式；如果线程中使用了定时器，则必须要启动 RunLoop

多线程的正常退出应该使用标志位来进行判断，让线程自然退出。

## 常用 API

```objective-c
//返回一个调用堆栈返回地址的数组 使用[NSThread callStackReturnAddresses]
@property (class, readonly, copy) NSArray<NSNumber *> *callStackReturnAddresses NS_AVAILABLE(10_5, 2_0);
//返回一个调用堆栈符号的数组 使用[NSThread callStackSymbols]
@property (class, readonly, copy) NSArray<NSString *> *callStackSymbols NS_AVAILABLE(10_6, 4_0);
//判断当前线程是否是主线程
@property (class, readonly) BOOL isMainThread NS_AVAILABLE(10_5, 2_0); 
//获取主线程
@property (class, readonly, strong) NSThread *mainThread NS_AVAILABLE(10_5, 2_0);
//应用程序是否是多线程的
+ (BOOL)isMultiThreaded;
//阻塞当前线程，直到到达给定的时间
+ (void)sleepUntilDate:(NSDate *)date;
//在给定的时间间隔内，睡眠当前线程
+ (void)sleepForTimeInterval:(NSTimeInterval)ti;
//终止当前线程
+ (void)exit;
//获取当前线程的优先级
+ (double)threadPriority;
//设置当前线程的优先级
+ (BOOL)setThreadPriority:(double)p;
//用枚举设置线程优先级，在线程启动之后设置不生效
@property NSQualityOfService qualityOfService NS_AVAILABLE(10_10, 8_0); // read-only after the thread is started
```

## 三个通知

```objective-c
//由当前线程派生出第一个其他线程时发送，一般一个线程只发送一次
FOUNDATION_EXPORT NSNotificationName const NSWillBecomeMultiThreadedNotification;
//这个通知目前没有实际意义，可以忽略
FOUNDATION_EXPORT NSNotificationName const NSDidBecomeSingleThreadedNotification;
//线程退出之前发送这个通知
FOUNDATION_EXPORT NSNotificationName const NSThreadWillExitNotification;
```



## NSThread 的使用注意事项

* 避免直接使用 NSThread，Apple 推荐使用性能更好的并发编程：GCD 和 Operation Queue
* 线程消耗了系统的宝贵资源，有些可能是 wired memory，应该合理分配任务到每个线程，保证长时间运行和运行成效（不能让一个线程长时间的处于锁的等待之类的操作）。可以去打断一个长时间处于空闲状态的线程，有助于减少内存的占用。
* 避免使用不是线程安全的共享资源，如果非使用不可，避免冲突最简单容易的方法是每个线程都使用一个副本！加锁／原子操作同样有性能损耗，所以把避免资源争夺放在首位可以同样得到高性能的程序。
* 对 UI 进行的所有操作都放在主线程中运行，这样就避免了处理窗口绘图和用户事件的同步问题。
* 如果想在程序退出的时候在多线程中保存数据，使用 POSIX API 创建非独立线程（可连接线程）及在 applicationShouldTerminate 中延迟退出。
* 关于异常处理，每一个线程都有自己的堆栈，且一个线程是捕捉不到其他线程的异常的，所以每一个线程都负责捕捉自己的异常，对异常的处理一般是高速其他线程当前线程发生了什么，然后可以选择继续执行，等待指示或者直接退出，如果捕捉异常失败，则可能引起进程的 crash 。
* 不到万不得已，不要使用函数去中断一个线程，让线程自然结束，强行中断可能会妨碍线程的自清理，发生内存泄漏或其他问题。
* 对于类库开发者而言，由于不知道使用者是不是在多线程中使用，只能假设应用程序的调用是在多线程中的，或者是在多线程中切换的，所以你应该总是在临界区进行加锁的处理。

## 参考链接

[iOS多线程编程](http://nsoio.github.io/2013/12/18/2013-12-18-multi-threading-programming-of-ios-part-1/)

[iOS多线程之NSThread](http://blog.devzeng.com/blog/ios-multithead-of-nsthread.html)

[小笨狼漫谈多线程：NSThread](http://ios.jobbole.com/83804/)

## 脚注

[^pthread]: **POSIX线程**（英语：POSIX Threads，常被缩写为Pthreads）是[POSIX](https://zh.wikipedia.org/wiki/POSIX)的[线程](https://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B)标准，定义了创建和操纵线程的一套[API](https://zh.wikipedia.org/wiki/Application_programming_interface)。