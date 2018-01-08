[TOC]

### 什么是 GCD

GCD 全称为 Grand Central Dispatch，是 libdispatch 的市场名称，而 libdispatch 是 Apple 的一个库，其为并发代码在 iOS 和 OS X 的多核硬件上执行提供支持。确切地说 GCD 是一套低层级的 C API，通过GCD，开发者只需要向队列中添加一段代码块（ block 或 c 函数指针），而不需要直接和线程打交道。GCD 在后端管理着一个线程池，它不仅决定着你的代码块在哪个线程被执行，还根据可用的系统资源对这些线程进行管理。这样通过 GCD 来管理线程，从而解决线程被创建的问题。

GCD 是基于 pthread 和 kqueue 实现的。

### 基础概念

队列：

* 串行

  同一时间只能执行一个任务

* 并发

  同一时间可以有多个任务被执行

函数：

* 同步

  同步函数在完成了它预定的任务之后才会返回，在执行任务时会阻塞当前线程。

* 异步

  任务会完成，但是异步函数不会等任务完成再返回，执行任务时不会阻塞当前线程。

任务

* 要执行的函数

### Quality of Service( QoS )

这是 iOS8 之后提供的新功能，苹果提供了几个 Quality of Service 枚举来使用：user interactive，user initiated，utility 和 background。通过这些枚举，我们可以告诉系统我们在做什么工作，然后系统会通过合理的资源控制来最搞笑地执行任务代码，其中主要涉及到 CPU 调度的优先级、I/O 优先级、任务运行在哪个线程以及运行的顺序等等。

#### qos_class_t 枚举 

- QOS_CLASS_USER_INTERACTIVE

  与用户交互的任务，这些任务通常跟UI级别的刷新相关，比如动画，这些任务需要在一瞬间完成

- QOS_CLASS_USER_INITIATED

  由用户发起的并且需要立即得到结果的任务，比如滑动scroll view时去加载数据用于后续cell的显示，这些任务通常跟后续的用户交互相关，在几秒或者更短的时间内完成

- QOS_CLASS_DEFAULT

  优先级介于 user-initiated 和 utility，当没有 QoS信息时默认使用，开发者不应该使用这个值来设置自己的任务

- QOS_CLASS_UTILITY

  一些可能需要花点时间的任务，这些任务不需要马上返回结果，比如下载的任务，这些任务可能花费几秒或者几分钟的时间

- QOS_CLASS_BACKGROUND

  这些任务对用户不可见，比如后台进行备份的操作，这些任务可能需要较长的时间，几分钟甚至几个小时

- QOS_CLASS_UNSPECIFIED

  未指明的类型（未定义）

被设置成后台级别的队列，它会等待所有比它级别高的队列中的任务执行完或CPU空闲的时候才会执行自己的任务。例如磁盘的读写操作非常耗时，如果我们不需要立即获取到磁盘的数据，我们可以把读写任务放到后台队列中，这样读写任务只会在恰当的时候去执行而不会影响需要更改优先级的其他任务，整个程序也会更加有效率

### 一个主队列和四个全局并发队列

系统提供了一个主队列（串行）和四个全局并发队列，主队列的获取方法是：

```objective-c
dispatch_get_main_queue()
```

全局并发队列的获取方式差不多，只不过参数不同，通过以下方式可以获取：

```objective-c
dispatch_queue_t
dispatch_get_global_queue(long identifier, unsigned long flags);
```

队列的优先级：

* DISPATCH_QUEUE_PRIORITY_HIGH 	

  对应 QOS_CLASS_USER_INITIATED

* DISPATCH_QUEUE_PRIORITY_DEFAULT 

  对应 QOS_CLASS_DEFAULT

* DISPATCH_QUEUE_PRIORITY_LOW 

  对应 QOS_CLASS_UTILITY

* DISPATCH_QUEUE_PRIORITY_BACKGROUND 

  对应 QOS_CLASS_BACKGROUND

identifier 出可以传队列的优先级，从而获得不同优先级的全局并发队列，flags处是为将来考虑的，现在只要传0即可。

### 自定义队列

我们可以通过下面这个方法创建一个自定义队列

```objective-c
dispatch_queue_t
dispatch_queue_create(const char *_Nullable label,
		dispatch_queue_attr_t _Nullable attr);
```

label 处需要我们给队列定义名字，attr 处有两种用法：

* 直接传 DISPATCH_QUEUE_SERIAL 创建串行队列，直接传 DISPATCH_QUEUE_CONCURRENT 创建并发队列。

