#单例篇
```objective-c
#pragma mark - life Cycle
+ (instancetype)manager
{
    static TKYTSDKManager *manager;//申明一个静态变量
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        manager = [self new];//实例化一个对象 赋值 给静态变量
    });
    return manager;
}
```
静态变量，静态变量只会申明一次，下一次再走这个方法的时候，就不会重新声明，也就是不会置为nil，这样子，假如我们现在给静态变量赋了个值，3，那么，下一次我们只要还是从这个方法里面取到的静态变量，他的值就还是3（前提是不重新赋值）。这样子就很容易理解单例，我们运用单例，也就是想保证，我们取得的那个实例，在任何时候，地址都是相同的，都是同一个实例

```objective-c
static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        manager = [self new];//实例化一个对象 赋值 给静态变量
    });
```
这个方法，就是给静态变量赋值，而且是只赋一次值，可以有很多途径，例如锁，gcd
用锁的话，可以这么理解，我们在编写代码的时候，可能会有统一时间调了很多次单例的这种情况，这种就需要同步锁，来让它们进行一个有序的操作，如果我们这时候做了非空判断，就可以保证只进行一次赋值操作啦，但是如果没有做非空判断呢，就会对变量进行一个多次有序的赋值


#+load +initialize篇

+load 	当类被加载到runtime的时候就会运行，也就是说是在main.m之前

+initialize 是在类接收第一条消息之前初始化类

###区别
1. 类初始化的时候每个类只会调用一次+initialize()，如果子类没有实现+initialize()，那么将会调用父类的+initialize()
2. +load 会根据Compile Sources(xcode工程目录 General-Build Phases-Compile Sources)中的顺序进行加载
3. +initialize() 在+load方法前进行调用
4. 分类中的load方法始终会在类的load方法后执行
5. 如果有分类的话，会调用分类的+initialize()不会调用类本身的+initialize()

 
#装饰者模式
装饰对象的接口必须与它所装饰的 Component 的接口是一致的,因此,所有的 concreteDecorator 类必须有一个公共的父类

利用递归进行方法的调用
 
 

