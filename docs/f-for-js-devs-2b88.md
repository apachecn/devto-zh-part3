# JS 开发人员的 F#

> 原文：<https://dev.to/rametta/f-for-js-devs-2b88>

最近，我在工作中做了一次内部演讲，讨论了 F#与 JS 的相似之处。它普遍受到欢迎，我想把这个演讲转换成一篇博文，提供给其他可能感兴趣的人。

这绝不是 F#特性的详尽列表，但这篇文章的重点是展示熟悉的 JS 代码，以及如何用 F#同等地编写它，因为我相信展示这样的例子是学习一门新语言的最佳方式，并且有更好的机会被采用。

## [T1】简介](#intro)

F#是一种运行在。NET 运行时。现在它是跨平台的。NET 核心，因此它可以在任何机器上编写和运行。默认情况下，它是不可变的，但完全可以与 C#或 VB 互操作。灵感来源于 Haskell、Scala、Erlang、C#和 Python。

F#可用于创建服务器、脚本、桌面应用和移动应用。(甚至是 webapps，通过用类似[寓言](https://fable.io/)的工具直接编译成 JS)

## 功能

函数是 F#的核心。本质上有两种类型的函数，命名的和匿名的。语法类似于 JS，但稍短一些。在 F#中，所有的函数都是自动执行的，这意味着所有的函数都可以部分应用，而不需要任何额外的工作。

**JS**T2】

```
const add = (x, y) => x + y
const mul = x => y => x * y // curried
add(4, 4) // 8
mul(4)(4) // 16 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
let add x y = x + y
let mul x y = x * y
add 4 4 // 8
mul 4 4 // 16

// anonymous
let sub = fun x y -> x - y
sub 8 4 // 4 
```

Enter fullscreen mode Exit fullscreen mode

## 作文

函数组合是将一个函数的输出作为输入传递给另一个函数的过程。在 JS 中，需要嵌套它们的函数，或者使用管道或组合函数作为助手来实现这一点。在 F#中有流水线操作符`|>`，正向合成操作符`>>`，反向合成操作符`<<`。

### 管道操作员

管道操作符只允许将函数参数放在函数的前面，而不是后面。

**JS**T2】

```
const add3 = x => x + 3
const mul5 = x => x * 5
const div2 = x => x / 2
div2(mul5(add3(97))) // 250 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
let add3 x = x + 3
let mul5 x = x * 5
let div2 x = x / 2
97 |> add3 |> mul5 |> div2 // 250 
```

Enter fullscreen mode Exit fullscreen mode

### 合成运算符

复合运算符允许将函数组合成一个函数。这与管道的区别在于，只有函数可以组合在一起，而管道可以接受任何值并将其传递给下一个函数。

**JS**T2】

```
const compose = require('..')
const add3 = x => x + 3
const mul5 = x => x * 5
const div2 = x => x / 2
const doMath = compose(div2, mul5, add3)
doMath(97) // 250 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
let add3 x = x + 3
let mul5 x = x * 5
let div2 x = x / 2
let doMath = add3 >> mul5 >> div2
// or backwards
let doMath = div2 << mul5 << add3
doMath 97 // 250 
```

Enter fullscreen mode Exit fullscreen mode

## 列表

F#列表非常类似于 JS 数组。虽然 F#有 3 种类似数组的集合。列表、数组和序列。但我只关注列表，因为它们是最富有的。

### 列表映射

列表映射在 F#中看起来和在 JS 中几乎一样，除了必须使用`List.map`函数，而不是像在 JS 中那样使用数组原型来点链。

**JS**T2】

```
const data = [1, 2, 3]
data.map(x => x * 2)
// [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
let data = [1; 2; 3]
List.map (fun x -> x * 2) data
// [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

### 列表转换

JS 因其丰富的数组原型函数而受到称赞，如 map、filter、find、reduce。这些 F#都有，还有 60 多个其他的！比如 List.sum，List.average，List.distinct，List.isEmpty，List.chunkBySize 和[许许多多更多的](https://msdn.microsoft.com/visualfsharpdocs/conceptual/collections.list-module-%5bfsharp%5d)。

**JS**T2】

```
[1, 2, 3]
    .map(x => x * 2)
    .filter(x => x > 3)
    .reduce((acc, x) => acc + x, 0) 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
[1; 2; 3]
    |> List.map (fun x -> x * 2)
    |> List.filter (fun x -> x > 3)
    |> List.sum 