* 使用这个函数创建 attr 

  这个函数也是 iOS8 之后才有的，配合 qos_class_t 枚举使用

  attr 传 DISPATCH_QUEUE_SERIAL 或者 DISPATCH_QUEUE_CONCURRENT，qos_class 传 qos_class_t 枚举，relative_priority 是一个优先级的偏移量，该值必须小于 0 或者等于 QOS_MIN_RELATIVE_PRIORITY，否则此函数返回为空。

  [Apple官方文档链接](https://developer.apple.com/documentation/dispatch/1453028-dispatch_queue_attr_make_with_qo)

  ```objective-c
  dispatch_queue_attr_t
  dispatch_queue_attr_make_with_qos_class(dispatch_queue_attr_t _Nullable attr, dispatch_qos_class_t qos_class, int relative_priority);
  ```

### dispatch_once

dispatch_once_t 必须是全局或 static 变量，保证 dispatch_once_t 只有一份实例

经常用到单例中，例子：

```objective-c
+ (instancetype)shareInstance
{
    static SingletonObject *object;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        object = [self new];
    });
    return object;
}
```

如何重置单例呢？只需要下面这么做就好了：

```objective-c
#import "SingletonObject.h"

static SingletonObject *object;
static dispatch_once_t onceToken;

@implementation SingletonObject

+ (instancetype)shareInstance
{
    
    dispatch_once(&onceToken, ^{
        object = [self new];
    });
    return object;
}

+ (void)clearSingleton
{
    object = nil;
    onceToken = 0;
}

@end
```

### dispatch_barrier（栅栏函数）

Dispatch Barrier确保提交的闭包是指定队列中在特定时段唯一在执行的一个。在所有先于Dispatch Barrier的任务都完成的情况下这个闭包才开始执行。轮到这个闭包时barrier会执行这个闭包并且确保队列在此过程不会执行其它任务。闭包完成后队列恢复。

dispatch_barrier_async只在自己创建的队列上有这种作用，在全局并发队列和串行队列上，效果和dispatch_sync一样。而且 barrier 的队列和我们的任务队列要保持一致。

示例代码：

```objective-c
dispatch_queue_t queue = dispatch_queue_create("com.thinkive.customMaxinQueue", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(queue, ^{
    sleep(1);
    NSLog(@"111222333");
});
dispatch_async(queue, ^{
    sleep(1);
    NSLog(@"444555666");
});
    
dispatch_barrier_sync(queue, ^{
    sleep(2);
    NSLog(@"栅栏拦截");
});
dispatch_async(queue, ^{
    sleep(1);
    NSLog(@"after one");
});
    
dispatch_async(queue, ^{
    sleep(1);
    NSLog(@"after two");
});
```

控制台日志：

```objective-c
2017-06-26 17:22:49.997 QueueDemo[70061:1667579] 444555666
2017-06-26 17:22:49.997 QueueDemo[70061:1667581] 111222333
2017-06-26 17:22:51.998 QueueDemo[70061:1667464] 栅栏拦截
2017-06-26 17:22:53.002 QueueDemo[70061:1667581] after one
2017-06-26 17:22:53.002 QueueDemo[70061:1667579] after two
```

### Dispatch_group（调度组）

dispatch_group_t 是用来监视一组 block 的机制。

当 group 里所有事件都完成的时候，GCD API 有两种方式发送通知，第一种是dispatch_group_wait，会阻塞当前线程，要等待所有任务都完成或超过超时时间才会执行之后的操作。第二种方法是使用dispatch_group_notify，异步执行闭包，不会阻塞当前线程。

示例代码：

```objective-c
//创建一个自定义并发队列
dispatch_queue_t queue = dispatch_queue_create("com.thinkive.mxCustomQueue", DISPATCH_QUEUE_CONCURRENT);
//创建一个调度组
dispatch_group_t group = dispatch_group_create();
NSLog(@"begin");
//通知调度组监听下面的任务
dispatch_group_enter(group);
dispatch_async(queue, ^{
    sleep(1);
    NSLog(@"1");
    //通知调度组任务已经完成
    dispatch_group_leave(group);
});
dispatch_group_enter(group);
dispatch_async(queue, ^{
    sleep(4);
    NSLog(@"2");
    dispatch_group_leave(group);
});
//调度组中所有任务都已经完成
dispatch_group_notify(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    NSLog(@"一切都结束了");
});
//等待调度组执行任务，如果3秒内未执行完，取消等待，执行之后的任务。
dispatch_group_wait(group, dispatch_time(DISPATCH_TIME_NOW, 3*NSEC_PER_SEC));
NSLog(@"结束感言");
```

控制台日志：

```objective-c
2017-06-26 17:43:11.980 QueueDemo[70126:1690328] begin
2017-06-26 17:43:12.985 QueueDemo[70126:1690396] 1
2017-06-26 17:43:14.981 QueueDemo[70126:1690328] 结束感言
2017-06-26 17:43:15.986 QueueDemo[70126:1690377] 2
2017-06-26 17:43:15.986 QueueDemo[70126:1690377] 一切都结束了
```

注意事项

- dispatch_group_async等价于dispatch_group_enter() 和 dispatch_group_leave()的组合。
- dispatch_group_enter() 必须运行在 dispatch_group_leave() 之前。
- dispatch_group_enter() 和 dispatch_group_leave() 需要成对出现的

### Dispatch Semaphore （信号量）

dispatch semaphore 用来解决一些同步的问题，dispatch_semaphore_create会创建一个信号量，该函数需要传递一个信号值，dispatch_semaphore_signal 会使信号值加1，如果信号值的大小等于1，dispatch_semaphore_wait 会使信号值减1，并继续往下走，如果信号值为0，则等待。

示例代码：

```objective-c
//创建一个信号量，初始值为8
dispatch_semaphore_t semaphore = dispatch_semaphore_create(8);
NSLog(@"begin");
for (int i=0; i<10; i++) {
    //信号值-1
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        sleep(1);
        NSLog(@"第%d个", i+1);
        //信号值+1
        dispatch_semaphore_signal(semaphore);
    });
}
```

### 知识拓展

创建一个线程的主要成本包括构造内核空间（大约1KB，[wired memory](http://support.apple.com/kb/ht1342)），栈空间(默认主线程1MB，多线程512KB），注意栈空间必须是4KB的倍数，且最小是16KB，即`s>1024*4*i（i>=4）`，创建线程大约需要90ms的时间（由于底层内核的支持，Operation Objects可使用内核的常驻线程池里来节省创建时间）

### 参考链接

[GCD使用三部曲之：基本用法](http://www.cocoachina.com/ios/20170510/19232.html)

[iOS多线程--彻底学会多线程之『GCD](http://www.jianshu.com/p/2d57c72016c6)

[关于GCD开发的一些事儿](http://justsee.iteye.com/blog/2260591)

[多线程-GCD-iOS总结](https://mtry1.gitbooks.io/ios-summary/content/GCD.html)

