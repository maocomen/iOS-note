[TOC]

#基础

Swift 是一种用于 iOS，macOS，watchOS 和 tvOS 应用程序开发的一门新的编程语言。尽管如此，从我们开发 C 和 Objective-C 的经验中，我们依然可以看到 Swift 中很多熟悉的地方。

对于我们在 C 或 Objective-C 中已经习惯了的类型，Swift 都提供了对应的版本，包括用 Int 代表整型，Double 和 Float 代表浮点数，Bool 代表布尔值，String 代表文本数据。Swift 还提供了三种主要集合类型的强大版本，Array ，Set 和 Dictionary，具体可以看 [Collection Types](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105) 。

像 C 一样，Swift 使用变量来存储值，通过标识名称引用值。Swift 还广泛使用了那些值不能更改的变量。它们被称作常量，比 C 中的常量更强大。当我们需要用到那些不需要改变的值时，我们可以在整个 Swift 工程中使用常量来提高代码的安全性和清晰性。

除了熟悉的类型之外，Swift 还引入了在 Objective-C 中找不到的高级类型，比如元组。元组使得我们能创建并传递一个值的分组。我们可以使用元组将多个值从函数返回为单个复合值。

Swift 还引进了可选类型，用来处理值的缺失。可选值要不“有一个值，它是X”，要不“没有值”。使用可选类型类似于 Objective-C 中的空指针，但可选类型是适用于任何类型的，不止适用于类。可选值不仅仅是比 Objective-C 中的 nil 空指针更安全，更具有表现力，它还是 Swift 中最强大的功能的核心。

Swift 是一种类型安全的语言，这意味着它可以帮我们清楚地了解代码可以使用的值的类型。如果我们的部分代码需要一个 String，如果我们错误地传入了一个 Int，那么类型安全会阻止我们。同样，类型安全也会阻止我们像一个需要非可选的字符串的代码中意外地传入一个可选字符串。类型安全有助我我们在开发过程中尽早地捕获和修复错误。

## 常量和变量

常量和变量将名称（例如 maximumNumberOfLoginAttempts 或 welcomeMessage ）与一个特定类型的值（例如数字 10 或者字符串 “Hello”）关联起来。一个常量的值一旦设置就不能改变，而变量可以在未来的某个时刻设置一个不同的值。

### 常量和变量的声明

常量和变量必须在使用前声明。我们可以用 let 关键字声明一个常量，用 var 关键字声明一个变量。下面是个例子，用来说明如何用常量和变量来追踪一个用户的尝试登陆次数：

```swift
let maximumNumberOfLoginAttempts = 10
var currentLoginAttempt = 0
```

这段代码可以解读为：

声明一个新的常数 maximumNumberOfLoginAttempts ，并给它一个值 10 。然后，声明一个新的变量 currentLoginAttempt ，并给它一个初始值 0 。

在这个例子中，允许尝试登陆的最大次数被定义成了常量，因为这个最大值是不会变化的。当前尝试登陆次数被定义成了变量，因为在每次登陆失败之后，当前尝试登陆次数都要增加。

我们可以单行声明多个常量或者变量，用逗号分割。

```swift
var x = 0.0, y = 0.0, z = 0.0
```

### 类型注释

我们在声明常量或者变量的时候，可以提供一个类型注释，以清楚常量和变量可以存储的值的类型。要写一个类型注释，我们需要在常量或变量名后面加一个冒号，然后跟一个空格，然后写需要使用的类型名称。

下面这个例子为变量 welcomeMessage 提供了一个类型注释，来指明变量只能存储 String 类型的值。

```swift
var welcomeMessage: String
```

在上述声明中，冒号的意思是“…of type…”，那么上面这句代码可以解读为：

“声明了一个 String 类型的变量 welcomeMessage. ”

短语“ of type String ”的意思是“ can store any String value .” 我们也可以认为是 “ the type of thing ”（or “ the kind of thing ”）可以被存储。

我们现在可以为变量 welcomeMessage 赋值任何字符串值而不会出现错误：

```swift
welcomeMessage = "Hello"
```

我们可以在单行上定义相同类型的多个变量，在最后一个变量名称后面写一个类型注释就好了。

```swift
var red, green, blue: Double
```

### 命名常量和变量

常量和变量名几乎可以包含任何字符，包括 Unicode 字符。

```swift
let π = 3.14159
let 你好 = "你好世界"
let 🐶🐮 = "dogcow"
```