```

Enter fullscreen mode Exit fullscreen mode

## 条件句

JS 有经典的 if-else 语法和三元运算符。F#没有三元运算符，但它有 if-else。在 F#中并不真正需要三元组，因为无论如何所有东西都是隐式返回的。关于 F#的伟大之处在于，由于模式匹配(下面解释)，你将很少需要 if-else 语法。不管怎样，这里有一个例子。

**JS**T2】

```
const bigify = x => x > 4 ? 'big' : 'small'
bigify(2) // 'small'
bigify(5) // 'big' 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
let bigify x = if x > 4 then "big" else "small"
bigify 2 // "small"
bigify 5 // "big" 
```

Enter fullscreen mode Exit fullscreen mode

## 对象/记录

JS 对象的等价物是 F#记录。值得注意的区别是，记录总是需要关联到一个类型，它们默认是引用类型，并且它们是不可变的。因此您不能更新现有记录，您需要创建一个新记录并复制值。

**JS**T2】

```
const data = {
  name: 'jason',
  cool: true,
  age: 3.14
}

// immutably update an object by creating a new object
const data2 = {
  ...data,
  age: 2.16
} 
```

Enter fullscreen mode Exit fullscreen mode

**F#** * *需要一个类型*

```
let data =
  { name = "jason"
    cool = true
    age = 3.14 }

// immutably update a record by creating a new record
let data2 =
  { data with age = 2.16 } 
```

Enter fullscreen mode Exit fullscreen mode

## 记录类型

如果不首先指定一个类型，上面的例子在 F#中是不可能的。

记录类型定义了记录的结构。由于 F#的强类型推断，您不需要将类型分配给保存数据的变量。编译器将根据定义的属性推断数据类型。所以在下面的例子中，编译器知道`data`是一个 Person 类型，因为它定义了所有完全相同的字段。

**F #**T2】

```
type Person =
  { name: string
    cool: bool
    age: float }

let data =
  { name = "jason"
    cool = true
    age = 3.14 } 
```

Enter fullscreen mode Exit fullscreen mode

## 枚举类型

JS 中对于枚举没有直接的比较，除非你使用一个带有 int 的对象，但是并不完全一样。

**F #**T2】

```
// enum
type CoolLevel = 
  | Good
  | Medium
  | Bad

type Person =
  { name: string
    age: float
    cool: CoolLevel } // requires a value from the enum

let data =
  { name = "lyagushka"
    age = 3.14
    cool = Good } // assign Good because it is in the enum 
```

Enter fullscreen mode Exit fullscreen mode

## 区别对待工会类型

为了在 JS 中获得 Union 类型的等价物，您必须使用一些第三方模块来获得一致的类型声明，例如 [DaggyJS](https://github.com/fantasyland/daggy) 。

虽然 Daggy 在 JS 中很棒，但它的模式匹配能力只能达到 JS 允许的程度。这就是 F#开始发光的地方。

如果你需要关于联合类型的解释，请看这篇文章，它会比我解释得更好。

下面是一个等价的 JS daggy 类型与本机 F# union 类型的示例，底部是模式匹配的峰值。

**JS**T2】

```
const { taggedSum } = require('daggy')

const ProductPage = taggedSum('ProductPage', {
  Loading: [],
  Error: ['msg'],
  Success: ['product']
})

const product = {
  name: 'Red Shoe',
  price: 3.14
}

const state = ProductPage.Success(product)

// pattern match
state.cata({
  Loading: () => `<div>Loading...</div>`,
  Error: msg => `<div>${msg}</div>`,
  Success: p => `<div>${p.name}</div>`
}) 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
type Product =
  { name: string
    price: float }

type ProductPage = 
  | Loading
  | Error of string
  | Success of Product

let product =
  { name = "Red Shoe"
    price = 3.14 }

let state = Success product

// pattern match
match state with
| Loading -> "<div>Loading...</div>"
| Error msg -> "<div>" + msg + "</div>"
| Success p -> "<div>" + p.name + "</div>" 
```

Enter fullscreen mode Exit fullscreen mode

## 模式匹配

模式匹配在 ML 风格的语言中很流行，因为它们非常强大。可以把它看作是类固醇的一种转换状态。在 F#中，使用语法`match [anything] with`你可以成功地计算出任何东西的类型或值。完全避免 if-else 或 switch-case 语句。

### 布尔

布尔是直截了当的，因为它们只能是两个事物中的一个，真或假。

```
let age = 6

