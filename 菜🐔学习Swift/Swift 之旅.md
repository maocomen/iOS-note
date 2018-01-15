[TOC]

# Swift 之旅

我们在学习一门新的编程语言的时候，往往第一个工程我们会在屏幕上打印 “hello world” ，在 Swift 中，可以用一行代码来完成它。

```swift
print("Hello, world!")
```

如果您之前用 C 语言或者 Objective-C 语言编写过代码的话，对 Swift 的这种语法会感到更亲切。在 Swift 中，上面这一行代码就是一个完整的程序，您不需要为输入／输出字符串等处理导入单独的库。在 Swift 中，写在全局环境中的代码作为程序的主入口（main.swift文件中的代码），所以不需要 main() 函数。我们同样不需要在每一句代码的结尾处写分号。

这篇文章会通过展示多种编程任务的实现，来告诉你，如何用 Swift 编写代码。

## 简单值

用 ==let== 来声明一个常量，用 ==var== 来声明一个变量。常量的值在编译的时候并不会被用到，但是必须为他指派一个值（常量不可更改而且只能赋值一次）。这就意味着我们对那些很多地方都会用到的值，可以用常量来表示。

```swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```

一个常量或者变量的类型必须与要赋给它的值类型相同。然后，并不是每次我们都需要指明变量的类型。在赋值的时候，我们可以不指明变量的类型，而是交给编译器去做推断。在上面的例子中，==myVariable== 是整型，因为最初赋值的时候我们赋的是整型。

如果初始值并没有提供足够的信息（或者根本就没有初始值），那么我们就需要在变量之后通过冒号来指定它的类型。

```swift
let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

值不会隐式地转换为其他类型。如果需要将值转化为其他类型，需要明确地创建所需类型的实例。

```swift
let label = "The width is "
let width = 94
let widthLabel = label + String(width)
```

如果我们想在字符串中包含其他值，可以用另一种更简单一点的方法，将要包含的值卸载圆括号中，并在圆括号之前写个反斜杠。

```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

对于多行字符串，我们用三个双引号来处理，双引号开始处的缩进会被移除，只匹配结尾处双引号的缩进。

```swift
let quotation = """
Even though there's whitespace to the left,
the actual lines aren't indented.
Except for this line.
Double quotes (") can appear without being escaped.
 
I still have \(apples + oranges) pieces of fruit.
"""
```

我们可以通过中括号[]来创建数组和字典，并且我们可以通过在 [] 中写入下标和键值来访问对应的元素。在最后一个元素后面我们可以加入逗号。

```swift
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"
 
var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
```

如果需要创建一个空的数组或者字典，我们需要用到初始化语法。

```swift
let emptyArray = [String]()
let emptyDictionary = [String: Float]()

let emptyArray:[String] = []
let emptyDictionary:[String: Float] = []
```

如果类型已经被推断出来了，那么我们可以用[]来表示一个空数组，用[:]来表示一个空字典。

```swift
var array = ["string"]
array = []
        
var dictionary = ["key":"value"]
dictionary = [:]
```

## 控制流

我们会用 if 和 switch 做条件判断，用 for-in ，while 和 repeat-while 做循环。条件变量或者循环变量周围的圆括号是可选的（可写可不写），但是主函数的大括号是必须写的。

```swift
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)
```

在 if 条件判断语句中，判断条件必须是一个布尔值。这就意味着 `if score{...}` 这种代码的写法是错误的，不会隐式比较它是否为0。

我们可以使用 if 和 let 组合来处理那些可能会丢失的值。这些值是可选的，可选值包括确切的值和 nil，如果是 nil ，说明这个可选值的值丢失了。我们可以在变量的类型后面加个问号（?）来表示这个变量是个可选值。

```swift
var optionalString: String? = "Hello"
print(optionalString == nil)
 
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

在上面的代码中，如果可选值是 nil ，那么条件会变成 false ，判断不成立，则会跳过上面大括号中的代码，不予执行。否则，可选值被解包并分配给常数 let ，这就使得我们可以在代码块中使用这个解码值。

另外一种处理可选值的方法就是，我们可以使用 ?? 操作符来为可选值提供一个默认值。如果缺少可选值，那么就使用默认值进行替代。

```swift
let nickName: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickName ?? fullName)"
```

Switches 支持任何类型的数据和各种比较操作，不仅仅是整型的相等比较。

```swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}
```

注意 let 在这里的用法，这里的作用是，将匹配的值赋值给一个常量。

在执行完相匹配的 switch case 中的代码之后，程序会退出 switch 语句。程序不会执行下一个 case 中的代码，所以我们不需要在 case 中的代码执行完成之后，再写一个 break 来跳出 switch 语句。

我们可以通过为字典中的键值对提供一对名称来用 for-in 对字典进行遍历。字典是个无序的集合，所以我们遍历出来的键值对也是无序的。

```swift
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
print(largest)
```

用 while 可以循环执行一个代码块，直到判断条件不成立。判断条件我们可以写在结尾处，这样子的话可以保证这个代码块至少执行一次。

```swift
var n = 2
while n < 100 {
    n *= 2
}
print(n)
 