常量和变量名不能包含空格，数学符号，箭头，私有（或无效）的 Unicode 代码点，线条或者框等图形字符。也不能以数字开头，但是数字可以写在名称的其他的地方。

一旦我们声明了某个类型的常量或者变量，我们就不能用同样的名字再次声明，或者让它存储一个不同类型的值。我们也不能将常量更改为变量或将变量更改为常量。

我们可以将现有变量的值更改为兼容类型的另一个值。在下面的例子中，变量 friendlyWelcome 的值从 "Hello!" 变成了 "Bonjour!"：

```swift
var friendlyWelcome = "Hello!"
friendlyWelcome = "Bonjour!"
// friendlyWelcome is now "Bonjour!"
```

与变量不同，常量的值一旦设置之后就不能更改了。如果我们尝试这样做的话，代码编译的时候编译器会报错。

### 打印常量和变量

我们可以使用 ==print(_:separator:terminator:)== 函数来打印常量或者变量的当前值。

```swift
print(friendlyWelcome)
// Prints "Bonjour!"
```

==print(_:separator:terminator:)== 是个全局函数，它可以将一个或者多个值打印到一个适当的输出。例如，在 Xcode  中，==print(_:separator:terminator:)== 函数会在控制台的窗口中将输出打印出来。separator 和 terminator 参数本身是有默认值的，所以我们在调用该函数的时候可以忽略掉他们。默认情况下，该函数通过加入一个换行符来终止其打印的行。如果打印完值之后，不想换行，我们可以传递一个空字符串作为终止符——例如，==print(someValue, terminator: "")==。如果我们想知道带有默认值的参数的信息，可以参考[默认参数值](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID169)。

Swift 使用`字符串插值`来在比较长的字符串中，用常量或者变量名做占位符。Swift 会迅速的将常量或者变量替换为他们的当前值。将名称用括号括起来，并在开头括号之前用一个反斜杠将其转义：

```swift
print("The current value of friendlyWelcome is \(friendlyWelcome)")
// Prints "The current value of friendlyWelcome is Bonjour!"
```

## 注释

我们可以使用注释将不可执行的文本添加到代码中，可以用来做一些笔记或者提醒自己做一些事情。在代码编译的时候，注释会被 Swift 编译器忽略掉。