match age > 12 with
| true -> printf "Teen"
| false -> printf "Not teen" 
```

Enter fullscreen mode Exit fullscreen mode

### 数字

数字不像布尔那样直接，因为可能有无限多的匹配可能性，所以当试图匹配数字时，如果没有匹配的模式，您将被迫使用下划线提供默认模式。

```
let age = 5

match age with
| 13 -> "teen"
| 1 -> "One Year Old"
| 4 | 5 -> "little" // 4 or 5 will match here
| x when x < 0 -> "not alive" // conditional logic
| _ -> "any other age" // default incase age is not matched with anything 
```

Enter fullscreen mode Exit fullscreen mode

### 列表

与列表匹配更酷，因为您可以使用下划线作为列表中任何值的通配符。

```
let myList = [1; 2]

match myList with
| [] -> "empty list"
| [ _ ] -> "list has 1 item"
| [ _; 5 ] -> "list has 2 items, 2nd item is 5"
| [ _; _; _ ] -> "list has 3 items"
| _ -> "list does not match any of the above patterns" 
```

Enter fullscreen mode Exit fullscreen mode

## 单子

单子是一个大话题，我甚至在 JS 中写了一整篇关于单子的文章。

在 F#中，内置了一些单子，比如 Option 类型，除了键入 some 或 None，不需要做进一步的工作就可以使用。

**JS**T2】

```
const { taggedSum } = require('daggy')

const Maybe = taggedSum('Maybe', {
  Just: ['value'],
  Nothing: []
})

const { Just, Nothing } = Maybe

const data = Just(50)

data.cata({
  Just: x => console.log(`Value: ${x}`), // 50
  Nothing: () => console.warn("Nothing here")
}) 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
let data = Some(50)

match data with
| Some x -> printf "Value: %i" x
| None -> printf "Nothing here" 
```

Enter fullscreen mode Exit fullscreen mode

## 打字

关于 F#中函数类型的一个简单说明。下面我写了 4 次完全相同的函数，每次都用不同的方式定义类型。

第一个具有隐式类型，让编译器根据调用者和传递给它的数据来推断类型。

第二个为每个参数定义类型，然后定义返回类型。

第三和第四个使用类型签名和匿名函数来定义类型。

所有这些都是有效的，每个都可以用于不同的用例。

**F #**T2】

```
// inferred types
let add x y = x + y

// explicit types
let add (x: float) (y: float): float = x + y

// explicit inline type signature
let add: float -> float -> float = fun x y -> x + y

// explicit separate type signature
type Add = float -> float -> float
let add: Add = fun x y -> x + y 
```

Enter fullscreen mode Exit fullscreen mode

## HTTP 请求

JS 的很大一部分是易于使用 Promise 类型进行异步操作，比如 HTTP 请求。

通过使用`async`关键字，Async 也被内置到 F#中。下面是一个获取页面 html 的等效 http 请求的例子。

**JS**T2】

```
const axios = require('axios')

axios
  .get('https://github.com/rametta')
  .then(({ data }) => console.log(`HTML: ${data}`))
  .catch(console.error) 
```

Enter fullscreen mode Exit fullscreen mode

**F #**T2】

```
// sync
let html = Http.RequestString("https://github.com/rametta")

// async
async { let! html = Http.AsyncRequestString("https://github.com/rametta")
        printfn "%d" html.Length }
|> Async.Start 
```

Enter fullscreen mode Exit fullscreen mode

## 其他酷 F#玩意儿

简要介绍了其他简洁的 F#特性。

### 范围运算符

使用两个点快速定义一个范围。

```
let myList = [ 1..5 ]
// [1; 2; 3; 4; 5] 
```

Enter fullscreen mode Exit fullscreen mode

### 可变关键字

当想要改变变量时，使用 mutable 关键字作为退路。

```
let mutable data = 6
data <- 8 
```

Enter fullscreen mode Exit fullscreen mode

### 产量关键字

```
let mySeq = seq {
  for i in 1..10 do
  for j in 10..15 do
  yield i * j
} 
```

Enter fullscreen mode Exit fullscreen mode

### 元组

```
let myTuple = (5, "hello")

let typedTuple: int * string = (5, "hello") 
```

Enter fullscreen mode Exit fullscreen mode

我希望这篇文章能揭示出 F#与 JS 有多么相似，我也希望它能鼓励你在未来的项目中使用它。

如果你想了解更多关于 F#的知识，请查看[fsharpforunandprofit](https://fsharpforfunandprofit.com)！

欢迎在 twitter 上关注我！ [@rametta](https://twitter.com/rametta)