var m = 2
repeat {
    m *= 2
} while m < 100
print(m)
```

我们可以使用 ..< 来制定一个索引范围来保证索引的循环。

```swift
var total = 0
for i in 0..<4 {
    total += i
}
print(total)
```

我们可以使用 ..< 来制定一个不包含其上限值的索引范围，用 … 来制定一个包含其所有值的索引范围。

## 函数和闭包

我们可以用 func 去声明一个函数，然后通过书写其函数名，并在函数名后面的圆括号中传入参数列表来调用函数。通过 -> 将参数名称类型与函数的返回类型进行分离。

```swift
func greet(person: String, day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet(person: "Bob", day: "Tuesday")
```

默认情况下，函数会使用它们的参数名作为参数的标签。我们可以在参数名之前插入我们的自定义参数标签，或者我们可以在参数名之前使用 _ 来去掉参数标签。

```swift
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")
```

我们可以使用元组来创建复合值。比如，一个函数需要返回多个值的时候，我们就可以用元组。元组的元素可以通过元素名称或者元素的下标数字来访问。

```swift
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0
    
    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }
    
    return (min, max, sum)
}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.sum)
print(statistics.2)
```

函数是可以被嵌套的。嵌套函数可以访问在外部函数中声明的变量。我们可以使用嵌套函数在那些很长或很复杂的函数中组织代码。

```swift
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
```

函数也是一种类型，这就意味着我们可以把函数作为一个函数的返回值。

```swift
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
```

函数也可以作为其他函数的入参。

```swift
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```

闭包：可以被延后调用的代码块。函数实际上是一个特殊的闭包。闭包中可以使用创建闭包之前所声明的变量或函数，即使这个闭包是在其他地方被调用的。可以参考上面的嵌套函数来理解。我们可以用 ({}) 来写一个没有名字的闭包，用 in 将参数和返回值从代码主体中分离出来。

```swift
//简单创建一个闭包
let closures = ({(name:String) -> Void in
    print(name)
 })
closures("张三")

//声明一个闭包作为参数的函数
func closuresMethod(handle:(String,String) -> Void) -> Void {
    handle("Hello", "world")
}
closuresMethod { (name1, name2) in
    print(name1 + name2)
}

//swift API
numbers.map({ (number: Int) -> Int in
    let result = 3 * number
    return result
})
```

我们有几种方式来简化闭包的写法。如果一个闭包的类型(参数类型，返回值类型)我们都知道的话，我们可以省略不写一些东西，比如参数类型，返回值类型，或者两个都不写。单个语句的闭包会隐式地返回其唯一语句的值。

我们也可以通过数字而不是名称来引用参数，这种操作在那些比较短的闭包中尤其能体现到它的作用。如果一个函数的最后一个参数是闭包的话，闭包可以在圆括号之后直接展示。如果一个函数只有一个闭包参数的话，可以把圆括号省略。

```swift
//我们先声明一个参数为闭包的方法
func closureMethod(_ handle:(String,String) -> String) -> String {
   return handle("Hello", "word")
}
//我们先写一个比较普通，最正常的闭包
let string = closureMethod ({ (title:String, content:String) -> String in
   return title + " " + content
})

//如果一个函数的最后一个参数是闭包的话，闭包可以在圆括号之后直接展示。
let string = closureMethod (){ (title:String, content:String) -> String in
   return title + " " + content
}

//如果一个函数只有一个闭包参数的话，可以把圆括号省略掉
//我们可以省去参数类型变成这样
let string = closureMethod { (title, content) -> String in
   return title + " " + content
}

//我们甚至可以把返回值类型也省略掉
let string = closureMethod { (title, content) in
   return title + " " + content
}

//还可以这样
let string = closureMethod ({ title, content in return title + " " + content })

//上面说，单个语句的闭包会隐式返回其唯一语句的值，那么。。。
let string = closureMethod ({ title, content in title + " " + content })

//我们可以用$0代表第一个参数，用$1代表第二个参数
let string = closureMethod { $0 + " " + $1 }

