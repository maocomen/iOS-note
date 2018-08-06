

[TOC]

# 集合类型

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

如果上下文中已经提供了类型信息，比如函数的入參或者一个已经确定了类型的常量或者变量，我们可以通过  [] (一对方括号) 来创建一个空的数组。

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

在这种情况下，数组字面值除了两个 String 值之外没有包含其他类型的值。这与 shoppingList 变量声明的类型一致（一个只能包含 String 值的数组），因此在这里以只包含两个 String 值的数组字面量来初始化 shoppingList 是被允许的。

依托于 Swift 的类型推断，如果我们用包含相同类型的值的数组字面量来初始化一个数组的时候，我们可以省略掉数组的类型。shoppingList 的初始化代码可以写的更短:

```swift
var shoppingList = ["Eggs", "Milk"]
```

因为数组字面量中所有的值都是相同的类型，Swift 可以推断出 [String] 是 shopplingList 最适合的类型。

### 访问和修改数组

我们可以通过使用数组的方法、属性或者使用下标脚本语法来访问或者修改数组。

要得出数组拥有的元素的个数，我们可以使用其只读的属性 count。

```sw
print("The shopping list contains \(shoppingList.count) items.")
// Prints "The shopping list contains 2 items."
```

使用布尔值属性 isEmpty 来作为检查数组里的元素个数是否为 0 的快捷方式。

```sw
if shoppingList.isEmpty {
    print("The shopping list is empty.")
} else {
    print("The shopping list is not empty.")
}
// Prints "The shopping list is not empty."
```

我们可以使用数组的 append(_:) 方法在数组末尾追加新的元素:

```swift
shoppingList.append("Flour")
// shoppingList now contains 3 items, and someone is making pancakes
```

另外，我们可以通过加赋值运算符（+=）来在数组末尾追加一个或多个元素:

```swift
shoppingList += ["Baking Powder"]
// shoppingList now contains 4 items
shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
// shoppingList now contains 7 items
```

我们可以使用下标脚本语法来从数组中取回一个值，紧跟着数组后写一个方括号，然后在方括号中传入我们想取回的值的索引就可以了:

```swift
var firstItem = shoppingList[0]
// firstItem is equal to "Eggs"
```

> 数组中第一个元素的下标是 0 而不是 1，Swift 中的数组都是以下标 0 作为开头。

我们可以通过下标脚本语法来改变数组中给定索引出已经存在的值:

```swift
shoppingList[0] = "Six eggs"
// the first item in the list is now equal to "Six eggs" rather than "Eggs"
```

当我们使用下标脚本语法的时候，我们必须保证我们指定的索引是有效的。如果我们使用了 shoppingList[shoppingList.count] = "Salt" 来在数组的末尾添加一个值，那么在运行的时候就会抛出一个异常。

我们也可以使用下标脚本语法来一次性改变数组中一个范围内的值，就算替换的范围和要替换的范围的长度不一样也可以。在下面的例子中，用 “Bananas” 和 “Apples” 替换掉了 “Chocolate Spread”，“Cheese” 和 “Butter”:

```swift
shoppingList[4...6] = ["Bananas", "Apples"]
// shoppingList now contains 6 items
```

如果想在指定索引位置插入一个元素，我们可以使用 insert(_:at:) 方法:

```swift
shoppingList.insert("Maple Syrup", at: 0)
// shoppingList now contains 7 items
// "Maple Syrup" is now the first item in the list
```

通过指定索引 0 ，上面的代码通过调用 `insert(_:at:)` 方法将一个值 “Maple Syrup” 插入到了 shoppingList 的最前面。

与之类似，如果我们想移除数组中指定索引的元素，可以使用 `remove(at:)` 方法。这个方法会将指定索引的元素移除，并将这个移除的元素作为返回值返回（如果我们不需要的话，我们可以忽略掉这个返回值）。

