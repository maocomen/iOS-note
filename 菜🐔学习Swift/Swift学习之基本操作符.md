[TOC]

# 基本操作符

操作符是用来检测、更改和组合值的特殊符号或短语。例如，加法运算符 (+) 是将两个数字的值加起来，就像 `let i = 1 + 2` ，逻辑与运算符 (&&) 是将两个布尔值组合起来，就像 `if enteredDoorCode && passedRetinaScan` 。

Swift 支持大多数标准的 C 语言操作符，并改进了几个功能来消除常见的代码错误。赋值运算符 (=) 是不会返回值的，以防止其被错误地用在相等运算符 (==) 的场景。算术运算符 (+, -, *, /, % 等等) 会检测并阻止值的溢出，在处理比该类型所允许的值的范围更大或更小的数的时候，可以避免一些意外的结果。我们可以通过使用 Swift 中的溢出运算符来选择溢出行为，如 [Overflow Operators](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html#//apple_ref/doc/uid/TP40014097-CH27-ID37) 所述。

Swift 还提供了在 C 中找不到的范围运算符，例如 a..<b 和 a…b，其作为表示一系列值的快捷表达式。

本章节介绍了 Swift 中的常见操作符。[Advanced Operators](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html#//apple_ref/doc/uid/TP40014097-CH27-ID28) 中涵盖了 Swift 中的高级操作符，并介绍了如何定义自己的自定义运算符并为自定义类型实现标准运算符。

## 术语

运算符是一元的，二元的或者三元的：

* 一元运算符在单个目标上操作 (例如 -a ) 。一元前缀运算符在其目标之前出现 (例如 !b ) ，一元后缀运算符在其目标之后出现 (例如 c! ) 。
* 二元运算符在两个目标上操作 (例如 a + b ) 并且是中缀，因为它出现在两个目标之间。
* 三元运算符在三个目标上进行操作。像 C 一样，Swift 只有一个三元运算符，就是三元条件运算符 (三目运算符 a ? b : c )。

被操作符影响的值叫做操作数。在表达式 `1 + 2` 中，+ 是二元操作符，值 1 和 2 是两个操作数。

## 赋值运算符

赋值运算 ( a = b ) 是用 b 的值来初始化 a 或者更新 a 的值：

```swift
let b = 10
var a = 5
a = b
// a is now equal to 10
```

如果赋值操作符的右侧是一个具有多个值的元组，那么它的元素可以一次性被分解为多个常量或者变量：

```swift
let (x, y) = (1, 2)
// x is equal to 1, and y is equal to 2
```

与 C 和 Objective-C 中的赋值运算符不同，Swift 中的赋值运算符本身并不会返回一个值，所以下面的这种声明是无效的：

```swift
if x = y {
    // This is not valid, because x = y does not return a value.
}
```

这个特征阻止了我们在想使用相等操作符 (==) 的时候，却错误地使用了赋值运算符 (=) 的这种情况的发生。通过使 if x = y 失效，Swift 可以帮我们避免这类代码错误。

## 算术运算符

Swift 对所有的数字类型都支持四种标准的算术运算符：

* 加法 (+)
* 减法 (-)
* 乘法 (*)
* 除法 (/)

```swift
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4.0
```

与 C 和 Objective-C 中的算术运算符不同，Swift 中的算术运算符默认是不允许值的溢出的。我们可以使用 Swift 中的溢出操作符来选择值的溢出行为。可以参考 [Overflow Operators](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html#//apple_ref/doc/uid/TP40014097-CH27-ID37) 。

加法操作符同样也支持 String 的链接。

```swift
"hello, " + "world"  // equals "hello, world"
```

### 取余操作符

取余操作符 (a % b) 会计算出 a 是 b 的多少倍，然后返回剩下的值 (被称为取余) 。

以下是取余运算符的工作原理。要计算 9 % 4 ，我们首先要知道 9 里面有多少个 4 :

![image: ../Art/remainderInteger_2x.png](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Art/remainderInteger_2x.png)

我们可以在 9 中找到两个 4 ，剩下的是 1 (橙色部分) 。

在 Swift 中，可以这样写：

```swift
9 % 4    // equals 1
```

为了计算出 a % b 的答案，% 运算符会先计算出下面的等式，并将 remainder 作为输出值返回：

```swift
a = (b x some multiplier) + remainder
```

some multiplier 代表着 b 在 a 中的最大倍数。

将 4 和 9 代入等式中：

```swift
9 = (4 x 2) + 1
```

在计算负数 a 的余数的时候，这种方法同样适用：

```swift
-9 % 4   // equals -1
```

将 -9 和 4 代入等式中：

```swift
-9 = (4 x -2) + -1
```

会给出余数 -1 。

如果 b 是个负数，会忽略 b 的符号。意思就是说 a % b 和 a % -b 会给出同样的答案。

### 一元减号运算符

我们可以使用前缀 -  (一元减号运算符) 来切换一个数字的符号。

```swift
let three = 3
let minusThree = -three       // minusThree equals -3
let plusThree = -minusThree   // plusThree equals 3, or "minus minus three"
```

一元减号运算符 (-) 直接为于其要运行的值之前，不需要添加任何空格。

### 一元加号运算符

一元加号运算符 (+) 只会简单地返回其操作的值，不会发生任何值的变化：

```swift
let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix equals -6
```

虽然一元加号运算符实际上并没有做什么事情，但是当我们使用一元减号运算符来表示负数的时候，我们可以使用一元加号运算符来在我们的代码中为其正数提供一种对称性。

##复合赋值运算符

像 C 一样，Swift 也提供了将赋值运算符 (=) 与其他操作符组合起来的复合赋值运算符。一个例子是加法赋值运算符 (+=) ：

```swift
var a = 1
a += 2
// a is now equal to 3
```

表达式 `a += 2` 实际上是 `a = a + 2` 的缩写形式。实际上，加号和等于号被组合成了一个同时可以执行两个任务的操作符。

有关于 Swift 标准库中提供的操作符的更多信息，可以参阅 [Operator Declarations](https://developer.apple.com/documentation/swift/operator_declarations) 。



## 比较运算符

Swift 支持所有标准的 C 比较运算符：

* 等于 (a == b)
* 不等于 (a != b)
* 大于 (a > b)
* 小于  (a < b)
* 大于等于 (a >= b)
* 小于等于 (a <= b)

每个比较运算符都会返回一个 Bool 值来指明条件判断的结果是不是 true：

```swift
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 is not equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 is not less than or equal to 1
```

比较运算符经常会用在条件判断语句中，比如 if 语句：

```swift
let name = "world"
if name == "world" {
    print("hello, world")
} else {
    print("I'm sorry \(name), but I don't recognize you")
}
// Prints "hello, world", because name is indeed equal to "world".
```

有关 if 语句的更多信息，可以参阅 [Control Flow](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120) 。

如果两个元组具有相同的类型和相同数量的值，那么我们可以对它们进行比较。元组的比较是从左到右进行比较的，一次比较一个值，直到通过比较发现两个不同的值。这两个值的比较结果决定了元组比较的整体结果。如果元组的所有元素都相等，那么元组本身就是相等的。例如：

```swift
(1, "zebra") < (2, "apple")   // true because 1 is less than 2; "zebra" and "apple" are not compared
(3, "apple") < (3, "bird")    // true because 3 is equal to 3, and "apple" is less than "bird"
(4, "dog") == (4, "dog")      // true because 4 is equal to 4, and "dog" is equal to "dog"
```

在上面的示例中，我们可以在第一行看到明显的从左到右比较的行为。因为 1 小于 2 ，所以 (1, "zebra") 被认为是小于 (2, "apple") 的，根本就不会管元组中的其他值了。它不会关心 "zebra" 是否小于 "apple" ，因为比较结果已经由元组的第一个元素确定了。然而，当元组的第一个元素相同的时候，就会进行第二个元素的比较——这就是第二行和第三行发生的事情。

只有当操作符可以作用于元组中的每个值的时候，才能用给定的操作符对元组进行比较。例如，在下面的演示代码中，我们可以比较两个类型为 (String, Int) 的元组，因为 String 和 Int 都可以用 < 进行比较。与此相反，两个 (String, Bool) 类型的元组不能用 < 操作符进行比较，因为 < 操作符不能作用于 Bool 值。

```swift
("blue", -1) < ("purple", 1)        // OK, evaluates to true
("blue", false) < ("purple", true)  // Error because < can't compare Boolean values
```



## 三元条件运算符

三元条件运算符是一个操作三个对象的特殊的运算符，它的书写形式是 question ? answer1 : answer2 。它其实是针对于一个条件判断语句的缩写形式，会根据 question 是 ture 还是 false ，来决定执行两个表达式其中的一个。如果 question 是 true ，会执行 answer1 并返回其值，否则执行 answer2 并返回其值。

三元条件运算符是以下代码的缩写：

```swift
if question {
    answer1
} else {
    answer2
}
```

下面是一个例子，用来计算 tableView 的行高。如果这行有标题，那么行高应该比行内容高 50 个点，如果没有标题，行高应该比内容高 20 个点。

```swift
let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90
```

上面这个例子是以下代码的缩写：

```swift
let contentHeight = 40
let hasHeader = true
let rowHeight: Int
if hasHeader {
    rowHeight = contentHeight + 50
} else {
    rowHeight = contentHeight + 20
}
// rowHeight is equal to 90
```

第一个例子使用了三元条件运算符，意味着我们可以在一行代码上为 rowHeight 设置正确的值，代码看起来会比第二个例子中的更简洁。

三元条件运算符提供了一个有效的简写，来决定执行两个表达式中的哪一个。但是，请谨慎使用三元条件运算符。如果过度使用的话，它的这种特性可能会带来难以阅读的代码。尽量避免将多个三元条件运算符的实例组合成一个复合语句。

## Nil-聚合操作符

Nil-聚合操作符 (a ?? b) 在可选值 a 包含一个值的情况下，会将 a 展开并返回其包含的值，如果可选值是 nil ，那么会返回一个默认值 b 。表达式中的 a 始终是个可选类型。表达式中的 b 必须与可选值 a 中包含的值的类型一致。

Nil-聚合操作符可以看作下面代码的缩写形式：

```swift
a != nil ? a! : b
```

上面的代码使用了三元条件运算符，并且在 a 不是 nil 的时候对 a 进行强制解压并返回 a 中包含的值，否则就返回 b 。Nil-聚合操作符提供了一种更优雅的方式来封装这种条件检查并以一种简单易读的形式进行展开。

下面这个例子用了 Nil-聚合操作符来在默认的颜色和可选的用户自定义颜色直接进行选择：

```swift
let defaultColorName = "red"
var userDefinedColorName: String?   // defaults to nil
 
var colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName is nil, so colorNameToUse is set to the default of "red"
```

userDefinedColorName 变量被定义为了一个可选 String 类型，它有一个默认值 nil 。因为 userDefinedColorName 是个可选类型，所以我们在使用它的值的时候可以用 Nil-聚合操作符。在上面的例子中，运算符被用来确定 String 类型的变量 colorNameToUse 的初始值。因为 userDefinedColorName 是 nil ，所以表达式 `userDefinedColorName ?? defaultColorName` 会返回 defaultColorName 的值，或者 "red" 。

如果我们给 userDefinedColorName 赋一个非 nil 值，并再次执行 Nil-聚合操作符 检查，那么则会返回 userDefinedColorName 内部包装的值而不是默认值。

```swift
userDefinedColorName = "green"
colorNameToUse = userDefinedColorName ?? defaultColorName
// userDefinedColorName is not nil, so colorNameToUse is set to "green"
```



## 范围运算符

Swift 包含了几种范围运算符，它们是表示一系列值的快捷方式。

### 闭合区间运算符

闭合区间运算符 (a…b) 限定了一个从 a 到 b 的范围，并且包含值 a 和 b 。值 a 不能大于 b 。如果我们需要遍历范围内的所有值，那么我们可以使用闭合区间运算符，比如 for-in 循环。

```swift
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25
```

想要看更多关于 for-in 循环的消息，可以点击链接 [Control Flow](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120) 。



### 半开区间运算符

半开区间运算符 (a..<b) 限定了一个从 a 到 b 的范围，但是不包括 b 。说它是半开放的，因为它包含第一个值 a ，但是却不包含最终值 b 。与封闭区间运算符一样，a 不能大于 b 。如果 a 等于 b ，那么生成的范围为空。

当我们在使用以 0 开头的列表 (例如数组) 的时候，半开区间运算符会非常有用，它可以列出一个从 0 到列表长度的范围，但是不包括列表的长度。

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
    print("Person \(i + 1) is called \(names[i])")
}
// Person 1 is called Anna
// Person 2 is called Alex
// Person 3 is called Brian
// Person 4 is called Jack
```

注意，数组中包含了 4 个项目，但是 0..<count 仅计数到了3 (数组最后一个元素的索引)，因为它是个半开区间。有关数组的更多信息，可以参阅 [Arrays](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID107) 。

### 单面区间

封闭区间运算符有一个替代形式，这个替代形式可以用来表示在某个方向尽可能继续的范围——例如从索引2(包括)开始到数组最后一个元素的索引的范围。在这些情况下，我们可以省略封闭区间运算符某一边的值。这种范围运算符称为单面区间，因为运算符只有单面有值。例如：

```swift
for name in names[2...] {
    print(name)
}
// Brian
// Jack
 
for name in names[...2] {
    print(name)
}
// Anna
// Alex
// Brian
```

半开区间运算符也有单面形式，我们可以只写一个最终值。就像之前包含两个值的写法一样，最终值并不包含在区间之内。例如：

```swift
for name in names[..<2] {
    print(name)
}
// Anna
// Alex
```

单面区间不仅可以用在取下标的时候，还可以用在其他上下文中。我们不可以遍历一个忽略起始值的单面区间，因为系统并不能清楚的知道我们要从哪里开始。我们可以遍历一个忽略最终值的单面区间；然而，因为这个范围是不确定的，我们需要加上一个明确的结束条件来结束这个循环。我们还可以检查单面区间是否包含某些特定的值，像下面的代码一样。

```swift
let range = ...5
range.contains(7)   // false
range.contains(4)   // true
range.contains(-1)  // true
```



## 逻辑运算符

逻辑运算符会对布尔逻辑值 ( true 和 false ) 进行修改或者组合。Swift 支持基于 C 语言的三种标准逻辑运算符：

* 逻辑 NOT，逻辑非运算符 ( !a )
* 逻辑 AND ，逻辑与运算符 ( a && b )
* 逻辑 OR ，逻辑或运算符 ( a || b )

### 逻辑非运算符

逻辑非运算符会对布尔值取反，也就是说 true 会变成 false ，false 会变成 true 。

逻辑非运算符是前缀运算符，出现在操作的值之前，不加空格。它可以读作 非a ，就像下面例子中一样：

```swift
let allowedEntry = false
if !allowedEntry {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

短语 if !allowedEntry 可以被读作 ==如果不允许输入== 。后续代码只会在 `不允许输入时` 执行，也就是 allowedEntry 为 false 的时候。

在这个例子中，仔细选择布尔常量或者变量的名字可以帮助我们保持代码的可读性和简洁性，同时可以避免双重否定或者令人困惑的逻辑语句。

### 逻辑与运算符

逻辑与运算符 ( a && b) 需要创建一个表达式，并且当两个值都为 true 的时候，表达式的值才会是 true 。

如果任意一个值是 false，那么表达式的值也会是 false。实际上，如果第一个值是 false ，第二个值甚至不会被评估，因为他不可能将表达式的值变为 true 。这被称为短路评估。

下面这个例子会对两个 Bool 值进行评估，只有当两个值都是 true 时，才会允许访问：

```swift
let enteredDoorCode = true
let passedRetinaScan = false
if enteredDoorCode && passedRetinaScan {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED"
```

### 逻辑或运算符

逻辑或运算符 ( a || b ) 是由两个相邻的管道符号组成的中缀运算符。我们可以用它创建一个逻辑表达式，只要两个值其中的一个为 true ，那么表达式的值就是 true 。

像上面的逻辑与运算符一样，逻辑或运算符中也用到了短路评估来考虑其表达式的值。如果逻辑或运算符左边的值是 true ，那么就根本不会对右边的值进行评估了，因为它已经不能更改整个表达式的结果了。

在下面这个例子中，第一个 Bool 值 (hasDoorKey) 是 false ，但是第二个 (knowsOverridePassword) 是 true。因为只要有一个值为 true，则整个表达式的值也会变成 true ，并允许继续访问：

```swift
let hasDoorKey = false
let knowsOverridePassword = true
if hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"

```

### 组合逻辑运算符

我们可以组合多个逻辑运算符来创建更长的复合表达式：

```swift
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

这个例子中使用了多个 && 和 || 来创建了一个长的复合表达式。然而，&& 和 || 操作符只能操作两个值，所以实际上这是三个表达式链接在了一起。这个例子可以解读为：

如果我们输入了正确的门的代码并通过了视网膜扫描，或者我们有一个有效的门钥匙，或者我们有紧急覆盖的密码，则允许访问。

在 enteredDoorCode ，passedRetinaScan 和 hasDoorKey 都是 false，前两个判断都是 false 的情况下，只要知道了紧急覆盖密码，那么整个组合表达式的值就会被推断为 true 。

### 显式括号

在不是严格要求的情况下，我们可以适当加入一些括号，来使得复杂的表达式的意图更易懂。在上面的例子中，我们可以在复杂表达式的第一个小部分加上一个括号，来使得意图更加明确：

```swift
if (enteredDoorCode && passedRetinaScan) || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

括号表明，前两个值被认为是整个逻辑中一个单独的可能状态。复合表达式的输出没有改变，但是整体的意图对读者而言更加清晰了。可读性总是要高于简洁性；用括号可以帮助我们更明确我们的意图。



# 引用

[官网地址](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-ID60)