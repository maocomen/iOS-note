[TOC]

# 字符串和字符

字符串其实是一系列的字符，比如 "hello, world" 或者 "albatross" 。Swift 中的字符串由 String 类型表示。String  的内容可以以多种形式进行访问，包括 Character 的值的集合。

Swift 的字符串和字符提供了一种快速的，符合 Unicode 的方式来处理代码中的文本。字符串创建和操作的语法是轻量级的和可读的，具有与 C 类似的字符串文字语法。字符串的组合简单到我们用 + 号就可以将两个字符串拼接起来，我们可以通过选择该字符串是常量还是变量来决定该字符串是否可变，就像 Swift 中的任何其他值一样。我们还可以使用字符串将常量、变量、文本和表达式插入更长的字符串中，这种操作叫做字符串插值。它会使得我们更简单地去创建一些用来做展示，存储和打印功能的自定义的字符串。

尽管语法简单，但是 Swift 中的字符串仍然是一个快速、现代化的字符串实现。每个字符串都由编码独立的 Unicode 字符组成，并提供了在各种不同的 Unicode 表示中访问这些字符的支持。

## 字符串字面值

我们可以在我们的代码中使用预定义的 String 值（字符串字面值）。字符串字面值是由双引号 (") 包围的字符序列。

我们可以用字符串字面值来作为常量或者变量的初始值：

```swift
let someString = "Some string literal value"
```

注意，Swift 会将 someString 推断为 String 类型，因为常量 someString 是用字符串字面值作为初始值而初始化的。

### 多行字符串字面值

 如果需要一个跨多行的字符串，请使用多行字符串字面值——一个被三个双引号包围的字符串序列：

```swift
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.
 
"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```

多行字符串字面值包括其开头和结尾引号之间的所有行。字符串会以开头引号下的第一行作为开始，以结束引号上的最后一行作为结束，这意味着以下两种字符串都不会以换行符开始或结尾：

```swift
let singleLineString = "These are the same."
let multilineString = """
These are the same.
"""
```

当我们的源代码在多行字符串字面值中包含换行符时，该换行符也会显示在字符串的值中。如果我们需要使用换行符来使得我们的代码更简单易读，却不希望换行符成为字符串字面值的一部分，我们可以在每行的末尾插入反斜杠 (\\)。

```swift
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.
 
"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

要使用换行符开头或结尾的多行字符串字面值，我们可以在第一行或者最后一行输入一个空白行。例如：

```swift
let lineBreaks = """
 
This string starts with a line break.
It also ends with a line break.
 
"""
```

多行字符串可以用缩进来匹配周围的代码。在结束引号之前的空白 ("") 会告诉 Swift，应该在其他行也忽略哪些空白。然而，如果我们在每一行的开头都写了空格 (除了结束引号之前)，那么空格会被包括在字符串内容中。

![图片1](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Art/multilineStringWhitespace_2x.png)

在上面的例子中，整个多行字符串字面值是缩进的，第一行和最后一行都不会以空格开头。中间那行的缩进比结束引号之前的缩进要多，所以它会以额外的 4 格缩进开头。

### 字符串字面值中的特殊字符

字符串字面值中可以包含以下特殊字符：

* ```
  转义的特殊字符  \0 (空字符)  \\(反斜杠) \t(水平选项卡) \n(换行符) \r(回车) \"(双引号) \'(单引号)
  ```

* ```
  任意的 Unicode 标量，写作 \u(n)，n 的地方是一个 1-8 位的 16 进制数，它的值等于一个有效的 Unicode 编码点
  ```

Unicode 会在下文的 [Unicode](#Unicode) 中进行讨论。

下面的代码展示了 4 个特殊字符的例子。wiseWords 常量包含了两个转义的双引号字符。dollarSign，blackHeart 和 sparklingHeart 常量展示了 Unicode 标量格式：

```swift
let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
// "Imagination is more important than knowledge" - Einstein
let dollarSign = "\u{24}"        // $,  Unicode scalar U+0024
let blackHeart = "\u{2665}"      // ♥,  Unicode scalar U+2665
let sparklingHeart = "\u{1F496}" // 💖, Unicode scalar U+1F496
```

因为多行字符串字面值使用的是三个双引号而不是一个，所以我们可以在多行字符串字面值中包含一个双引号而不对其进行转义。但是如果我们想包含另外一个三个双引号，我们至少需要对其一个双引号进行转义。例如：

```swift
let threeDoubleQuotes = """
Escaping the first quote \"""
Escaping all three quotes \"\"\"
"""
```

## 初始化一个空字符串

如果我们需要创建一个空字符串来作为构建一个长字符串的起始点，我们可以将一个空的字符串字面值赋值给一个变量，或者我们可以使用初始化语法来构建一个 String 实例：

```swift
var emptyString = ""               // empty string literal
var anotherEmptyString = String()  // initializer syntax
// these two strings are both empty, and are equivalent to each other
```

如果我们想知道一个 String 类型的实例有没有值，可以调用它的布尔值属性 isEmpty 。

## 字符串的可变性

我们可以通过把特定的 String 值分配给变量（这种情况下可被修改）或者常量（这种情况下不能被修改）来指定该 String 值是否可以被修改（或可变）。

```swift
var variableString = "Horse"
variableString += " and carriage"
// variableString is now "Horse and carriage"
 
let constantString = "Highlander"
constantString += " and another Highlander"
// this reports a compile-time error - a constant string cannot be modified
```

## String 是值类型

Swift 中的 String 是值类型。如果我们创建一个新的 String 值，那么该 String 值在传递给函数或者方法，又或者是给变量或者常量赋值的时候都会进行一次拷贝。在任何情况下，都会创建一个已有 String 值的新副本，并且在传递或赋值过程中使用的都是新副本而不是旧副本。值类型在 [Structures and Enumerations Are Value Types](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-ID88) 中有介绍。

Swift 中会对 String 类型进行默认拷贝，这种行为可以保证，当一个函数或方法在传递一个 String 值的时候，我们可以很清楚的知道这个字符串确切的值，不管它来自哪里。我们也可以确信，除非我们自己对其进行修改，否则这个值在传递过程中永远不会改变。

在幕后，Swift 编译器优化了字符串的使用，以便实际的复制只在必要的时候进行。这意味着在使用身为值类型的字符串的时候，我们总是能获得很好的性能。

## 使用字符

我们可以通过一个 for-in 循环遍历字符串来访问字符串的单个字符值：

```swift
for character in "Dog!🐶" {
    print(character)
}
// D
// o
// g
// !
// 🐶
```

for-in 循环在 [For-In Loops](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID121) 有介绍。

或者，我们可以通过提供一个 Character 类型注释，从单个字符串字面值上创建一个独立的字符常量或变量。

```swift
let exclamationMark: Character = "!"
```

我们可以通过将一个元素都是 Character 值的数组作为参数传递给 String 的构造器来构造一个 String 类型的值。

```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)
// Prints "Cat!🐱"
```



## 连接字符串和字符

我们可以用加号运算符 (+) 将字符串的值加在一起（连接在一起），来形成一个新的字符串值：

```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome now equals "hello there"
```

我们也可以使用复合复制运算符 (+=) 将一个字符串值拼接到一个已经存在的字符串值的后面。

```swift
var instruction = "look over"
instruction += string2
// instruction now equals "look over there"
```

我们可以使用 String 类型的 `append()` 方法来为 String 类型的变量拼接一个 Character 类型的值：

```swift
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome now equals "hello there!"
```

如果我们要使用多行字符串文本值来创建一个更长的字符串，则需要字符串中的每一行都以换行符结束，包括最后一行。例如：

```swift
let badStart = """
one
two
"""
let end = """
three
"""
print(badStart + end)
// Prints two lines:
// one
// twothree
 