Swift 中的注释和 C 中的很相似。单行注释以两个斜杠 ( // ) 开头。

```swift
// This is a comment.
```

多行注释以斜杠加星号 ( /* ) 开头，以星号加斜杠结尾 ( */ )。

```swift
/* This is also a comment
 but is written over multiple lines. */
```

与 C 中的多行注释不同，Swift 中的多行注释可以嵌套在其他多行注释当中。我们可以在已经启动的注释块中启动第二个注释块来编写一个嵌套的多行注释。关闭的时候我们需要先关闭第二个注释块，然后再关闭第一个注释块。

```swift
/* This is the start of the first multiline comment.
 /* This is the second, nested multiline comment. */
 This is the end of the first multiline comment. */
```

嵌套的多行注释可以使我们快速简单地注释掉一大段代码，即使这段代码中已经有了多行注释。

## 分号

与其他语言不同的是，在我们的代码中，我们不需要在每一个语句之后写一个 (;) ，当然如果我们非要写的话也不会有什么问题。然后，如果我们在一行上写了多个语句，就需要用分号进行分割。

```swift
let cat = "🐱"; print(cat)
// Prints "🐱"
```

## 整型

整型是没有分数分量的数，像 42 和 -23 。整型可以是有符号的（正数，0 或者负数），也可以是无符号的（正数或者 0 ）。

Swift 为有符号和无符号的整型提供了 8 位，16 位，32 位和 64 位四种形式。这些整型的命名方式类似于 C ，8 位无符号的整型类型为 UInt8，32 位有符号的整型类型为 Int32 。像 Swift 中的其他所有类型一样，这些整型类型都有他们的大写名称。

### 整型边界

我们可以使用 max 和 min 来访问每个整型类型的最大值和最小值。

```swift
let minValue = UInt8.min  // minValue is equal to 0, and is of type UInt8
let maxValue = UInt8.max  // maxValue is equal to 255, and is of type UInt8
```

这些属性的值具有适当大小的数字类型（像上面的例子中，它们的数字类型是 UInt8 ），因此它们在表达式中可以与相同类型的其他值一起工作。

### Int

在大多数情况下，我们不需要在代码中选择要使用的整型的特定大小。Swift 提供了一个额外的整数类型，Int，Int 的大小与当前本机的字节大小相同。

* 在 32 位平台上，Int 相当于 Int32
* 在 64 位平台上，Int 相当于 Int64

除非我们需要使用特定大小的整数，否则我们在代码中一直使用 Int 类型就好了。这有助于代码的一致性和互操作性。即使在 32 位平台上，Int 也可以存储 -2,147,483,648 到 2,147,483,647 中间的任何值，这已经是一个相当大的范围了，足已存储大部分的整数。

### UInt

Swift 同样提供了一个无符号的整型类型，UInt，同样，它的大小与当前本机的字节大小相同。

* 在 32 位平台上，UInt 相当于 UInt32
* 在 64 位平台上，UInt 相当于 UInt64

## 浮点数

浮点数是具有小数部分的数字，比如 3.14159 ，0.1 和 -273.5 。

比起整型，浮点类型能代表更广泛的范围的值，并且可以存储比存储在整型中更大或者更小的数值。Swift 提供了两种带符号的浮点数类型。

* Double 代表 64 位的浮点数。
* Float 代表 32 位的浮点数。

## 类型安全和类型推断

Swift 是一种类型安全的语言。一种类型安全的语言会鼓励我们清楚地了解我们的代码使用的值的类型。如果我们的代码需要一个 String ，那么我们就不能错误地传入一个 Int 。

因为 Swift 是类型安全的，它会在代码编译时执行类型检查，并将任何不匹配的类型标记为错误。这就使得我们在开发过程中能尽早的捕获并修复错误。

当我们使用不同类型的值的时候，类型检查可以帮助我们避免出错。然而，这并不意味着我们就需要指明我们声明的每个常量或者变量的类型。如果我们没有指定我们需要的值的类型，Swift 会根据类型推断来计算出一个适当的类型。类型推断使得编译器能在编译代码的时候，自动推导出特定表达式的类型，只需要检查我们提供的值就好了。

由于类型推断的存在，Swift 比 C 语言和 Objective-C 少了很多的类型声明。常量和变量的类型依然是明确的，但是大部分指定类型的操作已经帮我们完成了。

当我们声明带初始值的常量和变量的时候，类型推断尤其有用。通常是在声明常量或者变量的时候，赋了一个文本值(或文本)完成的。(文本值是在源代码中直接显示的值，例如下面例子中的 42 和 3.14159 )

举个例子，如果我们给一个常量赋了一个文本值 42 ，却没有指明它的类型，那么 Swift 推断出我们希望这个常量是 Int 类型，因为初始化的时候我们赋了一个看起来像整型的值。

```swift
let meaningOfLife = 42
// meaningOfLife is inferred to be of type Int
```

同样，如果我们赋的是个浮点数，同样没有指定类型的话，Swift 会推断出我们想创建一个 Double 类型：

```swift
let pi = 3.14159
// pi is inferred to be of type Double
```

如果推断出的类型是个浮点数的时候，Swift 会优先选择 Double（而不是 Float ）。

如果在表达式中组合整型和浮点数，那么推断出来的会是 Double 类型。

```swift
let anotherPi = 3 + 0.14159
// anotherPi is also inferred to be of type Double
```

字面值 3 本身没有确定的类型，因此，从浮点数是加法的一部分而推断出 Double 作为适当的输出类型。

## 数字字面值

整数可以写成：

* 十进制数，不加前缀
* 二进制数，加 0b 前缀
* 八进制数，加0o 前缀
* 十六进制数，加 0x 前缀

下面所有整数的十进制值都是 17 ：

```swift
let decimalInteger = 17
let binaryInteger = 0b10001       // 17 in binary notation
let octalInteger = 0o21           // 17 in octal notation
let hexadecimalInteger = 0x11     // 17 in hexadecimal notation
```

浮点数文本值可以是十进制（不带前缀）或十六进制（带 0x 前缀）。在小数点的两侧都必须有一个十进制数（或十六进制数）。十进制浮点数也可以有一个可选的指数，用大写或者小写的 e 表示；十六进制的浮点数必须具有指数，用大写或者小写的 p 表示。

对于十进制指数 exp ，结果为基数乘以 10 的 exp 次：

* 1.25e2 代表 1.25 * 10*10，或125.0
* 1.25e-2 代表 1.25/10/10，或0.0125

对于十六进制指数 exp ，结果为基数乘以 2 的 exp 次：

* 0xFp2 代表 15 * 2 * 2，或 60.0
* 0xFp-2 代表 15 ／2 ／2 ，或 3.75

下面所有浮点数的十进制值都是 12.1875 :

```swift
let decimalDouble = 12.1875
let exponentDouble = 1.21875e1
let hexadecimalDouble = 0xC.3p0
```

数字的文本值可以包含额外的格式来使得它们更容易阅读。整数和浮点数都可以通过添加额外的 0 或下划线来获得更好的可读性。两种格式都不会影响数字的基本值：

```swift
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
```

## 数值类型转换

在我们的代码中，最好使用 Int 类型来代表所有的常量和变量，即使我们知道了它是个正数。在日常环境中使用默认的整数类型代表着代码中的常量和变量可以互相操作，也可以匹配系统推断出来的整数类型。

最好只有当手头的任务特别需要的时候再使用其他整数类型，比如给外部来源规定明确的大小的数据，或者是性能、内存方面的优化，又或者其他必要的优化。在这些情况下使用显式大小的类型会帮助我们去捕获一些意外的值的溢出，还能隐式的记录使用的数据的类型。

### 整数转换

对于每个数值类型而言，可以存储的常量和变量的数值范围是不同的。对于 Int8 类型的常量和变量而言，他们存储的数值范围为 -128-127 ，但是 UInt8 类型的常量和变量可存储的数值范围为 0-255 。

在代码编译的时候，不符合一个规定大小的整数类型的常量或变量会报错：

```swift
let cannotBeNegative: UInt8 = -1
// UInt8 cannot store negative numbers, and so this will report an error
let tooBig: Int8 = Int8.max + 1
// Int8 cannot store a number larger than its maximum value,
// and so this will also report an error
```

因为每个数值类型可存储的值的范围是不同的，所以我们必须根据具体情况来选择相应的数值类型进行类型转换。这种选择方法可以防止隐藏的转换错误，并有助于我们在写代码的时候明确我们的转换意图。

要将一个特定的数值类型转换为另一个数值类型，我们可以用现有的值去初始化一个我们想得到的类型的值。在下面的例子中，`twoThousand`常量是 UInt16 类型的，但是`one`常量却是 UInt8 类型的。它们不能直接相加，因为它们的类型不一样。相反，这个例子通过调用 `UInt16(one)` 来用初始值 `one` 创建一个新的 UInt16 类型的值，来代替原来的 `one` 。

```swift
let twoThousand: UInt16 = 2_000
let one: UInt8 = 1
let twoThousandAndOne = twoThousand + UInt16(one)
```

 现在加号两边的变量都是 UInt16 类型的，所以这个加法式子是成立的。输出的常量 (twoThousandAndOne) 被推断出的类型是 UInt16 ，因为它是两个 UInt16 的值的和。

`SomeType(ofInitialValue)` 是通过默认值来初始化一个 Swift 类型的值的默认方式。在幕后，UInt16 有一个接收 UInt8 值的构造器，所以我们可以使用这个构造器来用已经存在的 UInt8 值来创建一个 UInt16 值。但是这并不代表着我们可以传入任意类型——它必须是 UInt16 的构造器已经提供的类型才行。我们可以对现有类型进行拓展，提供支持新类型的构造器（包括我们自定义的类型）覆盖之前的构造器。

### 整数和浮点转换

整数和浮点数之间的转换，类型必须明确：

```swift
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
// pi equals 3.14159, and is inferred to be of type Double
```

在这里，常量 three 的值被用来创建了一个新的 Double 类型的值，所以加号两边的值的类型是一样的。如果不做转换的话，加法式子是不成立的。

浮点数转整数的类型也必须明确。可以通过给构造器传入 Float 或 Double 参数来创建一个整数类型。

```swift
let integerPi = Int(pi)
// integerPi equals 3, and is inferred to be of type Int
```

用这种方式去构建一个整数的话，浮点数会被截断。4.75 构造的整数是 4 ，-3.9 构造的整数是 -3 。



## 类型别名

类型别名就是为现有的类型定义一个可替代的名称。我们可以用 ==typealias== 关键字来定义类型别名。如果我们想通过一个在上下文中更合适的名字去引用现有的类型的话，就可以用到类型别名了，例如我们处理来自外部源的特定大小的数据的时候：

```swift
typealias AudioSample = UInt16
```

一旦定义了一个类型别名，你可以在任何使用原本名称的地方使用类型别名。

```swift
var maxAmplitudeFound = AudioSample.min
// maxAmplitudeFound is now 0
```

在这里，AudioSample 被定义为 UInt16 的别名。因为它是个别名，所以调用 AudioSample.min 实际上是调用 UInt16.min ，它提供了一个初始值 0 给变量 maxAmplitudeFound 。



## 布尔值

Swift 具有基本的布尔类型，Bool。布尔值又被称为逻辑值，因为它们只能是真或假。Swift 提供了两个布尔常量值，true 和 false。

```swift
let orangesAreOrange = true
let turnipsAreDelicious = false
```

实际上 orangesAreOrange 和 turnipsAreDelicious 的类型被推断为 Bool，因为它们在声明的时候被赋予了布尔文本类型的初始值。就像上面提到的 Int 和 Double 一样，我们在为它们设置 true 和 false 的时候，不需要声明常量和变量的类型为 Bool ，我们可以很快速的声明一个变量或者常量。当我们使用已知类型的值去初始化一个常量或者变量的时候，类型推断会使我们的 Swift 代码更简洁，更易读。

当我们使用条件语句(比如 if 语句)的时候，布尔值尤其有用：

```swift
if turnipsAreDelicious {
    print("Mmm, tasty turnips!")
} else {
    print("Eww, turnips are horrible.")
}
// Prints "Eww, turnips are horrible."
```

在 [Control Flow](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120) 中，有对条件语句(例如 if 语句)更详细的介绍。

Swfit 的类型安全不允许非布尔值来替代 Bool。下面的例子会编译报错：

```swift
let i = 1
if i {
    // this example will not compile, and will report an error
}
```

但是下面的替代示例是有效的：

```swift
let i = 1
if i == 1 {
    // this example will compile successfully
}
```

i == 1 的比较结果是 Bool 类型，所以第二个例子能通过类型检测。[Basic Operators](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-ID60) 里对 i == 1 的相关比较做了讨论。

与 Swift 中其他类型安全的示例一样，这个方法可以避免一些意外的错误，并保证特定部分的代码意图始终清晰。

## 元组

元组是将多个值组合成单个复合值。元组中的值可以是任意类型的，而且每个值不必是相同类型。在下面的例子中，(404, "Not Found") 是一个描述 HTTP 状态代码的元组。HTTP 状态代码是 Web 服务器在请求网页时返回的特殊值。如果我们访问一个不存在的网页时，就会返回错误代码 ==404 Not Found==。

```swift
let http404Error = (404, "Not Found")
// http404Error is of type (Int, String), and equals (404, "Not Found")
```

(404, "Not Found") 元组将一个 Int 和一个 String 组合了起来，使得 HTTP 状态码有两个独立的值：一个数字和一个人类可读的描述。它可以被描述为：一个 (Int , String) 类型的元组。

我们可以使用任意类型的排列来创建元组，而且元组可以包含任意我们喜欢的类型。没有什么能阻止你拥有一个 ( Int , Int , Int ) 类型或者 ( String , Bool ) 类型的元组，或者其他你需要的排列。

我们可以将元组的内容分解成单独的常量或者变量，然后像常规一样访问它：

```swift
let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)")
// Prints "The status code is 404"
print("The status message is \(statusMessage)")
// Prints "The status message is Not Found"
```

如果我们只需要某些元组元素的值，我们可以在分解元组的时候，在需要忽略的元组元素那里用下划线  ( _ ) 表示：

```swift
let (justTheStatusCode, _) = http404Error
print("The status code is \(justTheStatusCode)")
// Prints "The status code is 404"
```

或者，我们可以使用从 0 开始的下标来访问元组中的各个元素值。

```swift
print("The status code is \(http404Error.0)")
// Prints "The status code is 404"
print("The status message is \(http404Error.1)")
// Prints "The status message is Not Found"
```

在定义元组时，我们可以给各个元素命名：

```swift
let http200Status = (statusCode: 200, description: "OK")
```

如果我们给元组中的元素命名了，那么我们可以通过元素名称来访问这些元素。

```swift
print("The status code is \(http200Status.statusCode)")
// Prints "The status code is 200"
print("The status message is \(http200Status.description)")
// Prints "The status message is OK"
```

元组在作为函数的返回值时有很大的作用。尝试检索网页的功能可能会返回 (Int, String) 的元组类型来描述页面检索的成功或失败。通过返回一个有两个不同类型、不同的值的元组，比起来返回单个类型的单个值，这个函数的结果明显可以提供更多有用的信息。有关的详细信息，可以看 [Functions with Multiple Return Values](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID164)。



## 可选值

在值可能不存在的情况下，我们可以使用可选值。可选值代表两种可能性：要么有值，我们可以对其进行解压接收，要么根本没有值。

这里有个例子，来说明在没有值的情况下，如何用可选值来处理。Swift 中的 Int 类型有一个初始化器来尝试将 String 类型的值转换为 Int 类型。然而，不是所有的字符串都可以转化为整数的。字符串 “123” 可以转化为数字 123 ，但是字符串 “hello world” 没有明显的数字去转化。

下面这个例子就是如何将 String 转化为 Int 。

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)
// convertedNumber is inferred to be of type "Int?", or "optional Int"
```

因为初始化可能会失败，所以会返回一个可选的 Int 而不是 Int 值。一个可选的 Int 需要写成 Int？而不是 Int 。问号表明它包含的值是可选的，意味着它可能会包含一个 Int 值，也可能没有值。（它不能包括其他类型的值，比如说 Bool 值 或者 String 值。它只能包含 Int 值，或者没有值）

### nil

我们可以通过赋予一个特殊的值 nil 来设置一个无价值状态的可选变量。

```swift
var serverResponseCode: Int? = 404
// serverResponseCode contains an actual Int value of 404
serverResponseCode = nil
// serverResponseCode now contains no value
```

如果我们在不提供默认值的情况下定于可选变量，那么该变量会自动置为 nil 。

```swift
var surveyAnswer: String?
// surveyAnswer is automatically set to nil
```

### if 语句和强制展开

我们可以在 if 语句中将可选值与 nil 进行比较来判断可选值是否包含一个值。我们可以通过 "equal to"(==) 或 "not equal to"(!=) 来执行比较。

如果可选值有值，会被认为与 nil 不相等。

```swift
if convertedNumber != nil {
    print("convertedNumber contains some integer value.")
}
// Prints "convertedNumber contains some integer value."
```

一旦我们确认了可选值包含一个值，那么我们可以通过在可选值的名称后面加一个感叹号 (!) 来接受它的潜在值。这个感叹号表示：我知道这个可选值肯定包含一个值，请使用它。这被称为强制展开可选项的值：

```swift
if convertedNumber != nil {
    print("convertedNumber has an integer value of \(convertedNumber!).")
}
// Prints "convertedNumber has an integer value of 123."
```

有关 if 语句的更多信息，可以查看 [Control Flow](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120)。

### 可选绑定

我们可以使用可选绑定来判断一个可选值是否包含值，如果是，那么该值用作临时变量或常量。可选绑定可以使用在 if 或 while 条件语句中，用来判断一个可选值是否包含值，并将该值提取为常量或者变量，作为单个操作的某个部分。[Control Flow](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120) 中更详细的描述了 if 和 while 语句。

在 if 语句中，像下面这样做一个可选绑定：

```swift
if let constantName = someOptional {
    statements
}
```

我们可以用可选绑定而不是强制展开重写一下一开始[可选值](#可选值)的那个例子：

```swift
if let actualNumber = Int(possibleNumber) {
    print("\"\(possibleNumber)\" has an integer value of \(actualNumber)")
} else {
    print("\"\(possibleNumber)\" could not be converted to an integer")
}
// Prints ""123" has an integer value of 123"
```

这段代码可以解释为：

"如果 Int(possibleNumber) 的返回值 (一个可选值 Int ) 包含一个值，那么会把这个值赋给一个新的常量 actualNumber 。"

如果转换是成功的，那么 actualNumber 常量可以被用于 if 语句的第一个分支中。因为它已经被包含在可选内容中的值初始化了，所以没必要再添加 ！后缀去访问它的值。在这个例子中，actualNumber 只是用来打印转换的结果。

可选绑定的时候，我们可以用常量也可以用变量。如果我们想操作 if 语句第一个分支中的值，那么我们可以用 if var actualNumber 替换 if let actualNumber ，那么可选项所包含的值就会赋给一个变量而不是一个常量了。

我们可以在单个 if 语句中尽可能多的包含我们需要的可选绑定和布尔判断，用逗号分隔即可。如果任意一个可选绑定的值是 nil 或者任意一个布尔判断为 false ，那么整个 if 语句的条件被认为是 false 。下面的两个 if 语句是等效的：

```swift
if let firstNumber = Int("4"), let secondNumber = Int("42"), firstNumber < secondNumber && secondNumber < 100 {
    print("\(firstNumber) < \(secondNumber) < 100")
}
// Prints "4 < 42 < 100"
 