```swift
let mapleSyrup = shoppingList.remove(at: 0)
// the item that was at index 0 has just been removed
// shoppingList now contains 6 items, and no Maple Syrup
// the mapleSyrup constant is now equal to the removed "Maple Syrup" string
```

> 如果我们需要访问一个超出数组边界的索引对应的值，我们将触发一个运行时异常。我们需要在使用之前，通过对比数组的 count 属性来检查这个索引是否在数组的范围之内。数组中有效索引的最大值是 count-1，因为数组的索引总是从 0 开始——然而，当 count 是 0 的时候（意味着数组是空的，没有任何元素），任何索引都是无效的。

当元素被删除后，数组中的任何空白都会被关闭，所以索引 0 对应的值再一次成为了 “six eggs”:

```swift
firstItem = shoppingList[0]
// firstItem is now equal to "Six eggs"
```

如果我们想余处数组的最后一个元素，我们最好是使用 removeLast() 方法而不是 remove(at:) 方法，因为这样子可以避免我们查询一次数组的 count 属性:

```swift
let apples = shoppingList.removeLast()
// the last item in the array has just been removed
// shoppingList now contains 5 items, and no apples
// the apples constant is now equal to the removed "Apples" string
```



### 遍历数组

我们可以使用 for-in 循环去遍历整个数组中元素的集合:

```swift
for item in shoppingList {
    print(item)
}
// Six eggs
// Milk
// Flour
// Baking Powder
// Bananas
```

如果我们同时需要每个元素的值和这个元素的索引，我们需要用数组的枚举序列（也就是数组 enumerated() 方法执行之后的返回值）代替数组进行遍历。enumerated() 方法会为数组中的每一个元素生成一个元组，这个元组由一个整数和该元素组成。整数从 0 开始，每循环一次就加一。如果枚举的是整个数组，那么这些整数将完全与数组中元素的索引相匹配。作为迭代的一部分，我们可以将彦祖分解成临时的常量或者变量:

```swift
for (index, value) in shoppingList.enumerated() {
    print("Item \(index + 1): \(value)")
}
// Item 1: Six eggs
// Item 2: Milk
// Item 3: Flour
// Item 4: Baking Powder
// Item 5: Bananas
```



## 集合

集合是一个存储同一类型且不重复的值的无序集合。当元素的顺序不是那么重要，或者我们确保元素不能重复的情况下，我们可以使用集合来代替数组。

