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

* 棋盘拥有 25 个格子，目标就是到达或者超过第 25 号格子。
* 玩家的起始位置是 “第 0 号格子”，它位于棋盘的左下角。
* 每一回合，我们都需要投掷一个骰子（六个面，6 个面分别写着 1-6 的数字，最后骰子停下来时，朝上那面所展示的数字就是我们要移动的格子数），然后按照上面虚线所指示的路径移动指定数量个格子。
* 如果我们停留在了梯子的下面，我们就可以顺着梯子爬上去。
* 如果我们停留在了蛇的头部，那么我们就必须沿着蛇滑下来。

在下面的例子中，游戏棋盘用一个 Int 数组表示。数组的大小基于一个叫做 finalSquare 的常量，例子中用这个常量来初始化数组并用其作为检查是否胜利的条件。因为玩家在棋盘上的起始点是 “第 0 号格子”，所以初始化后的数组需要有 26 个值为 0 的 Int 值，而不是 25 个。

```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
```

有些格子需要被当作蛇和梯子，所以要给他们设置一些特定值。作为梯子底座的格子需要设置成正数来方便我们在棋盘向上移动，作为蛇头的格子需要设置成负数方便我们在棋盘上向下移动。

```swift
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
```

3 号格子需要作为梯子的底部，如果移动到该处，它会把我们移动到 11 号方格。为了达到这个目的，我们将 board[03] 设置为了 +08，代表它等于一个整数值 8（3 和 11 之间的差值）。为了对齐值和语句（看起来更好看一些），这里使用了一元加号运算符 (+i) 和一元减号运算符 (-i) ，而且小于 10 的数字用了 0 来填充成两位数。（严格的说，这两种做法都不是必须的，但是他们会使我们的代码看起来更整洁优雅。）

```swift
var square = 0
var diceRoll = 0
while square < finalSquare {
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
    if square < board.count {
        // if we're still on the board, move up or down for a snake or a ladder
        square += board[square]
    }
}
print("Game over!")
```

上面的例子使用了一种很简单的额方法来模拟骰子的投掷。它没有用到随机数，而是让 diceRoll 从 0 开始。每次执行 while 循环中的代码，diceRoll 都会加 1，然后判断它有没有变的太大。每当这个变量的值返回为 7 时，就会认为 diceRoll 变得太大了，从而将它重置为 1。diceRoll 的值会形成一个有序的序列，它的值将始终是 1，2，3，4，5，6。。。

骰子投掷完之后，玩家会移动 diceRoll 个格子。玩家移动完之后很可能已经超出了第 25 号格子，这种情况下游戏其实已经结束了。为了应对这种情况，代码里做了 square 是否小于数组 board 的 count 属性的检查。如果 square 小于 board.count ，存储在 board[square] 中的值将会被添加到当前的 square 值上，来表示玩家在梯子或者蛇上的上下移动。

> 如果没有执行这个检查的话，board[square] 这行代码很可能会接收到一个超出了 board 数组范围的额值，这会引起一个运行时错误。

当前 while 循环执行结束，然后会判断循环条件，看是否需要执行下一次循环。如果玩家已经移动到或者超过了第  25 号格子，循环条件会被评估为 false，游戏结束。

在这种情况下，使用 while 循环是比较好的一种方案。因为在 while 循环开始时，我们并不能确定游戏的长度（这个循环需要执行几次）。相反的是，我们很清楚地知道循环执行需要的条件，循环将一直执行，直到满足特定的条件为止。

### Repeat-While

while 循环的另一种形式，就是 repeat-while 循环了，在判断循环条件之前就会执行一次循环代码块。然后会继续重复循环，直到判断条件为 false 为止。

> Swift 中的 repeat-while 循环类似于其他语言中的 do-while 循环。

这是 repeat-while 循环的通用形式：

```swift
repeat {
    statements
} while condition
```

还是以 *Snakes and Ladders* 为例，这次用 repeat-while 循环来代替 while 循环。finalSquare，board，square 和 diceRoll 初始化的方式与 while 中的完全相同。

```swift
let finalSquare = 25
var board = [Int](repeating: 0, count: finalSquare + 1)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
```

在这个版本的游戏中，循环的第一个动作（这个动作指的就是循环代码块，repeat-while 会先执行一次循环代码块，再进行条件判断，再执行循环代码块，再进行条件判断。。。循环往复）是检查是否是梯子和蛇。棋盘上没有梯子可以直接将玩家移动到第 25 号格子，所以不可能通过踩中梯子向上移动而直接赢得游戏。因此，以检查梯子和蛇作为循环的第一个动作是安全的。

在游戏开始时，玩家处于 “第 0 号格子”。board[0] 始终是 0 所以不会有什么影响。

