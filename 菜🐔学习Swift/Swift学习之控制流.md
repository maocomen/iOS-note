[TOC]

# 控制流

Swift 提供了多种控制流语句。包括用来执行多次任务的 while 循环；基于特定的条件执行不同的分支任务的 if ，guard 和 switch；以及像 continue 和 break 这些将执行流转移到代码中另一个点的语句。

Swift 同时提供了 for-in 循环，让我们可以更简单地对数组、字典、范围、字符串和其他序列进行遍历。

Swift 中的 switch 语句比许多类 C 的语言中的 switch 语句强大的多。Swift 中的 case 可以匹配多种不同的模式，包括区间匹配，元组和特定类型的强制转换。Switch 中 case 中的匹配值可以绑定到临时常量或变量上，方便我们在 case 正文中使用，如果需要判断一些复杂的匹配条件，我们可以在 case 中用 where 语句表示。

## For-In 循环

我们可以使用 for-in 循环来对一个序列进行遍历，比如数组中的元素，数字的范围或者字符串中的字符。

下面这个例子为我们示范了如何使用 for-in 循环来遍历数组中的元素：

```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!
```

我们也可以对字典进行遍历来访问其键值对。对字典进行遍历的时候，字典中的每个元素会被当作一个元组(key, value) 作为返回，我们需要将元组 (key, value) 中的成员分解为显示命名的常量，才能在 for-in 循环体中使用它们。在下面的例子中，字典中的键被分配给了一个叫 animalName 的常量，字典中的值被分配给健做 legCount 的常量。

```swift
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}
// ants have 6 legs
// cats have 4 legs
// spiders have 8 legs
```

字典的内容本质上是无序的，所以我们对其进行遍历的时候并不能保证检索它们的顺序。需要特别注意的是，元素插入到字典中的顺序并不能代表他们遍历时的顺序。有关数组和字典的更多信息，可以查看 [Collection Types](https://docs.swift.org/swift-book/LanguageGuide/CollectionTypes.html)。

for-in 循环同样能用来遍历数字区间，下面这个例子打印了乘五表中的前几项:

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

被遍历的序列是 1 到 5 的数字范围，这里因为要包含 1 和 5 两个值，所以要使用闭合区间运算符 (...)。index 的值一开始会被设为范围内的第一个值，也就是 1，然后执行循环中的语句。在上面的例子中，循环中只包含一条语句，语句中会打印 index 的当前值对应的那个条目。在语句执行完成后，index 的值会被更新为范围中的额第二个值，也就是 2，并且 print(_:separator:terminator:) 函数会被再一次调用。这个过程会一直持续到范围结束。

在上面的例子中，index 是一个常量，它的值在每次循环迭代开始的时候自动进行设置。所以，index 不需要在使用它之前进行声明。在循环的声明中它被隐式地声明了，不需要再次调用 let 关键字来进行声明。

如果我们在遍历时不需要用到序列中的值，可以使用下划线来代替原本的变量值，以达到忽略这些值的目的。

```swift
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}
print("\(base) to the power of \(power) is \(answer)")
// Prints "3 to the power of 10 is 59049"
```

上面这个例子计算了 1 个数字的幂的值（3 的 10 次幂）。它使用以 1 作为开始值，10 作为结束值的闭合范围，以 1（3 的 0 次幂）乘 3 作为开始，循环了 10 次。对于这种计算方式每次循环时计数器的值就没那么必要了——代码只要执行正确的次数就可以得到正确的结果。用于代替循环变量的下划线字符 (_) 会忽略循环变量，并且在每次循环过程中也不会提供对当前值的访问。

在某些情况下，我们可能不想使用包含开始和结束两个端点的闭合区间。我们可以以绘制表盘上的分钟刻度线为例。我们需要绘制 60 个刻度线，从 0 开始，但不以 60 作为结束。那么我们就可以使用半开区间运算符 ( ..< )，包括下限但不包括上限。有关范围的更多信息，可以查看 [Range Operators](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html#ID73)。

```swift
let minutes = 60
for tickMark in 0..<minutes {
    // render the tick mark each minute (60 times)
}
```

某些用户可能希望其 UI 中的刻度线再少一点。他们可能更喜欢每五分钟做一个标记。使用 stride(from:to:by:) 函数可以跳过那些不需要的标记。

```swift
let minuteInterval = 5
for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
    // render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
}
```

闭合区间也支持这个功能，通过使用 stride(from:through:by:) 代替:

```swift
let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // render the tick mark every 3 hours (3, 6, 9, 12)
}
```

## While 循环

whild 循环会执行一组语句，直到判断条件变成 false 为止。当我们在第一次迭代开始之前并不能准确地知道迭代次数时，最好使用这种循环。Swift 提供了两种 while 循环:

* 在每次循环开始时都检测其判断条件的 while 循环
* 在每次循环结束时才检测其判断条件的 repeat-while 循环

### While

while 循环在开始时就会对其条件进行评估。如果条件被判断为 true，则重复一组语句，直到判断条件变为 false。

这是 while 循环的一般方式:

```swift
while condition {
    statements
}
```

我们可以先来玩一个叫蛇与梯子的小游戏（也被称为滑道和梯子）:

![](https://docs.swift.org/swift-book/_images/snakesAndLadders_2x.png)

游戏规则如下:

* 