# 你最喜欢的编程语言语法特征是什么？

> 原文：<https://dev.to/geocine/what-are-your-favorite-programming-language-syntax-features-1emm>

我有自己的非穷举列表，这些纯粹是基于我用过的: *JavaScript，TypeScript，C#*

# 安全导航操作员

这也被称为*可选链接操作符*、*安全调用操作符*、*空条件操作符*。

我们没有编写多个嵌套的 if，而是使用通常的链接，但是在点(或者用于链接的其他字符)之前放置问号符号`?`。

```
String name = articles?[0]?.author?.name; 
```

Enter fullscreen mode Exit fullscreen mode

# 零合并运算符

这是三元条件 if 运算符`x ? x : y`
的简写

```
string pageTitle = suppliedTitle ?? "Default Title"; 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中，OR 操作符`||`的行为与上面的相同。如果计算结果为真，则返回第一个操作数，否则，返回第二个操作数。当左手边为真时，右手边甚至不求值；它“短路了”。

```
 let pageTitle = suppliedTitle || "Default Title" 
```

Enter fullscreen mode Exit fullscreen mode

# λ

这也被称为匿名函数、函数文字、lambda 抽象或 lambda 表达式。

```
const hello = (name) => `Hello ${name}` 
```

Enter fullscreen mode Exit fullscreen mode

你可以用这个结构做很多事情。[curry](https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983)，[闭包](https://dev.to/yaminmhd/understanding-javascript-closures-1fk7)，[高阶函数](https://eloquentjavascript.net/05_higher_order.html)等。

# 自动实现的属性

在 C#中，当属性访问器中不需要额外的逻辑时，自动实现的属性使属性声明更加简洁。

```
public string Name { get; set; } 
```

Enter fullscreen mode Exit fullscreen mode

在 C# 6 和更高版本中，你可以像初始化字段一样初始化自动实现的属性:

```
public string FirstName { get; set; } = "Jane"; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里了解更多关于自动属性

# 异步/等待

async/await 模式是许多编程语言的语法特性，它允许以类似于普通同步函数的方式构造异步、非阻塞函数。

C#

```
public async Task<int> FindPageSize(Uri uri) 
{
    byte[] data = await new WebClient().DownloadDataTaskAsync(uri);
    return data.Length;
} 
```

Enter fullscreen mode Exit fullscreen mode

JS

```
async function createNewDoc() {
  let response = await db.post({}); // post a new doc
  return await db.get(response.id); // find by id
} 
```

Enter fullscreen mode Exit fullscreen mode

# 交集/并集类型

在 TypeScript 中，交集类型组合类型，以便您可以在特定的对象或方法上拥有这两种类型的所有属性和成员。

```
target: string & Element; 
```

Enter fullscreen mode Exit fullscreen mode

同时，联合类型是一个非此即彼的场景。我们不是组合类型来接收所有成员和属性，而是使用联合类型来指定一个方法接受几种不同类型中的任何一种。

```
target: number | string; 
```

Enter fullscreen mode Exit fullscreen mode

# JSON 序列化/反序列化

JavaScript 让我们很容易序列化和反序列化 JSON。

```
const obj = { name: 'Aivan' }
JSON.stringify(obj) // serialize object to JSON string

const str = `{name : "Aivan"}`
JSON.parse(str) // deserialize string to object 
```

Enter fullscreen mode Exit fullscreen mode

# 模板文字

模板文字是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。

```
`string text ${expression} string text` 
```

Enter fullscreen mode Exit fullscreen mode

# 析构和展开算子

析构赋值语法是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性解包到不同的变量中。

```
var a, b, rest;
[a, b] = [10, 20];
console.log(a); // 10
console.log(b); // 20

[a, b, ...rest] = [10, 20, 30, 40, 50];
console.log(a); // 10
console.log(b); // 20
console.log(rest); // [30, 40, 50]

({ a, b } = { a: 10, b: 20 });
console.log(a); // 10
console.log(b); // 20

({a, b, ...rest} = {a: 10, b: 20, c: 30, d: 40});
console.log(a); // 10
console.log(b); // 20
console.log(rest); // {c: 30, d: 40} 
```

Enter fullscreen mode Exit fullscreen mode

这取自 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

# 对象属性值速记

而不是像这样写你的对象:

```
const name = 'Aivan'
const obj = { name: name } // { name: 'Aivan'} 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样写。如果键值的名称和变量名相同，这是可能的。

```
const name = 'Aivan'
const obj = { name } // { name: 'Aivan'} 
```

Enter fullscreen mode Exit fullscreen mode

# 注释者/装饰者

Decorators 为调用高阶函数提供了非常简单的语法。它还允许在代码周围应用包装器时使用更干净的语法，从而减少对您所写内容的实际意图的影响。

```
@Wove()
class DataMapper {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里了解更多关于装修工[。](https://blog.mgechev.com/2018/01/29/javascript-decorators-aop-autobind-memoization/)

# 默认参数

如果没有传递值或未定义，默认参数允许用默认值初始化命名参数。

```
function multiply(a, b = 1) {
  return a * b;
}

// you can call it like this
multiply(1) // 1
multiply(2, 2) // 4 
```

Enter fullscreen mode Exit fullscreen mode