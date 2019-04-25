[TOC]

# OC 中的五大方法家族

Objective-C 中有五大方法家族，每个家族都有自己的规则。`alloc/new/copy/mutableCopy` 四个家族返回的都是 `"a retainable object pointer"` ，而 `init` 家族要求必须是实例方法，必须返回 Objective-C 对象。

* alloc

   must return a retainable object pointer type

* new

   must return a retainable object pointer type

* copy

   must return a retainable object pointer type

* mutableCopy

   must return a retainable object pointer type

* init

  must be instance methods and must return an Objective-C pointer type.Additionally, a program is ill-formed if it declares or contains a call to an `init` method whose return type is neither `id` nor a pointer to a super-class or sub-class of the declaring class (if the method was declared on a class) or the static receiver type of the call (if it was declared on a protocol).

  >init 家族必须是实例方法而且必须返回 Objective-C 指针类型。另外，如果一个程序声明或包含了一个对返回类型既不是 id 类型也不是指向类的父类或着子类的指针的 init 方法的调用（如果方法是在类上声明的）或静态接收器类型的调用（如果方法是在协议上声明的），那么这个程序是不正确的。
  >
  >有许多类似 init 选择器的现有方法仍然没有遵循 init 约定。通常，这些方法要么是命名的时候意外冲突了，要么就是初始化期间调用的辅助方法。因为 init 方法中有特殊的保留/释放操作，如果这些类似的现有方法并不是真正意义上的 init 方法，那么将他们与传统意义上的 init 方法区分开来是非常重要的。有人认为，基于返回类型和声明类之间的确切关系，在家族之外隐式地定义这些方法将是非常微妙和脆弱的。因此，clang 确定了一小部分看起来合理的返回类型，并将其推所有的类型都看作错误。这也有助于鼓励程序员们不要在 init 家族中给出其他不合理的方法名。
  >
  >注意，具有非 Objective-C 类型（例如 void）返回值的 init-family 选择器的方法并不是错误的；事实上它根本不在 init 家族中。

如果一个方法的声明、实现和重载不具有相同的方法族，那么这个程序也是不正确的。

# MRC(Manual Reference Counting)

## 初衷

在 c 语言中，我们通过 malloc 申请一块内存，等到不用的时候，需要调用 free 去释放掉它。在 C++ 中，我们通过 new 来创建一个对象，通过 delete 来释放它。这种内存管理模式，就使得程序员们必须知道一个内存对象什么时候不再使用了，它的终点在哪里。随着时间的流逝，代码越来越复杂，参与开发的程序员越来越多，就很难把握这个内存对象的终点了。于是，要么内存对象一直保留着，没人敢释放，整个程序占用的内存空间越来越大；要么，某个胆大的程序员把它释放掉，然后某处发生了 crash。尽管大家总结出了许多"谁创建谁释放"，"谁引用谁释放"的原则，但是使用上还是会带来种种问题。

苹果为了解决这个问题，就引入了引用计数，总体思路就是：任何一个内存对象由系统自己处理释放的问题，无论创建者还是持有者，不需要考虑别人是否还在使用同一个内存对象，做好自己该做的就可以了，别人的事情别人自己负责。所有使用到同一内存对象的地方，使用者只要保证自己 retain 一次，使用完之后 release 一次，就可以了。调用 retain 的时候，内存对象的引用计数会 +1，调用 release 的时候内存对象的引用计数会 -1，当引用计数为 0 的时候，系统就会释放该内存对象。所以，只要我们是按照规则调用了 retain 和 release ，就不会有什么问题。

> ```swift
> + (instancetype)createModel
> {
>     Model *model = [[self alloc] init];
>     return model;
> }
> ```
>
> 另外，如上图，在通过非五大方法家族的方法返回一个对象实例的时候，本着自己创建自己释放的原则，我们应该在 return 之前调用一次 release，变成下图
>
> ```swift
> + (instancetype)createModel
> {
>     Model *model = [[self alloc] init];
>   	[model release];
>   	model = nil;
>     return model;
> }
> ```
>
> 但是如果调用 release 之后，model 就被释放掉了，那么返回的就是个空，这个方法也就没有了它的作用。那么，为了解决这个问题，苹果引入了自动释放池这个机制，程序中的每个 runloop 都会绑定一个自动释放池，我们用 autorelease 来将一个对象标记为 autorelease 对象，在 autorelease 的实现中，这个内存对象会被加入到就近的自动释放池中，待到这个自动释放池释放的时候才会对 autorelease 对象执行 release 操作，这样就延迟了内存对象的生命周期，既能达到我们的目的，又能达到自己创建自己释放的原则。但是因为 autorelease 对象会带来一系列的开销，所以我们能不使用 autorelease 的话还是尽量不要使用。事实上这个方法变化成了下面这个样子:
>
> ```swift
> + (instancetype)createModel
> {
>     Model *model = [[self alloc] init];
>   	[model autorelease];
>     return model;
> }
> ```

