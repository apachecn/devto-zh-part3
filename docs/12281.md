# 用于 JS 开发人员的 Kotlin(第 1 部分)

> 原文：<https://dev.to/cassiozen/kotlin-for-js-devs-part-1-5bld>

JavaScript 是一种令人惊叹的语言:当它被引入到最普遍的编程语言中时，它来自于一种“玩具语言”——你可以在浏览器上、服务器上运行它，你可以构建一个移动应用程序、一个可穿戴的应用程序——见鬼，你可以使用 JavaScript 为 Nasa 的太空服编程。

[![Node in Spacesuite](img/1409f63e2bbc8bed775d935a5d3d65de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sv6FqVTO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qbro8smi626q9q5nbzgs.png)

尽管如此，学习多种语言还是有机会的——我甚至没有谈论职业机会:正如戴维·托马斯和安德鲁·亨特在他们的经典著作《实用程序员》中所说:

> 每年至少学习一门新语言。不同的语言用不同的方式解决同样的问题。通过学习几种不同的方法，你可以拓宽你的思路，避免墨守成规。

简而言之:是的，JavaScript 很神奇，但学习一门新语言是一件强大的事情:它影响你思考编程的方式，给你新的视角，影响你解决问题的方式，最终使你成为一名更好的软件开发人员。

# 好吧，但为什么是科特林？

[![Why Kotlin](img/47ea6aac558b6c5767cb82961f184435.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fBK0KGn8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ev3v5ocbr3l38kcsk985.png)

外面有数百种编程语言，那么为什么是 Kotlin 呢？好吧，科特林的受欢迎程度(和需求)正在飙升；它受到用户的喜爱，并吸引了大批 JavaScript 开发人员。这不是我说的:GitHub 的 octoverse 状态报告指出 Kotlin 是其平台上发展最快的语言，T2 的“Kotlin 状态”报告显示 JavaScript 开发者是新 Kotlin 开发者的第二大“来源”。最后，对语言的热爱被记录在[“堆栈溢出”报告](https://medium.com/r/?url=https%3A%2F%2Finsights.stackoverflow.com%2Fsurvey%2F2018%2F)中。

撇开奇特的事实不谈，Kotlin 有很多特性，使它成为你下一门语言的绝佳候选语言:

*   它有一个现代的语法:优雅、简洁和直截了当——你可以专注于完成事情。
*   它理解面向对象和函数式编程不是竞争的，而是正交的范例，并采用了两者(在这种情况下，它实际上与 JavaScript 有相似之处)。
*   它是 Android 开发的官方语言，有许多可用的库、扩展和优化。
*   它是多平台的——kot Lin 的主要目标是 JVM (Java 虚拟机——用于客户端和服务器应用程序),但是你可以把它编译成 JavaScript 在网络上使用，或者编译成本地二进制(例如 windows、linux、iOS 和 Mac 库)

最后但同样重要的是，使用起来很有趣。

# 快速语言参考(从 JS 到 Kotlin)

在接下来的几节中，您将对 Kotlin 语言的语法和语义有一个总体的、鸟瞰式的了解，并与 JavaScript 进行比较。当然，随着新的语法和语义的出现，新的模式和构建方式也随之出现，但是目前为止我们不要关注这些(或其他 Kotlin 细节)——学习一门新语言的最佳方式是快速开始使用它，这样您就可以很快为更多做好准备(并及时阅读本系列的下一篇文章=])。

* * *

## 变量和常数

Kotlin 变量是用关键字`var`声明的。为了防止重新分配，您可以使用`val`——两者的工作方式都很像 JavaScript 的`let`和`const`。

Kotlin 是一种类型化语言，这意味着在某些情况下，您必须在代码中添加类型注释。但是，大多数情况下，编译器可以自动推断类型:

#### JavaScript

```
const name = "Jon Snow";
let isAlive = true;
let role; // Declared but not initialized 
```

#### [釜底抽薪](#kotlin)

```
// Types can be inferred from initialized variables
val name = "Jon Snow"
var isAlive = true

// If the variable is declared but not initialized, a type annotation is required:
var role: String 
```

* * *

## 字符串

### 单行和多行字符串

#### JavaScript

```
const house = "Stark";
const motto = `
Winter
is
comming
`; 
```

#### [釜底抽薪](#kotlin)

```
val house = "Stark"
val motto = """
Winter
is
comming
""" 
```

### 字符串插值

#### JavaScript

```
const action = `Attacking using a ${weapon}`;

const result = `Looks like you will ${user.getFate()}`; 
```

#### [釜底抽薪](#kotlin)

```
const action = "Attacking using a $weapon" 

const result = "Looks like you will ${user.getFate()}" // curly brackets are only necessary to interpolate expressions 
```

* * *

## 功能

### 命名函数

Kotlin 中的函数是使用 fun 关键字声明的。您需要为每个参数以及函数返回类型添加类型注释。

#### JavaScript

```
function double(num) {
    return num * 2;
}
double(2); // 4

// Default values
function shout(message, postfix = "!!!") {
    return `${message.toUpperCase()}${postfix}`;
}
shout("hey"); // HEY!!! 
```

#### [釜底抽薪](#kotlin)

```
fun double(num:Int) {
    return num * 2
}

// Default values
fun shout(message: String, postfix = "!!!"): String {
    return "${message.toUpperCase()}$postfix"
} 
```

### 命名参数

在 Kotlin 中，函数参数可以在调用函数时命名。当一个函数有大量参数或缺省参数时，这非常方便。

#### [釜底抽薪](#kotlin)

```
fun reformat(str: String,
             normalizeCase: Boolean = true,
             upperCaseFirstLetter: Boolean = true,
             divideByCamelHumps: Boolean = false,
             wordSeparator: Char = ' ') {
    ... 
}

reformat("SomeString", normalizeCase = false, divideByCamelHumps = true) 
```

### 函数表达式(Lambdas)

Lambda 表达式是“函数文字”，即没有声明，但作为表达式立即传递的函数。在 JavaScript 中，它们通常被称为“箭头函数”，在 kotlin 中被称为“lambdas”。

在 Kotlin 中，lambda 表达式总是用花括号括起来，完整语法形式的参数声明放在花括号内，并有可选的类型注释，主体放在一个`->`符号之后。注意:在 Kotlin 中不能为 lambdas 指定返回类型。在大多数情况下，这是不必要的，因为它可以自动推断。

#### JavaScript

```
const double = (num) => num * 2; // Single line has implicit return

const square = (num) => {
    const result = num * num;
    return result; // Multi line: No implicit return
} 
```

#### [釜底抽薪](#kotlin)

```
val double = { num:Int -> num * 2 }

val square = { num: Int -> 
    val result = num * num
    // The last expression in a lambda is always considered the return value:
    result
} 
```

lambda 表达式只有一个参数是很常见的。为了方便起见，Kotlin 对这些参数进行了特殊处理，并自动在名称 it 下声明了单个参数:

#### JavaScript

```
const carModels = cars.map((car) => car.model );
const oldEnough = users.filter((user) => user.age >= 21 ); 
```

#### [釜底抽薪](#kotlin)

```
val carModels = cars.map { it.model }
val oldEnought = users.filter { it.age >= 21 } 
```

* * *

## 流量控制

### 如果/否则

#### JavaScript

```
if (number > 0) {
    console.log("Positive number");
} else {
    console.log("Negative number");
} 
```

#### [釜底抽薪](#kotlin)

```
if (number > 0) {
    print("Positive number")
} else {
    print("Negative number")
} 
```

与 JavaScript(和许多其他编程语言)不同，`if`是 Kotlin 中的一个表达式——它返回值:

#### JavaScript

```
let result;
if (number > 0) {
    result = "Positive number";
} else {
    result = "Negative number";
} 
```

#### [釜底抽薪](#kotlin)

```
val result = if (number > 0) {
    "Positive number"
} else {
    "Negative number"
} 
```

在 Kotlin 中，如果你写一行，你可以省去花括号——因此没有三元运算符:

#### JavaScript

```
const result = number > 0 ? "Positive number" : "Negative number"; 
```

#### [釜底抽薪](#kotlin)

```
val result = if (number > 0) "Positive number" else "Negative number" 
```

### (切换时)

Kotlin 有一个`when`构造，可以被认为是 JavaScript `switch`语句的替代:

#### JavaScript

```
switch (selectedFruit) {
  case "orange":
    console.log("Oranges are 59 cents a pound.");
    break;
  case "apple":
    console.log("Apples are 32 cents a pound.");
    break;
  case "cherry":
    console.log("Cherries are one dollar a pound.");
    break;
  case "mango":
  case "papaya":
    console.log("Mangoes and papayas are 3 dollars a pound.");
    break;
  default:
    console.log(`Sorry, we are out of ${selectedFruit}.`);
} 
```

#### [釜底抽薪](#kotlin)

```
when(selectedFruit) {
    "orange" -> print("Oranges are 59 cents a pound.")
    "apple" -> print("Apples are 32 cents a pound.")
    "cherry" -> print("Cherries are one dollar a pound.")
    "mango", "papaya" -> print("Mangoes and papayas are 3 dollars a pound.")
    else -> print("Sorry, we are out of $selectedFruit.")
} 
```

现实中科特林的`when`比这个厉害多了，很多人形容为“超能力切换”。以下是一些`when`优势:

*   它可以用作一个表达式
*   它可以有任意的条件表达式
*   它可以自动转换值
*   它可以无参数使用
*   它可用于要求穷举(所有可能的条件都需要匹配)

在本文中，我不会深入探讨 Kotlin 的`when`,但是知道它在您的代码中非常有用是很重要的。当你觉得准备好了，请参考另一篇[文章](https://superkotlin.com/kotlin-when-statement/)了解更多。

* * *

## 循环往复

Kotlin 确实提供了一个`for`循环，但是它只适用于迭代器(比如列表和映射)。幸运的是，Kotlin 还提供了范围和迭代器，可以使用`..`操作符来创建:

#### JavaScript

```
for (let i = 1; i<=10; i++) {
    console.log(i);
}
// 1 2 3 4 5 6 7 8 9 10

const places = ["New York", "Paris", "Rio"];
for (const place of places) {
  console.log(`I Love ${place}`);
}
// I Love New York 
// I Love Paris
// I Love Rio 
```

#### [釜底抽薪](#kotlin)

```
for (i in 1..10) {
    print(i)
}
// 1 2 3 4 5 6 7 8 9 10

val places = listOf("New York", "Paris", "Rio")
for (place in places) {
    println("I Love $place")
}
// I Love New York 
// I Love Paris
// I Love Rio 
```

* * *

## 收藏

Kotlin 不提供集合文字(如数组的[]或对象的{)。相反，它提供了可用于创建集合的全局函数。这个决定背后的原因是，当语言提供集合文字时，它会影响用户使用集合的方式(进而影响人们使用语言本身的方式)。可变 vs 不可变，列表 vs 数组——kot Lin 给了用户选择的自由。

### 数组(在这种情况下意味着“可以动态增长的东西的列表…”)

“数组”是一个重载的术语，在不同的编程语言中可能有不同的含义——kot Lin 正是如此:它确实有数组，但它们不能与 JavaScript 数组相比(也就是说，它们不是可以动态增长或收缩的列表)。在这种情况下，更具可比性的集合是一个列表:

#### JavaScript

```
const houses = [ "Stark", "Lannister", "Tyrell", "Arryn", "Targaryen", "Baratheon" ];
houses[2]; // "Tyrell"
houses.push("Martell");
houses.length; //7 
```

#### [釜底抽薪](#kotlin)

```
val houses = mutableListOf("Stark", "Lannister", "Tyrell", "Arryn", "Targaryen", "Martell", "Baratheon")
houses[2] // "Tyrell"
houses.add("Martell")
houses.size //7 
```

### 对象(在这种情况下意味着“键值映射”)

JavaScript 中的对象有多种用途:它是一个散列表，但也是所有东西(除了原语)的基础结构(更不用说它还是 Object.assign 等实用方法的来源)。因为我们在讨论集合，所以我在举例说明 hashmap 用例(存储键值对)。

#### JavaScript

```
const colors = {
  "red":  0xff0000,
  "green":  0x00ff00,
  "blue":  0x0000ff,
  "cyan":  0x00ffff,
  "magenta":  0xff00ff,
  "yellow":  0xffff00
};
colors.hasOwnProperty("yellow"); // true
colors.yellow; // 0xffff00 
```

#### [釜底抽薪](#kotlin)

```
val colors =  mutableMapOf(
  "red" to 0xff0000,
  "green" to 0x00ff00,
  "blue" to 0x0000ff,
  "cyan" to 0x00ffff,
  "magenta" to 0xff00ff,
  "yellow" to 0xffff00
)
colors.contains("yellow") // true
colors.get("yellow") // 0xffff00 
```

### 关于不变性的一个快速注释

Kotlin 还提供了其集合的只读版本:

#### [釜底抽薪](#kotlin)

```
// mapOf is the read-only version of mutableMapof
val colors =  mapOf(
  "red" to 0xff0000,
  "green" to 0x00ff00,
  "blue" to 0x0000ff
)
val updatedColors = colors.plus("teal" to 0x008080) // doesn't change the original - it returns a new map

// listOf is the read-only version of mutableListof
val houses = listOf("Stark", "Lannister", "Tyrell", "Arryn", "Targaryen", "Martell", "Baratheon")

// Methods that return a new list instead of modifying it are still available:
var updatedHouses = houses.take(3).map {it.toUpperCase()} //["STARK", "LANNISTER", "TYRELL"]

// Adding new items requires copying the whole original one and making sure the new copy is also immutable
var updatedHouses = houses.toMutableList().apply{ add("Martell") }.toList() 
```

### 销毁分配

析构声明语法非常方便，可以为您节省几行代码:当您将一个集合赋给一个值时，Kotlin 会分解并匹配两边，将右边的值赋给左边的变量。在最简单的情况下，它可以用于并行赋值:

#### JavaScript

```
const coordinates = [5, 10, 15];
const [x, y, z] = coordinates; 
```

#### [釜底抽薪](#kotlin)

```
val coordinates = arrayOf(5, 10, 15)
val (x, y, z) = coordinates 
```

虽然上面的例子看起来很傻，但这对于处理返回多个值的函数来说特别方便:

#### JavaScript

```
function weatherReport(location) {
  // Make an Ajax request to fetch the weather...
  return [72, "Mostly Sunny"];
}
const [temp, forecast] = weatherReport("Berkeley, CA"); 
```

#### [釜底抽薪](#kotlin)

```
fun weatherReport(location) {
  // Make an Ajax request to fetch the weather...
  return Pair(72, "Mostly Sunny") // Pair is a standard class in Kotlin that represents a generic pair of two values
}
val (temp, forecast) = weatherReport("Berkeley, CA") 
```

* * *

## 类

与 JavaScript 非常相似，Kotlin 中的类是使用关键字`class`声明的:

#### JavaScript

```
class Monster {
  constructor(name, color, numEyes) {
    this.name = name;
    this.color = color;
    this.numEyes = numEyes;
  }
  speak(likes) {
      return `My name is ${this.name} and I like ${likes}`;
  }
}
var nhama = new Monster("Nhama", "red", 1);
nhama.speak("guacamole")
// "My name is Nhama and I like guacamole" 
```

#### [釜底抽薪](#kotlin)

```
class Monster(val name: String, val color: String, val numEyes: Int) {
  fun speak(likes: String):String {
      return "My name is $name and I like $likes"
  }
}
var nhama = Monster("Nhama", "red", 1)
// Kotlin doesn't have a `new` keyword - you instantiate a class by calling it directly
nhama.speak("guacamole")
// "My name is Nhama and I like guacamole" 
```

Kotlin 类也有一个构造函数，但是如果您只想定义类属性，可以省略关键字。

* * *

### 数据容器

在 JavaScript 中，通常创建普通对象来将命名值分组在一起。在 Kotlin 中，您会想要创建一个数据类——它们也充当数据容器，但是更轻便，有固定的字段名称，类型更严格。

#### JavaScript

```
const movie1 = {
    name: "Back to the Future",
    rating: 5,
    director: "Bob Zemeckis"
}
const movie2 = {
    name: "Star Wars: Episode IV - A New Hope",
    rating: 5,
    director: "George Lucas"
} 
```

#### [釜底抽薪](#kotlin)

```
data class Movie(
  val name: String, 
  val rating: Int, 
  val director: String
)
val movie1 = Movie("Back to the Future", 5, "Bob Zemeckis")
val movie2 = Movie("Star Wars: Episode IV - A New Hope", 5, "George Lucas") 
```

* * *

# 何去何从？

在整篇文章中，Kotlin 的语法总是与 JavaScript 的语法形成对比——然而，在一些领域，Kotlin 的独特方法与 JavaScript 不可同日而语——最突出的例子是 Kotlin 的可空性方法。在本文的第 2 部分中，我将介绍 Kotlin 的一些独特特性:空安全和异步编程——稍后见。