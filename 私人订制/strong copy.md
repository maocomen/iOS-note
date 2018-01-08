我们可以先思考一下下面这个问题

### question

为什么用 property 声明 NSString ，NSArray ，NSDictionary 的时候，要用 copy 修饰？不用 copy 用 strong 会有什么问题？

### answer：

#### 1.用 strong 修饰会出现什么问题

首先，NSString ，NSArray ，NSDictionary 都有其可变子类 NSMutableString ，NSMutableArray ，NSMutableDictionary ，因为子类实例其实是可以赋值给父类的，如果我们用 strong 修饰，声明了一个不可变类型，却在 set 方法里赋了一个可变类型，那么，self.property 就会指向子类可变类型，当子类可变类型发生改变的时候，self.property 也会相应跟着改变。

**举个🌰**

```objective-c
@interface ViewController ()

@property (nonatomic , strong) NSString *string;

@end
  
  
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSMutableString *mString = [[NSMutableString alloc] initWithString:@"123123"];
    self.string = mString;
    
    NSLog(@"\n propertyString:%@ \n mString:%@", self.string, mString);
    
    [mString appendString:@"234234"];
    NSLog(@"\n propertyString:%@ \n mString:%@", self.string, mString);
    
    [mString appendString:@"aaabbb"];
    NSLog(@"\n propertyString:%@ \n mString:%@", self.string, mString);
    
    // Do any additional setup after loading the view, typically from a nib.
}


- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}


@end
```

我们看一下控制台日志：

```objective-c
2017-05-17 12:02:22.414 PropertyDemo[11868:233559] 
 propertyString:123123 
 mString:123123
2017-05-17 12:02:22.414 PropertyDemo[11868:233559] 
 propertyString:123123234234 
 mString:123123234234
2017-05-17 12:02:22.414 PropertyDemo[11868:233559] 
 propertyString:123123234234aaabbb 
 mString:123123234234aaabbb
```



#### 2.用 copy 修饰会怎样

如果我们用 copy 修饰一个 property 的话，系统默认在 set 方法里做了一些操作（如果是我们重写 set 方法，也必须这样操作才行）

```objective-c
- (void)setProperty:(id)property
{
    _property = [property copy];
}
```

如果用 copy 修饰，set 方法就会变成这样，需要做一下 copy 操作，再赋值给 _property（这就是为什么我们在用 copy 修饰一些未实现 NSCopying 协议的类的时候，调用 set 方法会崩溃的原因）。 

我们将上面 property 声明 string 的代码中的 strong 改成 copy ，再看看控制台日志。

```objective-c
@interface ViewController ()

@property (nonatomic , copy) NSString *string;

@end
  
console Log:
2017-05-17 12:11:25.298 PropertyDemo[11900:238201] 
 propertyString:123123 
 mString:123123
2017-05-17 12:11:25.298 PropertyDemo[11900:238201] 
 propertyString:123123 
 mString:123123234234
2017-05-17 12:11:25.298 PropertyDemo[11900:238201] 
 propertyString:123123 
 mString:123123234234aaabbb
```

#### 总结

总体来说，用 copy 和 strong 修饰 property 的区别就是

* copy 修饰，系统会默认在 set 方法中进行一次 copy 操作。
* strong 修饰 ，直接进行赋值，不进行 copy 操作。

就这么多，如果有讲的不对的地方，还需要大家多多指证！😊😊😊