> Swift 中的 Set 类型桥接到了 Foundation 框架中的 NSSet 类。
>
> 如果想了解 Set 类型是如何通过 Foundation 和 Cocoa 框架工作的，可以看一下 [Bridging Between Set and NSSet](https://developer.apple.com/documentation/swift/set#2845530) 。

### Set 类型的哈希值

如果想让一个类型存储到集合中，这个类型必须是可哈希的——也就是说，该类必须提供一个计算它本身的哈希值的方法。哈希值是一个 Int 类型的值，所有相等的对象他们的哈希值肯定是相同的，比如，如果 a == b，那么，a.hashValue == b.hashValue 。

Swift 中的基础类型（比如 String，Int，Double 和 Bool），默认都是可哈希的，他们都可以用来做集合的值或字典的键值。没有关联值的枚举成员值（[Enumerations](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html) 有详细描述），也是默认可哈希的。

> 我们同样也可以使用我们自定义的类型作为集合的值或者字典的键值，只要让他们遵循 Swift 标准库中的 Hashable 协议即可。遵循 Hashable 协议的类必须提供一个可供访问的名为 hashValue 的 Int 属性。类型的 hashValue 属性的返回值在同一程序或不同程序的不同执行中不需要相同。
>
> 因为 Hashable 协议遵循了 Equatable 协议，遵循 Hashable 协议的类型同时必须提供一个 “相等” 运算符（`==`）的实现。Equatable 协议要求任何遵循了协议的 == 的实现都必须是一个对等关系。`==` 的实现必须满足下面三个条件，其中的 a，b 和 c 是任意值:
>
> * a == a （自反性）
> * a == b 意味着 b == a （对称性）
> * a == b && b == c 意味着 a == c （传递性）
>
> 更多关于协议的信息，可以查看 [Protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html) 。

### 集合类型语法

Swift 中的集合写作 `Set<Element>`，这里的 Element 是集合要存储的类型。不同于数组，集合并没有等价的简写形式。

### 创建并初始化一个空的集合

我们可以通过初始化语法来创建一个确定类型的集合:

```swift
var letters = Set<Character>()
print("letters is of type Set<Character> with \(letters.count) items.")
// Prints "letters is of type Set<Character> with 0 items."
```

> 由于初始化器的类型是 `Set<Character>` 所以 letters 的类型也会被推断为  `Set<Character>` 。

如果上下文中已经确认了类型信息，比如函数的入參或者是已经确定了类型的变量或者常量，我们可以用一个空的数组字面量来创建一个空的集合:

```swift
letters.insert("a")
// letters now contains 1 value of type Character
letters = []
// letters is now an empty set, but is still of type Set<Character>
```

### 使用数组字面量创建集合

我们也可以通过数组字面量来创建一个集合，算是一种初始带一个或者多个值的集合的快捷方式。

下面的例子创建了一个叫 favoriteGenres 的集合来存储 String 值:

```swift
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
// favoriteGenres has been initialized with three initial items
```

变量 favoriteGenres 被声明为 “一个 String 值的集合”，写作 `Set<String>` 。因为这个集合已经被明确指定为 String 类型，所以它只能存储 String 值。在这里，集合 favoriteGenres 初始化后有三个默认值（"Rock", "Classical" 和 "Hip hop"），因为它是根据一个带默认值的数组字面量去初始化的。

> 集合 favoriteGentes 声明为变量（用 var 修饰）而不是常量（用 let 修饰）是因为在接下来的例子中，会往集合中加入或删除元素。

一个集合的类型不能由数组字面量推断出来，所以 Set 的类型必须显式地声明出来。然而，因为 Swift 由推断机制，如果我们用来初始化的数组字面量中的元素都是相同类型的，我们可以不用去指明集合的类型。favoriteGenres 的初始化可以由简写代替:

```swift
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]
```

因为数组字面量中所有的元素都是相同的类型，Swift 会认为 变量 favoriteGenres 更适合使用 `Set<String>` 类型。

### 访问和修改集合

我们可以通过集合的方法和属性来访问和修改集合。

我们可以使用集合的 count 属性来得出集合中元素的数量:

```swift
print("I have \(favoriteGenres.count) favorite music genres.")
// Prints "I have 3 favorite music genres."
```

使用布尔值 isEmpty 来作为检查 count 是否为 0 的快捷方式:

```swift
if favoriteGenres.isEmpty {
    print("As far as music goes, I'm not picky.")
} else {
    print("I have particular music preferences.")
}
// Prints "I have particular music preferences."
```

可以调用 `insert(_:)` 方法来为集合添加一个新的元素:

```swift
favoriteGenres.insert("Jazz")
// favoriteGenres now contains 4 items
```

我们可以调用 `remove(_:)` 方法来从集合中删除一个元素，如果要移除的元素存在于集合中，就会从集合中移除它，并将它作为返回值返回，如果要移除的元素不存在于集合中，那么会返回 nil。如果要移除集合中所有的元素，我们可以调用 `removeAll()`。

```swift
if let removedGenre = favoriteGenres.remove("Rock") {
    print("\(removedGenre)? I'm over it.")
} else {
    print("I never much cared for that.")
}
// Prints "Rock? I'm over it."
```

要检查一个集合中是否包含某个元素，我们可以使用 `contains(_:)` 方法。

```swift
if favoriteGenres.contains("Funk") {
    print("I get up on the good foot.")
} else {
    print("It's too funky in here.")
}
// Prints "It's too funky in here."
```

### 遍历集合

我们可以在 for-in 循环中遍历集合中的元素。

```swift
for genre in favoriteGenres {
    print("\(genre)")
}
// Classical
// Jazz
// Hip hop
```

可以在  [For-In Loops](https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html#ID121) 查看更多关于 for-in 循环的信息。

Swift 的 Set 类型是无序的。如果我们想以特定的顺序遍历集合的值，可以使用 `sorted()` 方法，这个方法会返回一个经过 < 关系排序的数组。

```swift

2
3
4
5
6
for genre in favoriteGenres.sorted() {
    print("\(genre)")
}
// Classical
// Hip hop
// Jazz
```

## 执行集合操作

我们可以高效地执行一些集合操作，比如合并两个集合，确定两个集合共同拥有哪些值或者确定两个集合是否有包含关系（一个包含另一个），是否都包含了某些值或者没有包含相同的元素。

### 基本集合操作

下面的插图描述了两个集合 —a 和 b-- 在各种集合操作下的结果，插图中的阴影部分就是集合操作的结果。

![](https://docs.swift.org/swift-book/_images/setVennDiagram_2x.png)

* 使用 `intersection(_:)` 方法来创建一个只包含两个集合共有值的新集合（a 和 b 的交集）；
* 使用 `symmetricDifference(_:)` 方法来创建一个只包含两个集合非共有值的新集合（a 和 b 的对称差集）；
* 使用 `union(_:)` 方法来创建一个包含两个集合所有值的新集合（a 和 b 的并集）；
* 使用 `subtracting(_:)` 方法来创建一个只包含 a 集合元素，不包含 b 集合元素的新集合（a 中 b 的相对补集，b 对 a 的相对补集）。

```swift
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sorted()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted()
// []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
// [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
// [1, 2, 9]
```

### 集合中的成员关系和相等性

下面的插图描述了三个集合——a，b 和 c——，并且用重叠的区域表示了三个集合共享的元素。集合 a 是集合 b 的超集，因为 a 包含了 b 中所有的元素。相反的，集合 b 是集合 a 的子集，因为 b 中所有的元素都被包含在了 a 中。集合 b 和 集合 c 是不相交的，因为他们没有共同拥有的元素。

* 使用 “相等” 运算符（==）来判断两个集合是否包含有相同的值；
* 使用 `isSubset(of:)` 方法来判断集合中的值是否被另外一个集合所包含；
* 使用 `isSuperset(of:)` 方法来判断集合是否包含了另外一个集合的所有元素；
* 使用 `isStrictSubset(of:)` 或 `isStrictSuperset(of:)` 方法来判断集合是否为另一个集合的子集或者超集，但与其不相等。
* 使用 `isDisjoint(with:)` 方法来判断两个集合是否拥有完全不同的值，即是否是不相交的。

```swift
let houseAnimals: Set = ["🐶", "🐱"]
let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
let cityAnimals: Set = ["🐦", "🐭"]

houseAnimals.isSubset(of: farmAnimals)
// true
farmAnimals.isSuperset(of: houseAnimals)
// true
farmAnimals.isDisjoint(with: cityAnimals)
// true
```



##字典

字典是用来存储互相关联的键和值的无序集合，字典中的所有的键必须是同一个类型，所有的值也必须是同一个类型。每个值都与唯一的键相关联，该键在字典中充当该值的标识符。不同与数组中的元素，字典中的元素没有特定的顺序。当我们需要根据特定的标识去查找其对应的值的时候可以使用字典，这与现实生活中我们用字典来查找特定字的定义的行为非常相似。

> Swift 中的 Dictionary 类型桥接到了 Foundation 框架中的 NSDictionary 类型。
>
> 如果我们想知道有关于 Dictionary 是如何利用 Foundation 和 Cocoa 进行工作的，我们可以查看 [Bridging Between Dictionary and NSDictionary](https://developer.apple.com/documentation/swift/dictionary#2846239) 。

### 字典类型的简写语法

Swift 中字典类型的完整写法应该是 `Dictionary<Key, Value>` ，其中的 Key 是字典中键的值类型，Value 是字典为这些键存储的值的类型。

> 字典中的 Key 的类型必须遵守 Hashable 协议，就和集合中的值类型一样。

我们同样可以用简写形式 [Key: Value] 来声明字典类型。尽管两种写法的功能是完全相同的，但是更推荐这种简写形式，文档上涉及到的字典也全部都是简写形式。



### 创建一个空字典

和数组一样，我们可以用初始化语法来创建一个空的 Dictionary:

```swift
var namesOfIntegers = [Int: String]()
// namesOfIntegers is an empty [Int: String] dictionary
```

这个例子创建了一个类型为 [Int: Stirng] 的空字典来存储整数的可读名称。它的键是 Int 类型，值是 String 类型。

如果上下文中已经提供了字典的类型信息，我们可以使用空字典字面值来创建一个空字典，空字典字面值写作 `[:]`(在一对方括号中写一个冒号):

```swift
namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = [:]
// namesOfIntegers is once again an empty dictionary of type [Int: String]
```

### 用字典字面量来创建一个字典

我们也可以使用字典字面量来初始化一个字典，字典字面量与我们早些看到的数组字面量看起来差不多。字典字面量是一种将一个或多个键值编写为 Dictionary 集合的缩写形式。

键值对由一个键和一个值组合而成。在字典字面量中，每个键值对里的键和值用冒号进行分割，键值对形成一个列表，用逗号分割，再用方括号把它们括起来:

> [key 1: value 1, key 2: value 2, key 3: value 3]

下面的例子中创建了一个用来存储国际机场名称的字典。在这个字典中，键是由三个字母表示的国际航空运输协会代码，值是机场的名字:

```swift
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

字典 airports 被声明成了 [String: String] 类型，这意味着 “字典中，所有的键的类型都是 String，并且所有的值的类型也是 String ”。

> 字典 airports 被声明为了一个变量（用 var 修饰），而不是一个常量（用 let 修饰），因为在接下来的例子中我们会往字典 airports 中加很多东西。

字典 airports 由一个包含liang个键值对的字典字面量初始化而来。第一个键值对的键是 “YYZ”，值是 “Toronto”。第二个键值对的键是 “DUB”，值是 “Dublin”。

这个字典字面量包含两个 String: String 对。这个键值类型符合变量 airports 声明时要求的类型（字典中键只能是 String 类型，值只能是 String 类型） ，所以在这里，通过将字典字面量赋值给变量 airports，来初始化一个带两个初始化项的字典是可行的。

和数组一样，如果我们用一个键类型统一，值类型统一的字典字面量来初始化一个字典的时候，我们可以忽略掉字典的类型指明操作。这样，airports 的初始化代码就能写的更短:

```swift
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

因为上面例子中的字面量中，所有的键都具有相同的类型，而且所有的值也具有相同的类型，所以 Swift 可以推断出 airports 最适合的类型 [String: String]。

### 访问和修改字典

我们可以通过字典的方法和属性，或者是下标脚本语法来对字典进行访问或者修改。

和数组一样，我们可以使用只读的 count 属性来找出 Dictionary 中拥有多少元素:

```swift
print("The airports dictionary contains \(airports.count) items.")
// Prints "The airports dictionary contains 2 items."
```

使用布尔属性 isEmpty 来作为检查 count 属性是否为 0 的快捷方式:

```swift
if airports.isEmpty {
    print("The airports dictionary is empty.")
} else {
    print("The airports dictionary is not empty.")
}
// Prints "The airports dictionary is not empty."
```

我们可以使用下标脚本语法来为字典添加一个元素。使用正确类型的新键来作为下标脚本的索引，然后赋一个正确类型的新值即可:

```swift
airports["LHR"] = "London"
// the airports dictionary now contains 3 items
```

我们同样可以使用下标脚本语法来改变一个特定键所关联的值:

```swift
airports["LHR"] = "London Heathrow"
// the value for "LHR" has been changed to "London Heathrow"
```

作为下标脚本的替代品，可以使用字典的 `updateValue(_:forKey:)` 方法来设置或者更新特定键的值。就像上面下标脚本的例子，如果键对应的值不存在， `updateValue(_:forKey:)`  会为该键设置一个值，如果键对应的值已经存在了，使用该方法会更新这个键的值。不同于下标脚本语法的是，`updateValue(_:forKey:)` 在执行更新之后会返回旧的值。这样我们可以用来检查这次是否是更新操作。

`updateValue(_:forKey:)` 会返回一个符合字典值类型的可选值。比如，如果字典中的值类型是 String，那么，这个方法的返回值就是一个 String? 类型，又或者说是 “一个可选的 String”。如果更新之前，已经存在键所对应的值，那么这个可选值会将旧值包含在内，如果不存在键对应的值，就会返回 nil:

```swift
if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
    print("The old value for DUB was \(oldValue).")
}
// Prints "The old value for DUB was Dublin."
```

我们同样可以使用下标脚本语法来访问字典中特定键的值。因为我们要请求的键很可能没有所对应的值，所以字典的下标脚本语法会返回一个字典值类型的可选值。如果字典中已经存在请求的键对应的值，下标脚本会直接返回一个包含已经存在的值的可选值。否则，返回 nil:

```swift
if let airportName = airports["DUB"] {
    print("The name of the airport is \(airportName).")
} else {
    print("That airport is not in the airports dictionary.")
}
// Prints "The name of the airport is Dublin Airport."
```

通过使用下标脚本语法给键赋一个 nil 值，可以达到从字典中删除一个键值对的目的:

```sw
airports["APL"] = "Apple International"
// "Apple International" is not the real airport for APL, so delete it
airports["APL"] = nil
// APL has now been removed from the dictionary
```

或者，使用 `removeValue(forKey:)` 方法来从字典中删除一个键值对。如果字典中已经存在键对应的值，这个方法会先删掉该键值对，然后返回要移除的值，如果键对应的值不存在，会直接返回 nil:

```swift
if let removedValue = airports.removeValue(forKey: "DUB") {
    print("The removed airport's name is \(removedValue).")
} else {
    print("The airports dictionary does not contain a value for DUB.")
}
// Prints "The removed airport's name is Dublin Airport."
```

### 遍历字典

我们可以使用 for-in 循环来遍历一个字典中的键值对。循环中，字典中的每个项都会作为 （key，value）类型的元组来返回，我们可以将元组的成员分解为临时常量或者变量来作为迭代的一部分:

```swift
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}
// YYZ: Toronto Pearson
// LHR: London Heathrow
```

有关更多 for-in 循环的信息，可以查看 [For-In Loops](https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html#ID121)。

我们可以通过访问字典的 keys 和 values 属性来获取字典的键和值的可迭代集合:

```swift
for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}
// Airport code: YYZ
// Airport code: LHR

for airportName in airports.values {
    print("Airport name: \(airportName)")
}
// Airport name: Toronto Pearson
// Airport name: London Heathrow
```

如果有需要将字典的 keys 或 values 属性与接收 Array 实例的 API 一起使用，我们可以通过 keys 或 values 来初始化一个新的数组:

```swift
let airportCodes = [String](airports.keys)
// airportCodes is ["YYZ", "LHR"]

let airportNames = [String](airports.values)
// airportNames is ["Toronto Pearson", "London Heathrow"]
```

Swift 中的 Dictionary 类型是无序的。如果我们想以特定的顺序来遍历字典的 keys 或者 values，需要对字典的 keys 或者 values 属性使用 `sorted` 方法。