```swift
repeat {
    // move up or down for a snake or ladder
    square += board[square]
    // roll the dice
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    // move by the rolled amount
    square += diceRoll
} while square < finalSquare
print("Game over!")
```

在检查完是否是梯子和蛇之后，就到了掷骰子环节，掷完骰子之后，玩家会向前移动 diceRoll 个格子，然后当前循环结束。

循环条件（while square < finalSquare）和之前的一样，但是这次直到第一次循环结束时才会对它进行判断。与前面 while 循环的例子相比，repeat-while 循环的结构更适合这个游戏。在上面 repeat-while 循环的例子中，square += board[square] 总是会在 while 循环的循环条件判断为 true （即 square 仍在棋盘上）之后立即执行。这个行为就移除掉了之前 while 循环版本的游戏中对数组边界检查的需要。

## 条件语句

很多时候根据特定的条件执行不同的代码是很有用的。我们可能需要在发生错误的时候执行额外的代码，又或者在值变得太高或者太低的时候展示一条消息提示。为了达成这个目的，我们需要将我们的代码的一部分设置为条件（即在我们的代码中增加一些代码来作为条件判断语句）。

Swift 提供了两种方法来为代码添加条件分支：也就是 if 条件语句和 switch 条件语句。通常，if 条件语句更适合只有少量结果的简单条件。而 switch 更适合具有多种排列可能的更复杂的条件，并且 switch 在模式匹配情况下更加有用，它可以帮助我们选择适当的代码分支来执行。？？？？？

### If

在最简单的形式中，if 语句只有一个判断条件。它只会在条件判断为 true 的情况下才会执行相应的代码块。

```swift
var temperatureInFahrenheit = 30
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
}
// Prints "It's very cold. Consider wearing a scarf."
```

在上面的例子中，只对温度是否小于等于 32 华氏摄氏度（水的冰点）做了判断。如果判断为 true ，才会打印一条消息。否则，不会打印任何消息，并且会继续执行 if 语句大括号之后的代码。（这里无论判断为 true 还是 false，都会继续执行大括号之后的代码）

if 语句可以为 if 条件为 false 的情况提供另外一组语句，也就是 else 语句。这些语句由 else 关键字表示。

```swift
temperatureInFahrenheit = 40
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's not that cold. Wear a t-shirt."
```

上面的例子中，始终会执行两个分支中的其中一个。因为温度已经提高到了 40 华氏摄氏度，没那么冷了，已经不需要再戴围巾保暖了，所以 else 分支就被激活了。

我们可以将多个 if 语句连接起来，用来添加多个子句。

```swift
temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// Prints "It's really warm. Don't forget to wear sunscreen."
```

在这里，增加了一个 if 语句来处理特别暖的气温。最好一个 else 子句仍然存在，它用来响应那些不是太冷也不是太温暖的气温。最后一个 else 子句是可选的，如果我们不需要完成其条件集，可以将其忽略。

```swift
temperatureInFahrenheit = 72
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
}
```

由于温度不是太冷也不是太热，所以不会触发 if 和 else if 语句，不会打印任何信息。

### Switch

Switch 会尝试将一个值与若干个模式进行匹配。它只会执行第一个与之匹配成功的模式下的代码块。如果代码中需要处理在多种潜在状态下的不同行为，最好用 switch 语句代替 if 语句。

Switch 最简单的形式就是把某个值与一个或若干个相同类型的值进行比较:

```swift
switch some value to consider {
case value 1:
    respond to value 1
case value 2,
     value 3:
    respond to value 2 or 3
default:
    otherwise, do something else
}
```

每个 switch 语句都有多种可能匹配到的模式 ( cases )，每个模式都以 case 关键字作为开头。除了与特定的值进行比较之外，Swift 还为每种情况提供了几种方法来处理更复杂的匹配模式。这些会在本章后面进行介绍。

与上文中 if 语句单元中介绍的类似，每个 case 都是一个代码执行的单独分支。switch 语句会决定最后到底使用哪个分支。这个过程被称为根据给定的值进行分支切换。

每个 switch 语句的声明都必须详尽无疑。也就是说，每个可能的值都必须与 switch 的一种模式相匹配。如果不方便为每个可能的值都匹配一种模式，我们可以用默认模式来处理那些没有显式声明的任意值。默认模式由 default 关键字修饰，而且一般默认模式都写在 switch 语句的最后位置。

下面的例子用 switch 语句来匹配一个名为 someCharacter 的小写字符:

```swift
let someCharacter: Character = "z"
switch someCharacter {
case "a":
    print("The first letter of the alphabet")
case "z":
    print("The last letter of the alphabet")
default:
    print("Some other character")
}
// Prints "The last letter of the alphabet"
```