if let firstNumber = Int("4") {
    if let secondNumber = Int("42") {
        if firstNumber < secondNumber && secondNumber < 100 {
            print("\(firstNumber) < \(secondNumber) < 100")
        }
    }
}
// Prints "4 < 42 < 100"
```



### 可选项的隐式展开

如上所述，可选项表明常量和变量可以“无值”。在 if 语句中我们可以检测可选项的值是否存在，如果值真的存在，我们还可以有条件地使用可选绑定来接收它。

有时候，我们从程序的结构可以清楚的看到，某个可选值在第一次被赋值之后，会始终拥有一个值。在这种情况下，我们就很有必要去掉每次接受可选值时对它的检测和强制展开了，因为我们可以安全的认为它任何时候都是有值的。

这种可选项被定义为可选项的隐式展开。我们需要在那些我们选择的类型后面加入一个感叹号 (String!) 而不是 (String?) 来编写一个可隐式展开的可选项。

当一个可选值在第一次被定义之后就可以立即确定它的值，并且在以后的任何一个时间点它都是有值的，那么就可以用到可选项的隐式展开了。在 Swift 中，可选项的隐式展开主要用在构造器方法中，详情可以看 [Unowned References and Implicitly Unwrapped Optional Properties](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html#//apple_ref/doc/uid/TP40014097-CH20-ID55) 。

一个隐式展开的可选值在幕后其实就是个普通的可选值，但是它可以像非可选值一样来使用，我们在每次接收其值的时候可以不用对它进行展开。下面的例子展示了当可选字符串和隐式展开的可选字符串作为一个显示的字符串去访问它们的值的时候之间的差异。

```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // requires an exclamation mark
 
