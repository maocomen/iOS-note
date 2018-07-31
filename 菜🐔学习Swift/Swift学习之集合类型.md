

[TOC]

#集合类型

Swift 提供了三种主要的集合类型用于存储一系列的值，也就是所谓的数组，集合和字典。数组是值的有序集合。集合是唯一值的无序集合。字典是键值对的无序集合。

![](https://docs.swift.org/swift-book/_images/CollectionTypes_intro_2x.png)

在 Swift 中，我们能很明确的知道集合中能存储哪种类型的值和它们能存储的键的类型。这意味着我们不可能向集合中插入一个错误类型的值。它同样意味着我们可以从集合中取出确定类型的值。

> Swift 中的数组，集合和字典都是通过泛型集合实现的。如果我们想查看更详细的泛型集合的信息，可以访问[Generics](https://docs.swift.org/swift-book/LanguageGuide/Generics.html)。



## 集合的可变性

如果我们创建了一个数组，集合或者字典，并把它赋给了一个变量，那么创建的集合就是可变的。这意味着集合被创建完之后，我们可以通过添加、移除或者改变集合中的元素来变更这个集合。如果我们将数组、集合或者字典赋值给一个常量，则集合就变成了不可变的，它的大小和内容都不能被改变。

> 在集合不需要变更的情况下创建不可变集合是个不错的选择。这样可以使我们更容易的管理代码，并且 Swift  编译器也会帮助我们优化集合的性能。

## 数组

数组以有序的方式来存储具有相同类型的值。相同的值可以在数组的不同位置多次出现。

> Swift 中的 Array 类型桥接到了 Foundation 框架的 NSArray 上。
>
> 如果想要了解更多如何通过 Foundation 和 Cocoa 框架桥接 Array 的信息，可以查看 [Bridging Between Array and NSArray](https://developer.apple.com/documentation/swift/array#2846730)。

###数组的简写语法

在 Swift 中，数组的完整写法应该是 `Array<Element>` ，其中的 Element 是数组允许存入的值的类型。同样，我们可以通过简写 `[Element]` 来指定数组的类型。尽管两种写法功能上是完全相同的，但是官方更推荐简写形式，而且文档上涉及到数组的也都是简写形式。

### 创建一个空的数组

我们可以通过初始化器（构造器）语法来创建和一个指定类型的数组:

```swift
var someInts = [Int]()
print("someInts is of type [Int] with \(someInts.count) items.")
// Prints "someInts is of type [Int] with 0 items."
```

在上面的例子中，因为初始化器的类型是 [Int]，所以变量 someInts 会被推断为是 [Int] 类型。

如果我们已经通过函数的参数或者已经确定类型的常量或者变量来使得数组有了具体的类型和内容，我们可以通过  [] (一对方括号) 来创建一个空的数组。

```swift
someInts.append(3)
// someInts now contains 1 value of type Int
someInts = []
// someInts is now an empty array, but is still of type [Int]
```

### 创建一个带默认值的数组

Swift 的 Array 还提供了一个初始化器，用于创建一个特定大小的数组，并将其所有值设置为相同的默认值。我们可以传给初始化器一个对应类型的默认值（叫做 repeating ）和新数组中元素的数量（叫做 count ）:

```swift
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]
```

### 通过连接两个数组来创建数组

我们可以把两个已经存在的类型兼容的数组通过 + 号连接起来来创建一个新的数组。新数组的类型将从相加的两个数组里推断出来:

```swift
var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
// anotherThreeDoubles is of type [Double], and equals [2.5, 2.5, 2.5]

var sixDoubles = threeDoubles + anotherThreeDoubles
// sixDoubles is inferred as [Double], and equals [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]
```

### 使用数组字面量创建数组

我们同样可以使用数组字面量来初始化一个数组，数组字面量是一种将一个或者多个值作为数组集合的简写方式。用方括号将一系列的值括起来，值与值之间用逗号分割，就是数组字面量的书写方式了:

> [value 1, value 2, value 3]

下面的例子创建了一个叫做 shoppingList 的数组并用来存储 String 类型的值:

```swift
var shoppingList: [String] = ["Eggs", "Milk"]
// shoppingList has been initialized with two initial items
```

shoppingList 变量被声明为 "一个存储字符串的数组"，写作 [String]。由于这个数组有特定的值类型 String，所以它只能存储 String 类型的值。这里，shoppingList 使用两个 String 值（ "Eggs" 和 "Milk" ）进行初始化，这两个字符串值写在了字面值里。

> 数组 shoppingList 被声明成了一个变量（用 var 修饰）而不是常量（用 let 修饰），因为在接下来的例子里还会有很多东西被添加到购物清单中。 

