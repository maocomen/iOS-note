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

```python
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

```python
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.
 
"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

要使用换行符开头或结尾的多行字符串字面值，我们可以在第一行或者最后一行输入一个空白行。例如：

```python
let lineBreaks = """
 
This string starts with a line break.
It also ends with a line break.
 
"""
```

多行字符串可以用缩进来匹配周围的代码。在结束引号之前的空白 ("") 会告诉 Swift，应该在其他行也忽略哪些空白。然而，如果我们在每一行的开头都写了空格 (除了结束引号之前)，那么空格会被包括在字符串内容中。

![图片1](https://docs.swift.org/swift-book/_images/multilineStringWhitespace_2x.png)

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

Swift 中的每个 Character 实例都代表一个拓展的字形集群。一个拓展的字形集群是一个或多个 Unicode 标量的序列（当组合时），来产生一个单一的人类可识别的字符。

举个例子，字符 é 可以表示为单个 Unicode 标量 é (LATIN SMALL LETTER E WITH ACUTE 或 U+00E9)。但是，该字符也可以表示为一对标量（标准字符 e (LATIN SMALL LETTER E 或 U+0065) ），后跟 COMBINING ACUTE ACCENT 标量 (U+0301) 。该 COMBINING ACUTE ACCENT 标量被图形化的应用到它之前的标量，当它由一个能处理 Unicode 的文本渲染系统渲染时，会将 e 转换成 é 。

在这两种情况下，字符 é 都表示为单个的 Swift 字符值，代表一个拓展的字符集群。在第一种情况里，集群只包含一个标量；第二种情况下，它是由两个标量组成的集群：

```swift
let eAcute: Character = "\u{E9}"                         // é
let combinedEAcute: Character = "\u{65}\u{301}"          // e followed by ́
// eAcute is é, combinedEAcute is é
```

拓展的字符集群是一种灵活的方式，可以将许多复杂的脚本字符表示为单个的 Character 值。例如，韩语字母表中的韩语音节可以表示成一个预合成的或分解的序列。这两种表述都可以作为 Swift 的单个 Character 值：

```swift
let precomposed: Character = "\u{D55C}"                  // 한
let decomposed: Character = "\u{1112}\u{1161}\u{11AB}"   // ᄒ, ᅡ, ᆫ
// precomposed is 한, decomposed is 한
```

拓展的字符集群可以使用标量来封闭标记（例如 COMBINING ENCLOSING CIRCLE，或者 U+20DD），用来将其他 Unicode 标量作为单个 Character 值的一部分封闭起来：

```swift
let enclosedEAcute: Character = "\u{E9}\u{20DD}"
// enclosedEAcute is é⃝
```

区域指标符号的 Unicode 标量可以成对组合成单个 Character 值，比如 REGIONAL INDICATOR SYMBOL LETTER U （U

+1F1FA） 和 REGIONAL INDICATOR SYMBOL LETTER S（U+1F1F8）的组合：

```swift
let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"
// regionalIndicatorForUS is 🇺🇸
```



## 计数字符

要检索字符串中 Character 值的数量，可以使用字符串的 count 属性。

```swift
let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"
print("unusualMenagerie has \(unusualMenagerie.count) characters")
// Prints "unusualMenagerie has 40 characters"
```

要注意，Swift 中对 Character 使用了拓展的字符集群，这意味着字符串的链接和修改可能不会总是影响字符串的字符数量。

例如，如果我们用四个字符的单词 cafe 来初始化一个新的字符串，然后添加一个 COMBINING ACUTE ACCENT（U+0301）到它的结尾，那么新的字符串还是只有四个字符，第四个字符变成了 é 而不是 e :

```swift
var word = "cafe"
print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in cafe is 4"
 
word += "\u{301}"    // COMBINING ACUTE ACCENT, U+0301
 
print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in café is 4"
```

要注意，拓展的字符集群可以由多个 Unicode 标量组成，这意味着不同的字符—相同字符的不同表示—可能需要不同的内存来存储。因此，Swift 中的字符不会在字符串的表示中占用相同的内存。结果就是，字符串中的字符数量不得不通过遍历字符串中的拓展的字符集群来计算。如果我们使用的是特别长的字符串值，要注意的是，属性 count 必须要遍历整个字符串中的 Unicode 标量，以确定该字符串的字符数量。

count 属性返回的字符数量并不会总是和包含相同字符的 NSString 的 length 属性相同。NSString 的长度是基于字符串中 UTF-16 表示的 16-bit 代码单元的数量，而不是字符串中拓展字符集群的数量。



## 访问和修改字符串

我们可以通过 String 的方法或者属性或者使用下标语法来访问和修改它。

### 字符串索引

每个 String 值都有一个关联的索引类型，String.Index，它对应于字符串中每个 Character 的位置。

正如上面提到的，不同的字符需要存储到不同的内存，为了确定 Character 的特定位置，我们必须从 String 的开始或者结束的地方来迭代每个 Unicode 标量。由于这个原因，Swift 字符串不能被整数值索引。

使用 startIndex 来访问 String 中第一个 Character 的位置。endIndex 是 String 中最后一个 Character 的后一个位置。因此，endIndex 对于字符串的下标来讲不是一个有效的参数。如果一个字符串是空的，那么它的 startIndex 和 endIndex 是相等的。

我们可以用 String 提供的 index(before:) 和 index(after:) 方法来得到指定下标之前或之后的那个索引。如果我们要访问一个距离制定下标笔记远的索引，我们可以使用 index(_:offsetBy:) 方法而不是调用好多次 index(before:) 或 index(after:) 。

我们可以使用下标语法来访问 String 中特定索引的字符。

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

试图获取字符串范围之外的索引或者是用字符串索引之外的索引来获取字符都会引起运行错误。

```swift
greeting[greeting.endIndex] // Error
greeting.index(after: greeting.endIndex) // Error
```

我们可以利用 indices 属性来获取字符串中所有单个字符的索引。

```swift
for index in greeting.indices {
    print("\(greeting[index]) ", terminator: "")
}
// Prints "G u t e n   T a g ! "
```



### 插入和删除

如果我们想在字符串的指定索引位置插入一个字符，我们可以使用 insert(_:at:) 方法，如果我们想在指定索引位置插入一个别的字符串，我们可以使用 insert(contentsOf:at:) 方法。

```swift
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome now equals "hello!"
 
welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there!"
```

如果我们想删除字符串中特定索引处的字符，我们可以调用 remove(at:) 方法，如果想删除特定范围内的子字符串，我们可以使用 removeSubrange(_:) 方法。

```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"
 
let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello"
```

## 子串

当我们从一个字符串中得到一个子串的时候——例如，使用下标活着类似 prefix(_:) 的方法，我们会得到一个 [Substring](https://developer.apple.com/documentation/swift/substring) 类型的实例，而不是字符串。在 Swift 中，子串包含许多和字符串相同的方法，这意味着我们可以像使用字符串一样来使用它。然而，不像字符串一样，我们在操作字符串的时候，只会在很短的时间内使用子串。当我们准备将结果存储较长的时间的时候，我们可以将子串转换为 String 实例。例如：

```swift
let greeting = "Hello, world!"
let index = greeting.index(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning is "Hello"
 
// Convert the result to a String for long-term storage.
let newString = String(beginning)
```

像字符串一样，每个子串都有一个内存区域，其中组成子串的字符被存储在其中。字符串和子串的不同就在于，作为一个性能优化的点，子串可以重用部分用来存储原始字符串的内存或者是重用部分用来存储其他子串的内存。（字符串有类似的油画，但是如果两个字符串共享内存的话，那么它们是相等的。）这种性能优化意味着，在修改字符串或者子串之前，不必支付拷贝内存的性能成本。正如上面所提到的，子串不适合长期存储——因为它们会重用原始字符串的存储空间，只要子串被使用，都必须要将它所对应的原始字符串保存到内存中。

在上面的例子中，greeting 是一个字符串，这意味着它有一块内存区域来存储组成该字符串的所有字符。因为 beginning 是 greeting 的子串，它会重用 greeting 的内存。与此形成鲜明对比的是，newString 是一个字符串——它是根据一个子串创建出来的，它有自己的内存区域。下图很明显的显示了这些关系：

![](https://docs.swift.org/swift-book/_images/stringSubstring_2x.png)

## 字符串的比较

Swift 提供了三种比较文本值的方法：两个字符串或两个字符相等，前缀相等和后缀相等。

### 两个字符串或者字符相等

用“相等运算符 ==” 或者“不想等运算符 != ” 来判断两个字符串或者两个字符的相等性，就和 [比较运算符](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html#ID70) 中描述的一样：

```swift
let quotation = "We're a lot alike, you and I."
let sameQuotation = "We're a lot alike, you and I."
if quotation == sameQuotation {
    print("These two strings are considered equal")
}
// Prints "These two strings are considered equal"
```

对于两个字符串值(或者两个字符值)，如果它们的拓展字形集群在规范上是等价的，那么它们被认为是相等的。如果拓展的字形集群具有相同的语义和外观，则它们在规范上就是等效的，即使它们在幕后是由不同的 Unicode 标量所组成的。

例如，LATIN SMALL LETTER E WITH ACUTE (U+00E9 字符 é) 和 LATIN SMALL LETTER E (U+0065 字符) 与 COMBINING ACUTE ACCENT (U+0301 字符 ́) 的组合是等效的。这两个拓展的字形集群都是表示字符 é 的有效方法，因此它们被认为是等效的:

```swift
// "Voulez-vous un café?" using LATIN SMALL LETTER E WITH ACUTE
let eAcuteQuestion = "Voulez-vous un caf\u{E9}?"

// "Voulez-vous un café?" using LATIN SMALL LETTER E and COMBINING ACUTE ACCENT
let combinedEAcuteQuestion = "Voulez-vous un caf\u{65}\u{301}?"

if eAcuteQuestion == combinedEAcuteQuestion {
    print("These two strings are considered equal")
}
// Prints "These two strings are considered equal"
```

相反，英语中使用的 LATIN CAPITAL LETTER A (U+0041, or "A")，并不等效于俄语中使用的 CYRILLIC CAPITAL LETTER A (U+0410, or "А")。即使这两个字符非常之像，但它们并没有相同的语义含义:

```swift
let latinCapitalLetterA: Character = "\u{41}"

let cyrillicCapitalLetterA: Character = "\u{0410}"

if latinCapitalLetterA != cyrillicCapitalLetterA {
    print("These two characters are not equivalent.")
}
// Prints "These two characters are not equivalent."
```

需要注意的是，Swift 中的字符串和字符的比较对语言环境是不敏感的。

### 前缀和后缀相等

要检查字符串是否有特定的字符串作为前缀或者后缀，我们需要调用字符串的 `hasPrefix(_:)` 方法和 `hasSuffix(_:)` 方法，两个方法都只采用单个 String 类型的参数并以布尔值作为返回值。

在下面的例子中，用一组字符串代表了莎士比亚的《罗密欧与朱丽叶》前两幕中的场景位置:

```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion",
    "Act 1 Scene 4: A street outside Capulet's mansion",
    "Act 1 Scene 5: The Great Hall in Capulet's mansion",
    "Act 2 Scene 1: Outside Capulet's mansion",
    "Act 2 Scene 2: Capulet's orchard",
    "Act 2 Scene 3: Outside Friar Lawrence's cell",
    "Act 2 Scene 4: A street in Verona",
    "Act 2 Scene 5: Capulet's mansion",
    "Act 2 Scene 6: Friar Lawrence's cell"
]
```

我们可以对 romeoAndJuliet 数组中的元素使用 hasPrefix(_:) 方法来计算第一幕中出现的场景数量:

```swift
var act1SceneCount = 0
for scene in romeoAndJuliet {
    if scene.hasPrefix("Act 1 ") {
        act1SceneCount += 1
    }
}
print("There are \(act1SceneCount) scenes in Act 1")
// Prints "There are 5 scenes in Act 1"
```

同样的，我们可以使用 hasSuffix(_:) 方法来计算那些发生在 Capulet’s mansion（凯普莱特豪宅）和 Friar Lawrence’s cell（劳伦斯神父的小屋子）里的场景:

```swift
var mansionCount = 0
var cellCount = 0
for scene in romeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    } else if scene.hasSuffix("Friar Lawrence's cell") {
        cellCount += 1
    }
}
print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")
// Prints "6 mansion scenes; 2 cell scenes"
```

需要注意的是，`hasPrefix(_:)` 和 `hasSuffix(_:)` 方法会对两个字符串的拓展的字符集群进行字符规范对等比较，就如同在[字符串和字符平等](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html#ID299)中说的那样。

## 字符串的 Unicode 表示

将 Unicode 字符串写入文本中或者用其他方式存储时，该字符串中的 Unicode 标量需要被编码为几个定义的 Unicode 编码形式之一。每种编码形式都会将字符串拆分成一个个的小块(也称为码元)。其中包括 UTF-8 编码形式（将字符串编码为8位代码单元），UTF-16 编码形式（将字符串编码为16位代码单元）和 UTF-32 编码形式（将字符串编码为32位代码单元）。

Swift 提供了几种不同的方式去访问字符串的 Unicode 表示。我们可以通过 for-in 语句对字符串进行遍历，来查看字符串的单个字符值（Unicode 拓展字符集群）。这个问题在 [Working with Characters](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html#ID290) 中有详细的描述。

或者，我们也可以通过其他的 Unicode 兼容表示法 (下面的三种) 来访问字符串的值:

* UTF-8 码元集合 (通过字符串的 utf8 属性进行访问)
* UTF-16 码元集合 (通过字符串的 utf16 属性进行访问)
* 21位 Unicode 标量值的集合，等同于字符串的 UTF-32 编码格式 (通过字符串的unicodeScalars 属性进行访问)

接下来，我们会举几个例子来展示一下同一个字符串的几种不同的表示，这个字符串由字符 D, o, g, !!（DOUBLE EXCLAMATION MARK, 或者说 Unicode 标量 U+203C） 和 🐶 字符（ DOG FACE，或者说 Unicode 标量 U+1F436 ）:

```swift
let dogString = "Dog‼🐶"
```



### UTF-8 表示

我们可以通过遍历字符串的 utf8 属性来访问字符串的 UTF-8 表示。这个属性的类型是 String.UTF8View，它是一个非负8位（UInt8）值的集合，示例字符串中每一个字节的 UTF-8 表示值如下所示:

![图片](https://docs.swift.org/swift-book/_images/UTF8_2x.png)

```
for codeUnit in dogString.utf8 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// Prints "68 111 103 226 128 188 240 159 144 182 "
```

在上面的例子中，前三个十进制码元值（68，111，103）分别表示字符 D，o 和 g，他们的 UTF-8 表示和他们的 ASCII 表示相同。接下来的三个十进制码元值（226，128，188）是 DOUBLE EXCLAMATION MARK 字符的三字节表示。最后四位十进制码元值是 DOG FACE 字符的四字节表示。



### UTF-16 表示

我们可以通过遍历字符串的 utf16 属性来访问字符串的 UTF-16 表示。这个属性的类型是 String.UTF16View，它是一个非负16位(UInt16)值，示例字符串中每一个字节的 UTF-16 表示值如下所示:

![](https://docs.swift.org/swift-book/_images/UTF16_2x.png)

```swift
for codeUnit in dogString.utf16 {
    print("\(codeUnit) ", terminator: "")
}
print("")
// Prints "68 111 103 8252 55357 56374 "
```

前三个十进制码元值（68，111，103）依然还是表示字符 D，o 和 g，他们的 UTF-16 码元值与 UTF-8 中的码元值相同（因为这些 Unicode 标量表示的是 ASCII 字符）。

第四个十进制码元值（8252）是 16 进制值 203C 的十进制表示，它表示了 DOUBLE EXCLAMATION MARK 字符的 Unicode 标量 U+203C。这个字符可以在 UTF-16 中表示为单个码元了。

第五个和第六个码元值（55357 和 56374）是 DOG FACE 字符的 UTF-16 代理对表示。这个代理对由一个高代理值 U+D83D （十进制是55357）和一个低代理值 U+DC36（十进制是56374）组成。

### Unicode 标量表示

我们可以通过遍历字符串的 unicodeScalars 属性来访问其 Unicode 标量表示。这个属性是 String.UnicodeScalarView 类型，它是 Unicode.Scalar 值的集合。

每个 Unicode.Scalar 都有一个值属性，这个属性返回一个标量的 21 位值，这个值是 UInt32 类型。

![](https://docs.swift.org/swift-book/_images/UnicodeScalar_2x.png)

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar.value) ", terminator: "")
}
print("")
// Prints "68 111 103 8252 128054 "
```

上图中，前三个的 Unicode.Scalar 值（68，111，103）的值属性依旧表示字符 D，o 和 g。

第四个码元值（8252）依旧是 16 进制数 203C 的十进制表示，它代表 DOUBLE EXCLAMATION MARK 字符的 Unicode 标量 U+203C。

第五个并且是最后一个的 Unicode.Scalar 值，128054，是 16 进制数 1F436 的十进制表示，它代表了 DOG FACE  字符的 Unicode 标量 U+1F436。

作为查询它们的值属性的替代方法，每个 Unicode.Scalar 值都可以被用来创建一个新的 String 值，比如使用字符串插值:

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar) ")
}
// D
// o
// g
// ‼
// 🐶
```



# 引用

[官方链接](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html#//apple_ref/doc/uid/TP40014097-CH7-ID285)