//swift API
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
```

## 对象和类

我们可以通过在 class 后面加上类名来创建一个类。类中的属性的声明与常量和变量的声明方法相同，只不过属性的声明是写在类的上下文中。同理，方法和函数的声明也是这样写的。

```swift
class Shape {
    var numberOfSides = 0
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

通过在类后面加圆括号来创建类的实例，通过点语法来访问实例的属性和方法。

```swift
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```

这个版本的 shape 类缺少一些重要的东西：一个可以在创建实例时对其进行设置的初始化器。我们可以用 init 自己创建一个。

```swift
class NamedShape {
    var numberOfSides: Int = 0
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

在这里，我们需要注意，初始化器中，是用 self 来对 name 参数和 name 属性进行区分的。在创建实例的时候，构造器中的参数像函数调用一样传递进来。每个属性都必须被分配一个值，要不在属性声明的时候（像 numberOfSides ），要不在初始化器中（像 name ）。

我们可以用 deinit 来创建一个析构器，方便我们在对象销毁之前做一些清除操作。

创建子类的时候，我们需要在子类的名称后面加上父类的名称，并用分号进行分割。对于子类化标准根类的时候，我们可以不遵循这个规则，我们可以根据我们的实际需求来包含或者省略调父类的名称。

如果子类想要重写父类的方法的时候，需要用 ==override== 进行标记，那些没用 ==override== 标记实际上却重写了父类的方法，会被编译器检测到错误。编译器同样会检测那些写了 ==override== 标记，却没有真正复写父类的方法，并抛出错误。

```swift
class Square: NamedShape {
    var sideLength: Double
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }
    
    func area() -> Double {
        return sideLength * sideLength
    }
    
    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}
