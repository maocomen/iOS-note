在 Objective-C 中，我们为类申明成员变量的时候，经常会这么写：

```objective-c
@property (attribute1,attribute2) float value;
```

其中，在（）里，我们可以申明这个成员变量是原子性还是非原子性，可读，还是可读可写，强指针指向，还是弱指针指向。申明有以下几种类型：

* 原子性 （atomic 和 noatomic 用来决定成员变量的 setter 和 getter 是否为原子操作）

  * atomic 

    系统默认，提供多线程安全。在多线程环境下，可以保证程序在多线程情况下，编译器会自动生成一些互斥加锁代码，避免该变量的读写不同步问题。加了 atomic ，setter 函数会变成下面这样：

    ```objective-c
    {lock}
        if (property != newValue) {
            [property release];
            property = [newValue retain];
        }
    {unlock}
    ```

  * noatomic

    如果该对象无需考虑多线程的情况，加入这个属性，会让编译器少生成一些互斥加锁代码，可以提高效率。

* 读写状态

  * readonly 

    只读的，就是只生成 getter 方法，不会生成 setter 方法。

  * readwrite

    可读可写（默认）会生成 getter 方法 和 setter 方法。

* 引用关系

  * MRC
    * retain
    * assgin
    * copy
  * ARC
    * strong

      所修饰的成员变量为强指针指向，该成员变量的指针直接指向 [[alloc] init] 出来的那个对象。所指向的对象引用计数为0时，所修饰的成员变量也会销毁。

      所修饰的成员变量在被赋值的时候，强指针指向的对象引用计数会+1。

      只能修饰对象。

    * weak

      所修饰的成员变量为弱指针指向，该成员变量的指针指向[[alloc] init] 出来的那个对象。所指向的对象引用计数为0时，所修饰的成员变量也会销毁。

      所修饰的成员变量在被赋值的时候，弱指针指向的对象的引用计数不变。

      只能修饰对象。

    * assign

      ​

    * copy

    * unsafe_unretained


话不多说，代码才是真功夫。

```objective-c
@interface ViewController ()

@property (nonatomic , strong) UIView *demoView;

@property (nonatomic , strong) UIView *tempView;

@property (nonatomic , strong) UIView *tempViewTwo;

@property (nonatomic , strong) UIView *demoViewTwo;

@end
  
@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIView *view        = [UIView new];
    UIView *view2       = view;
    UIView *view3       = view2;
    
    self.demoView       = view3;
    self.demoViewTwo    = self.demoView;
    self.tempView       = self.demoViewTwo;
    self.tempViewTwo    = self.tempView;
    
    NSLog(@"\n %ld %ld %ld %ld %ld %ld %ld \n\n",
          CFGetRetainCount((__bridge CFTypeRef)(view)),
          CFGetRetainCount((__bridge CFTypeRef)(view2)),
          CFGetRetainCount((__bridge CFTypeRef)(view3)),
          CFGetRetainCount((__bridge CFTypeRef)(_demoView)),
          CFGetRetainCount((__bridge CFTypeRef)(_demoViewTwo)),
          CFGetRetainCount((__bridge CFTypeRef)(_tempView)),
          CFGetRetainCount((__bridge CFTypeRef)(_tempViewTwo))
          );

    // Do any additional setup after loading the view, typically from a nib.
}
@end
```

我们写这么一段代码来研究一下不同修饰词的效果

* strong

  这是 strong 修饰时的打印结果

  ![3C51884A-BC42-4963-A05C-4B9316ED5FAA](/Users/maxin/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/929010700/QQ/Temp.db/3C51884A-BC42-4963-A05C-4B9316ED5FAA.png)

  其实以上这些变量，指向的都是 `[UIView new]` ，申明临时变量时，默认是 strong 修饰，因为这些变量都是强指针指向，所以在每个变量赋值的时候，`[UIView new]` 这个东西的引用计数都会 +1，被他赋值的变量的引用计数和他一样。

* weak

  将 interface 里用 property 申明的成员变量用 weak 修饰

  ```objective-c
  @interface ViewController ()

  @property (nonatomic , weak) UIView *demoView;

  @property (nonatomic , weak) UIView *tempView;

  @property (nonatomic , weak) UIView *tempViewTwo;

  @property (nonatomic , weak) UIView *demoViewTwo;

  @end
  ```

  这时候的打印日志![2CBFD4F7-D5B9-43DC-B369-17B060D8B30B](/Users/maxin/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/929010700/QQ/Temp.db/2CBFD4F7-D5B9-43DC-B369-17B060D8B30B.png)

  发现一个很诡异的问题，就是 property 申明的成员变量 的引用计数略有不同。我们修改一下log，再看一遍日志。![3F37D05C-9CE7-4E33-BD08-73BD75700252](/Users/maxin/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/929010700/QQ/Temp.db/3F37D05C-9CE7-4E33-BD08-73BD75700252.png)

  改成这样，这时候的打印日志

  ![67AACF2C-049C-44B5-8EFC-61E0E1998C9E](/Users/maxin/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/929010700/QQ/Temp.db/67AACF2C-049C-44B5-8EFC-61E0E1998C9E.png)

  说明，弱引用并没有增加源对象的引用计数，_tempView 和 _tempViewTwo 的引用计数可能是因为自身是 self 的成员变量，所以用到的时候会 +1； 

* copy

  ```objective-c
  @interface ViewController ()

  @property (nonatomic , copy) UIView *demoView;

  @property (nonatomic , copy) UIView *tempView;

  @property (nonatomic , copy) UIView *tempViewTwo;

  @property (nonatomic , copy) UIView *demoViewTwo;

  @end
  ```

  改成 copy ，再运行，会发现，在控制台会打印下面的日志

  ![017B8794-D102-45B8-B86C-A121F346F4F8](/Users/maxin/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/929010700/QQ/Temp.db/017B8794-D102-45B8-B86C-A121F346F4F8.png)

  由于 UIView 没有实现 copyWithZone 方法，所以用 copy 修饰的时候不能这样赋值，可以选择在 get 方法中给 copy 修饰的成员变量赋值。但是这样子的话，相当于给成员变量设置了只读属性。

  最好用 copy 来修饰实现了 copyWithZone 方法的类。

* assign

  ​