let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // no need for an exclamation mark
```

我们可以将可选项的隐式展开想象成：在每次使用可选项的时候，可选项会自动进行展开。我们不需要在每次使用的时候都在可选项的名称后面加感叹号了，只需要在定义可选项的时候，在它的名字后面加个感叹号就可以了。

如果我们想检测一个隐式展开的可选项有没有包含值，我们可以像常规的可选项一样来进行处理：

```swift
if assumedString != nil {
    print(assumedString)
}
// Prints "An implicitly unwrapped optional string."
```

我们还可以对隐式展开的可选值使用可选绑定，以在单个语句中对它包含的值进行检测和展开：

```swift
if let definiteString = assumedString {
    print(definiteString)
}
// Prints "An implicitly unwrapped optional string."
```

## 异常处理

我们可以使用异常处理来响应那些程序在执行过程中可能会出现的错误情况。

与可选值相比，可选值是通过判断函数的返回值是否存在来决定成功还是失败，而异常处理允许我们确定失败的根本原因，在需要的时候，我们甚至能把错误传递到程序的另外一个部分。

当一个函数遇到错误条件时，会抛出异常。函数的调用者可以捕获异常并做出适当的响应。

```swift
func canThrowAnError() throws {
    // this function may or may not throw an error
}
```

函数可以通过在声明中包含 "throws" 关键字来表示可能会抛出异常。当我们在调用一个可能会抛出异常的函数的时候，我们可以在表达式中使用 "try" 关键字。

Swift 会自动将异常传播到当前范围之外，直到它们被 catch 语句处理为止：

```swift
do {
    try canThrowAnError()
    // no error was thrown
} catch {
    // an error was thrown
}
```

do 语句会创建一个新的包含范围，这个包含范围允许异常被传播到一个或多个 catch 子句中。

以下是一个如何使用异常处理来响应不同的错误条件的示例：

```swift
func makeASandwich() throws {
    // ...
}
 