## 优缺点

1. MRC 的计数机制改善了内存管理的方式，减少了各个模块的逻辑耦合，释放了程序员对"何时该释放的心理压力"
2. 要很清楚内存管理逻辑，如果多些一个 release 就会导致程序崩溃，如果少写一个 release 又会导致内存泄漏。合理地管理 retain 和 release 代码有一定难度。需要花大量时间在处理内存管理这一方面。

# ARC(Auto Reference Counting)

自动引用计数，WWDC2011 和 iOS5 所引入的最大的变革和最激动人心的变化。ARC 是 LLVM 3.0 编译器的一项特性，使用 ARC，一举解决了广大 iOS 开发者所憎恨的手动内存管理的麻烦。

在工程中使用 ARC 非常简单：只需要像 MRC 那样编写代码，只不过永远不写 `retain`、`release` 和 `autorelease` 即可（这是 ARC 的基本原则）。ARC 开启后，编译器会自动在合适的地方插入 `retain`，`release` 和 `autorelease`。其实就是类似于以前在 MRC 时代做的工作，只不过 MRC 是要开发人员手动插入内存管理代码，现在是由编译器完成了而已。

## 为什么会出现 ARC

从 MRC 那段我们可以得知，MRC 的出现是为了解决内存管理的问题，形成一种统一规范，不至于在项目迭代的过程中内存占用越来越大。但是，理想很丰满，现实很骨感。在 MRC 时代，首先我们需要很清楚的了解引用计数的运行机制，什么时候该 retain ，什么时候该 release，什么时候又该 autorelease，少写一个 release 就会引发内存泄露问题，多写一个 release 又会引发 crash，内存占用过高还会引起 OOM(Out Of Memory Killer) 。无疑增加了 Objective-C 的入门门槛，而且开发人员还得花大量时间在处理内存管理问题方面，就导致开发效率没有那么高。

于是，ARC 应运而生。

有了 ARC，我们再也不用过多担心内存管理问题了，只要注意不要引起循环引用，我们的编程过程还是非常愉快的。我们可以花更多的时间在程序的体验，性能等各个方面，来做出用户更满意的东西。

## 变量作用域

在 c 语言中，变量分为这几种类型：

* 全局变量 

  在所有作用域都可以访问的变量

  * 静态全局变量

    > 静态变量在计算机编程领域是指在程序执行之前系统就为之静态分配（即在运行期间不再改变分配情况）存储空间的一类变量。与之相对应的是在运行时只暂时存在的自动变量与以动态分配方式获取存储空间的一些对象。

    静态全局变量就是那些声明在头文件中的，其他模块只要引用头文件就可以使用的那些静态变量。每个文件中的静态变量都是互不影响的，假如在 A 文件中对静态变量的值进行了修改，那么 B 文件中获取到的还是静态变量的初始值。

  * 外部变量

    变量名具有外部链接的变量。

* 局部变量

  拥有局部作用域的变量，这样的变量只能由声明它的函数或块才能访问。

  > 自动变量，具体来说即是在控制流进入变量作用域时系统自动为其分配存储空间，并在离开作用域时释放空间的一类变量。
  >
  > 寄存器变量，一个变量直接引用寄存器，对变量名的操作的结果是直接对寄存器进行访问。编译器会将变量缓存于处理器的寄存器中，此种情况下不能对该变量或其成员变量使用引用操作符&以获取内存空间中的地址；除此以外，由于寄存器的数量以及其所能存储的数据类型受硬件限制还可能造成无法存储指定变量的情况。
  >
  > 局部变量在每种语言中的实现都不一样。一般局部变量的默认实现都是自动变量。而且一般语言不提供寄存器变量声明。
  >
  > c 语言中的默认实现也是自动变量。局部变量默认用 auto 进行标示。如果局部变量用 register 进行标识，那么就会变成寄存器变量。编译器在寄存器受限的情况下会忽略 register 标示，相当于还是当作自动变量来看待。

  * 静态局部变量
  * 非静态局部变量



# 引用

[arc-method-families](http://clang.llvm.org/docs/AutomaticReferenceCounting.html#arc-method-families)

[iOS内存管理：从MRC到ARC实践](https://segmentfault.com/a/1190000004705133)

[理解iOS的内存管理](https://blog.devtang.com/2016/07/30/ios-memory-management)

[手把手教你ARC——iOS/Mac开发ARC入门和使用](https://onevcat.com/2012/06/arc-hand-by-hand/)