let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```

属性除了可以存储值这个简单功能以外，还可以有一个 ==getter== 和一个 ==setter==。

```swift
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }
    
    var perimeter: Double {
        get {
            return 3.0 * sideLength
        }
        set {
            sideLength = newValue / 3.0
        }
    }
    
    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)."
    }
}
var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)
triangle.perimeter = 9.9
print(triangle.sideLength)
```

在 ==perimeter== 的 set 方法中，新值具有一个默认的名称 newValue ，我们可以在 set 后的圆括号中提供一个显式的名称。

注意，EquilateralTriangle 类的初始化器中有三个不同的步骤：

1. 为子类中定义的属性赋值
2. 调用父类构造器
3. 改变父类中定义的属性值。我们在这一步可以做一些额外的操作，比如 setter ，getter 或者执行一些其他的方法。

如果不需要改变属性的赋值语句，但是却想要在赋值之前或之后做一些处理的话，可以使用 ==willSet== 和 ==didSet== 。willSet 和 didSet 中的代码会在任何属性值变化的时候执行（除了构造器中的赋值操作）。就像下面的这个例子一样，例子中的类保证了三角形的边长和正方形的边长始终保持一致。

```swift
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}
var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
print(triangleAndSquare.triangle.sideLength)
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
```

如果在代码的编写过程中需要用到可选值，我们可以在可选值调用方法，属性或者取下标等操作之前加上一个 ？号。这个操作代表着，如果可选值是 nil，那么 ？后面所有的操作都会被忽略，整个表达式的值会变成 nil 。否则，会对可选值进行解包，？后所有的操作都会依托于解包后的值执行。因为整个表达式的值有两种情况，所以表达式的值其实也是个可选值。

```swift
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
```

## 枚举和结构体

用 ==enum== 来创建一个枚举。像类和其他所有的命名类型一样，枚举可以有与它相关联的方法。

```swift
enum Rank: Int {
    case ace = 1
    case two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king
    func simpleDescription() -> String {
        switch self {
        case .ace:
            return "ace"
        case .jack:
            return "jack"
        case .queen:
            return "queen"
        case .king:
            return "king"
        default:
            return String(self.rawValue)
        }
    }
}
let ace = Rank.ace
let aceRawValue = ace.rawValue
```

默认情况下，Swift 会为枚举分配从 0 开始的原始值，每分配一次原始值加 1，但是我们可以通过显式地为枚举指定一个特殊的值来改变这个行为。在上面的例子中，Ace 的原始值被显式地指定成了 1 ，而剩下所有的枚举的原始值是按顺序分配的（原来的从 0 开始现在变成了从 1 开始）。我们也可以使用字符串或者浮点数来作为枚举的原始类型。

使用 ==init?(rawValue:)== 构造器来根据原始值创建一个枚举实例。如果有匹配的原始值，会返回相应的枚举实例，如果没有匹配的原始值，会返回 nil。

```swift
if let convertedRank = Rank(rawValue: 3) {
    let threeDescription = convertedRank.simpleDescription()
}
```

枚举的实例值是一个实际值，并不是原始值的另一种表达方式。实际上，在没有有意义的原始值的情况下，我们并不需要为它提供一个原始值。

```swift
enum Suit {
    case spades, hearts, diamonds, clubs
    func simpleDescription() -> String {
        switch self {
        case .spades:
            return "spades"
        case .hearts:
            return "hearts"
        case .diamonds:
            return "diamonds"
        case .clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.hearts
let heartsDescription = hearts.simpleDescription()
```

请注意上面 hearts 枚举的两种引用方法：在给 ==hearts== 常量赋值的时候，因为 hearts 常量并没有指定的显式类型，所以引用是它的全称 Suit.hearts 。在 switch 中，使用缩写形式来引用 hearts 枚举，因为 self 的值已经可以确定是 Suit 了。我们可以在任何已经能确定类型的情况下使用缩写形式。

如果一个枚举有原始值，那么这些值是作为声明的一部分而被确定下来的。这就意味着每个特定类型的枚举实例的原始值总是相同的。枚举还可以拥有自己的关联值，关联值必须在实例化的时候就被确定下来，而且对于不同的枚举实例来说，关联值可以是不同的。我们可以将关联值看作枚举实例的存储属性。举个例子，我们要从服务器获取日出和日落时间。那么有两种情况，一种是服务器响应成功，服务器返回日出时间和日落时间，另一种是服务器响应失败，并返回错误描述。

```swift
enum ServerResponse {
    case result(String, String)
    case failure(String)
}
 
let success = ServerResponse.result("6:00 am", "8:09 pm")
let failure = ServerResponse.failure("Out of cheese.")
 
switch success {
case let .result(sunrise, sunset):
    print("Sunrise is at \(sunrise) and sunset is at \(sunset).")
case let .failure(message):
    print("Failure...  \(message)")
}
```

我们需要注意一下，在 switch 中相匹配的情况下，sunrise 和 sunset 是如何从 ServerResponse 枚举实例中被提取出来的。其实上面 switch 的写法本来应该是下面这样子的，我们需要定义一个常量来接受枚举的关联值，为了简便我们可以把 let 提到枚举前面。

```swift
switch servcerResponse {
case .result(let sunrise, let sunset):
    print("Sunrise is at \(sunrise) and sunset is at \(sunset).")
case .failure(let message):
    print("Failure...  \(message)")
}
```

我们可以用 ==struct== 来创建一个结构体。结构体支持很多与类中很相似的行为，包括方法和构造器。类和结构体的最大的一个不同点就是，当它们在代码中传递时，结构体总是会被复制一份，但是类是通过引用进行传递。

```swift
struct Card {
    var rank: Rank
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: .three, suit: .spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
```

## 协议和拓展

用 ==protocol== 来声明一个协议。

```swift
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}
```

类，枚举和结构体都可以采用协议。

```swift
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    var anotherProperty: Int = 69105
    func adjust() {
        simpleDescription += "  Now 100% adjusted."
    }
}
var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription
 
struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}
var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription


enum TestEnum : ExampleProtocol {
    case testOne
    var simpleDescription: String {
        return "123木头人"
    }
    func adjust() {
        print("执行方法")
    }
}
var c = TestEnum.testOne
c.adjust()
let bDescription = c.simpleDescription
```

注意在 ==SimpleStructure== 的声明中使用 ==mutating== 关键字来做标记，表明这是一个可以修改结构体的方法。==SimpleClass== 中不需要给任何方法做 ==mutating== 标记，因为类中的方法总是可以修改这个类。

枚举，结构体和类都可以创建实例，但是只有类的实例才叫对象。属性用来描述类，结构体或者枚举里面的值。属性分为存储属性和计算属性，存储属性存储类中常量和变量的值，而计算属性计算常量或者变量的值。通过实现 setter 和 getter 我们可以创建一个计算属性。存储属性只能放到类和结构体当中。计算属性可以放到类，结构体和枚举当中。

用 ==extension== 向现有的类型添加功能，比如方法和计算属性。我们可以用扩展来为指定的类型添加一个统一的协议，无论这个类型是在别的地方定义的还是从库或者框架引入的。

```swift
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    mutating func adjust() {
        self += 42
    }
}
print(7.simpleDescription)
```

我们可以像使用其他命名类型一样来使用协议，举个例子，我们可以创建一个具有多种类型对象的集合，所有的对象都遵循同一个协议。当我们使用协议类型的值时，任何协议之外定义的方法都是不可用的。

```swift
let protocolValue: ExampleProtocol = a
print(protocolValue.simpleDescription)
// print(protocolValue.anotherProperty)  // Uncomment to see the error
```

即使变量 protocolValue 拥有运行时类型 SimpleClass ，但是编译器认定了它的类型是 ExampleProtocol 。这就意味着我们不能调用那些虽然在类中定义了，但是没有在协议中定义的方法和属性。

## 异常处理

我们可以使用任意一个遵循了 ==Error== 协议的类型来代表异常。

```swift
enum PrinterError: Error {
    case outOfPaper
    case noToner
    case onFire
}
```

用 ==throw== 来抛出一个异常，并且我们需要用 ==throws== 来标记这个方法会抛出异常。如果一个函数中抛出了异常，那么这个函数会立即返回，我们可以在调用这个函数的代码那里来处理这个异常。

```swift
func send(job: Int, toPrinter printerName: String) throws -> String {
    if printerName == "Never Has Toner" {
        throw PrinterError.noToner
    }
    return "Job sent"
}
```

我们有这么几种方法去处理异常。一种是使用 ==do-catch== 。在 ==do== 代码块中，我们可以通过在方法前写入一个 ==try== 来标记，表示这个方法会抛出异常。在 ==catch== 代码块中，如果我们不指定一个异常的名字的话，这个异常会以默认的名字 ==error== 被提供出来。

```swift
do {
    let printerResponse = try send(job: 1040, toPrinter: "Bi Sheng")
    print(printerResponse)
} catch {
    print(error)
}
```

我们可以用多个 catch 代码块来处理一些特殊的异常。我们可以在 catch 后写入特定的异常模式，就像我们在 switch 的 case 后做的处理一样。

```swift
do {
    let printerResponse = try send(job: 1440, toPrinter: "Gutenberg")
    print(printerResponse)
} catch PrinterError.onFire {
    print("I'll just put this over here, with the rest of the fire.")
} catch let printerError as PrinterError {
    print("Printer error: \(printerError).")
} catch {
    print(error)
}
```

另一种处理方法就是使用 ==try？== 来将函数的结果转化为可选值。如果这个函数抛出一个异常，异常将会被丢弃并返回一个 nil 值，否则，将会返回一个包括函数返回值的可选值。

```swift
let printerSuccess = try? send(job: 1884, toPrinter: "Mergenthaler")
let printerFailure = try? send(job: 1885, toPrinter: "Never Has Toner")
```

使用 ==defer== 我们可以插入一段执行在该函数所有代码执行完之后，返回之前的代码。无论这个函数会不会抛出异常，这段代码都会执行。`我们可以用 defer 来在相应代码的旁边做一些设置和清理处理，即使它们需要在不同的时候被运行`。

```swift
var fridgeIsOpen = false
let fridgeContent = ["milk", "eggs", "leftovers"]
 