do {
    try makeASandwich()
    eatASandwich()
} catch SandwichError.outOfCleanDishes {
    washDishes()
} catch SandwichError.missingIngredients(let ingredients) {
    buyGroceries(ingredients)
}
```

在这个例子中，如果没有干净的盘子可以用或者缺少任何材料，makeASandwich() 方法都会抛出一个异常。因为  makeASandwich() 函数有可能会抛出异常，所以需要被封装在一个 try 表达式中。通过在 do 语句中封装函数的调用，所有的异常都会被抛出并传播到 catch 子句中。

如果没有抛出异常，那么 eatASandwich() 将会被调用。如果抛出了一个符合 SandwichError.outOfCleanDishes 的异常，那么 washDishes() 函数将会被调用。如果抛出了一个符合 SandwichError.missingIngredients 的异常，那么 buyGroceries(_:) 函数将会被调用，并传入通过 catch 捕获的异常的 [String] 关联值。

[Error Handling](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html#//apple_ref/doc/uid/TP40014097-CH42-ID508) 中更详细地介绍了如何抛出，捕获和传递异常。

## 断言和先决条件

断言和先决条件是在运行时发生的检查。在执行任何下一步的代码之前，我们都可以使用它们来确保满足基本条件。如果断言和先决条件中的布尔条件被推断为 true ，代码会正常执行。如果布尔条件被推断为 false ，程序的当前状态无效；代码不会继续执行，应用程序终止。

在编写代码的时候，我们可以使用断言和先决条件来表达我们的设想和期望，所以我们可以把它看作代码的一部分。断言可以帮我我们发现开发过程中的错误和错误的假设，先决条件可以帮助我们发现生产中的问题。

处了可以在运行时确认我们的期望之外，断言和先决条件也成为了代码中有效的文档格式。与上述 [Error Handling](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html#//apple_ref/doc/uid/TP40014097-CH42-ID508) 中讨论的错误条件不同，断言和先决条件不能用于可恢复或者预期的错误。因为失败的断言和先决条件代表无效的程序状态，所以无法捕获失败的断言。

断言和先决条件并不是代码设计的替代品，使用了它们也不代表着无效的条件就不可能会出现。然而，使用它们去强制执行有效的数据和状态的时候，如果无效的状态发生，会使得应用发生可预期的终止，而且会使得我们更容易去调适问题。一旦检测到无效状态，停止执行也有助于限制由该无效状态带来的损害。

断言和先决条件的区别在于它们的检测时期：断言只在调试版本中进行检查，但是先决条件在调试和生产版本中都会检查。在生产版本中，断言中的条件不会被评估。这意味着我们可以在开发过程中尽可能多地使用断言，而不会影响产品的性能。

### 用断言进行调试

我们可以从 Swift 标准库中调用 [assert(_:_:file:line:)](https://developer.apple.com/documentation/swift/1541112-assert) 函数来书写一个断言。我们会向断言中传递一个值可能是 true 和 false 的函数表达式，如果表达式的结果被推断为 false 的话，会展示一条消息。例如：

```swift
let age = -3
assert(age >= 0, "A person's age can't be less than zero.")
// This assertion fails because -3 is not >= 0.
```

在这个例子中，如果 age >= 0 被推断为 true ，也就是说，age 是个非负值，那么代码将会继续执行。如果 age 的值是负数，也就是说上面代码中 age >= 0 被推断为 false ，断言将会失败，程序终止。

==我们可以省略断言的消息——例如，当它只需要判断条件的时候。== （不是很理解官方英文意思）

```swift
assert(age >= 0)
```

如果我们已经用代码判断过条件了，那么我们可以使用 [assertionFailure(_:file:line:)](https://developer.apple.com/documentation/swift/1539616-assertionfailure) 函数来表示断言失败。例如：

```swift
if age > 10 {
    print("You can ride the roller-coaster or the ferris wheel.")
} else if age > 0 {
    print("You can ride the ferris wheel.")
} else {
    assertionFailure("A person's age can't be less than zero.")
}
```

### 执行先决条件

当判断条件有可能是 false 的时候，我们可以使用先决条件，我们需要保证的是，代码只有在判断条件为 true 的时候才会继续执行。例如，我们可以使用先决条件来判断下标是否越界，或者来判断是否给函数传入了一个有效的值。

我们可以通过调用 [precondition(_:_:file:line:)](https://developer.apple.com/documentation/swift/1540960-precondition) 函数来书写一个先决条件。我们可以向这个函数中传递一个表达式，这个表达式可以被推断为 true 或 false ，如果判断条件的结果被推断为 false ，那么会展示一条消息。例如：

```swift
// In the implementation of a subscript...
precondition(index > 0, "Index must be greater than zero.")
```

我们也可以调用 [preconditionFailure(_:file:line:)](https://developer.apple.com/documentation/swift/1539374-preconditionfailure) 函数来表明一个错误已经发生——例如，switch 中的默认情况会抛出一个错误，那么其他有效数据的处理就得交给 Switch 的其他情况来处理。



# 引用

[官网地址](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)


