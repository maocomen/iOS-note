[TOC]

## 宏与const，static，extern

为什么我们在开发中会经常看到声明全局常量或者变量的时候会用 static 修饰？为什么？用宏不是也可以声明常量么？两个有什么异同？

```objective-c
#import <Foundation/Foundation.h>

NSString * customModelName = @"张三";

@interface CustomModel : NSObject

@end
```

假如我们声明一个类，在类的头文件中，直接这么写，不用 static 修饰行不行？

我们一个一个地来进行验证。

### 宏

宏，我们平常使用的很多，这里就不说怎么用了，主要看一下宏的几个特点。

* 编译时期：预编译（编译之前处理）
* 编译检查：宏不做编译检查，不会报编译错误，只是替换
* 宏可以定义函数，方法
* 宏不可以声明变量
* 大量使用宏，容易造成编译时间久，每次都需要重新替换。

### 用非宏方式声明变量或常量

对比宏，非宏方式有这么几个特点。

* 编译时期：编译时期
* 编译检查：会编译检查，会报编译错误
* 不能定义函数，方法
* 可以声明变量也可以声明常量

上面我们已经声明了一个类，并且在类的头文件中声明了一个变量，现在，我们在 controller 中引入这个类，看看会发生什么。

```objective-c
#import "ViewController.h"
#import "CustomModel.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
}


- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}


@end
```

就普普通通的引入，按道理是不应该有什么毛病的，编译一下试试。

编译中。。。。

哇擦咧，竟然有报错？我们看一下报错信息。

```objective-c
duplicate symbol _customModelName in:
    /Users/maxin/Library/Developer/Xcode/DerivedData/ConstDemo-gptrppyhxfauedasgluukadwmela/Build/Intermediates/ConstDemo.build/Debug-iphonesimulator/ConstDemo.build/Objects-normal/x86_64/CustomModel.o
    /Users/maxin/Library/Developer/Xcode/DerivedData/ConstDemo-gptrppyhxfauedasgluukadwmela/Build/Intermediates/ConstDemo.build/Debug-iphonesimulator/ConstDemo.build/Objects-normal/x86_64/ViewController.o
ld: 1 duplicate symbol for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

信息上说，我们在 CustomModel.h 中声明了一个 customModelName 变量，然后又在 ViewController 中声明了一个？what fuck ？明明只是引入了一下。

这是因为，我们在 ViewController 中引入了 CustomModel，在编译 ViewController 的时候，会把 CustomModel 里的内容打在生成的 .o 文件中，这里会生成一个 customModelName 变量，然后在编译 CustomModel 的时候，又生成了一个变量，所以会报错。

那么？我们应该如何解决这个问题呢？如何让他只生成一次？

#### static 

static 修饰，变量只会生成一份内存，只会初始化一次。

变量的生命周期为编译时创建，程序结束时销毁。

我们在声明变量的时候加个 static 修饰词看一下。

```objective-c
#import <Foundation/Foundation.h>

static NSString * customModelName = @"张三";

@interface CustomModel : NSObject

@end
```

然后 controller 中变成这样

```objective-c
#import "ViewController.h"
#import "CustomModel.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSLog(@"name:%@", customModelName);
    // Do any additional setup after loading the view, typically from a nib.
}


- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}
```

编译可以通过，值也能正常打印出来。

#### extern 

extern 本身就是外部的，外面的意思，主要做一个对外声明，声明有这个变量。

作用：只是用来获取全局变量的值，不能用于定义变量

工作原理：先从当前文件查找有没有全局变量，没有找到，才会去其他文件里查找。

extern 声明的变量，必须有实现，而且只能实现一次。

其他地方可以修改，但是一旦修改，则在整个 app 启动期间都会有效，所以我们一般声明为常量。

我们可以按下面这种方式进行 extern 的使用。

```objective-c
//Const.h
#ifndef Const_h
#define Const_h

extern NSString * customModelName;

#endif /* Const_h */

//CustomModel.m
#import "CustomModel.h"
#import "Const.h"

NSString * customModelName = @"张三";

@implementation CustomModel

@end
```

#### const 

* const 仅仅用来修饰右边的变量（基本数据变量p，指针变量*p）
* 被 const 修饰的变量是只读的。

举个🌰

```objective-c
static const NSString *string = @"123";
```

这种写法，const 修饰的是名为 string 的一个指针，指针不可变，但是指针指向的地址可变，所以我们可以发现，还是可以给 string 重新赋值。下面代码：

```objective-c
static const NSString *string = @"123";
string = @"234";
```



第二个🌰

```objective-c
static NSString * const string = @"123";
```

这种写法，const 修饰的是 string 指针指向的地址，指针指向的地址不可改，但是指针本身可以改（改了指针相当于另一个指针了，没什么意义）

可以达到我们预期的静态 string 常量的效果。

下面两种写法是等价的

```objective-c
static const NSString *string = @"123";
==
static NSString const *string = @"123";
```



### 注意

extern 只是一个对外声明，并没有定义一个变量。如果不在工程中做 extern 所声明的变量的定义的话，会报错。引用的变量并没有定义。

static 是有定义变量的，`static NSString *name`这句代码，在不同的头文件中多次定义照样会报错。会提示在不同的文件中定义了同一个变量。

const 只是用来声明变量的只读的，可以和 static 和 extern 组合。

extern 不可以和 static 组合，用 extern 做了对外声明，则定义变量的时候也不可以用 static。