let goodStart = """
one
two
 
"""
print(goodStart + end)
// Prints three lines:
// one
// two
// three
```

在上面的代码中，badStart 与 end 连接产生了一个两行的字符串，这不是我们需要的结果。因为 badStart 的最后一行不是以换行符作为结束的，所以该行会与 end 的第一行进行组合。相反的是，goodStart 的每一行都是以换行符作为结束的，所以它与 end 进行组合的结果会跟我们期望的一样，是一个三行的字符串。

## 字符串插值

字符串插值是一种从常量、变量、文字和表达式的混合中构造出一个新的 String 值的方法。我们可以在单行和多行字符串字面值中使用字符串插值。每一个插入到字符串字面值中的元素都必须被一对圆括号包围，并以反斜杠 `(\)` 作为前缀。 

```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message is "3 times 2.5 is 7.5"
```

在上面的例子中，multiplier 的值插入字符串中的时候需要写成 `\(multiplier)` 。当需要计算字符串插值的实际值来创建字符串的时候，这个占位符会被替换为 multiplier 的实际值。

multiplier 同样是上面字符串后面大表达式中的一部分。这个表达式会计算 `Double(multiplier) * 2.5`  的值并在字符串中插入计算后的结果 (7.5) 。在这个例子中，当表达式需要被包含在字符串字面值中时，需要写作 `\(Double(multiplier) * 2.5) `  。

## Unicode

Unicode 是一种用于在不同的书写系统中编码，标示和处理文本的国际统一标准。它使得我们能几乎能从任何语言中以标准化的形式表示任何字符，并从外部源（文本文件或 web 页面）读取和写入这些字符。Swift 中的 String 和 Charactor 类型完全符合 Unicode，如本节所述。

### Unicode 

实际上，Swift 中的原生 String 类型是以 Unicode 标量值构建的。一个 Unicode 标量是具有唯一 21-bit 数字的字符串或修饰符，例如 U+0061代表拉丁小写字母 A（LATIN SMALL LETTER A ("a")），U+1F代表小鸡（FRONT-FACING BABY CHICK ("🐥")）。

Unicode标量是指在U+0000到U+D7FF或U+E000到U+10FFFF的范围内的任何Unicode编码点。Unicode标量不包括Unicode代理对代码点，这是在U+D800到U+DFFF的范围内的代码点。

不是所有的 21-bit Unicode 标量都被分配给了一个字符的—一些未分配的标量是为了未来的赋值预留的。被分配给了一个字符的标量通常会有一个名称，比如上面例子中的 LATIN SMALL LETTER A 和 FRONT-FACING BABY CHICK。

### 拓展的字形集群

Swift 中的每个 Character 实例都代表一个拓展的字形集群。

# 引用

[官方链接](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html#//apple_ref/doc/uid/TP40014097-CH7-ID285)