func fridgeContains(_ food: String) -> Bool {
    fridgeIsOpen = true
    defer {
        fridgeIsOpen = false
    }
    
    let result = fridgeContent.contains(food)
    return result
}
fridgeContains("banana")
print(fridgeIsOpen)

```

## 泛型

在尖括号内写入一个名称，来生成一个通用的功能或类型。

```swift
func makeArray<Item>(repeating item: Item, numberOfTimes: Int) -> [Item] {
    var result = [Item]()
    for _ in 0..<numberOfTimes {
        result.append(item)
    }
    return result
}
makeArray(repeating: "knock", numberOfTimes: 4)
```

我们可以创建带泛型的函数，方法，以及类，枚举和结构体。

```swift
// Reimplement the Swift standard library's optional type
enum OptionalValue<Wrapped> {
    case none
    case some(Wrapped)
}
var possibleInteger: OptionalValue<Int> = .none
possibleInteger = .some(100)
```

我们可以使用 ==where== 在函数体前面指定一个特殊的需求列表—例如，要求类型必须实现某个协议，要求两个类型必须相等，或要求一个类有一个特殊的超类。

```swift
func anyCommonElements<T: Sequence, U: Sequence>(_ lhs: T, _ rhs: U) -> Bool
    where T.Iterator.Element: Equatable, T.Iterator.Element == U.Iterator.Element {
        for lhsItem in lhs {
            for rhsItem in rhs {
                if lhsItem == rhsItem {
                    return true
                }
            }
        }
        return false
}
anyCommonElements([1, 2, 3], [3])
```



# 引用

[官网地址](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/GuidedTour.html)