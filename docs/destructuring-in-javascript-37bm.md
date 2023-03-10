# JavaScript 中的析构

> 原文：<https://dev.to/daltonsaffe/destructuring-in-javascript-37bm>

嗨！这是对学习现代 JavaScript 语法的开发人员解构对象和数组的简要介绍。

析构只是提取对象和数组中的值并将其赋给变量的一种方式。它在 2015 年由 ECMAScript 委员会针对 JavaScript 的 ES6 版本进行了标准化。析构是 ES6 的关键特性之一，它将使开发人员能够减少冗长的语法，编写出现代而清晰的 JavaScript 程序。

JavaScript 中的对象是这样初始化和构造的:

```
const obj = {
 firstName: 'Gaby', 
 lastName: 'Perez'
 } 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 之前，人们会将值逐个提取到变量中:

```
const firstName = obj.firstName
const lastName = obj.lastName
console.log(lastName) // 'Perez' 
```

Enter fullscreen mode Exit fullscreen mode

现在用析构变量赋值:

```
const { firstName, lastName } = obj
console.log(lastName) // 'Perez' 
```

Enter fullscreen mode Exit fullscreen mode

我们现在从一行代码中为两个变量分配了各自的值。析构是一种简单明了的方法，可以只声明你需要的变量，并将它们与同一行中的一个值相关联。当使用析构赋值时，记住变量名和键名应该对应，这样程序就知道它需要访问哪个值。

同样的提取模式也适用于 JavaScript 中的数组。相对于数组中的索引位置来声明变量，而不是键。

```
const [ fifty, lol ] = [50, 82]
console.log(lol) // 82 
```

Enter fullscreen mode Exit fullscreen mode

在处理访问嵌套对象结构中的值时，析构赋值模式大放异彩。

```
const student = {
  name: 'Ray Lo',
    subjects: {
      math: 'B+',
      science: 'A',
    },
  }

const { math, science } = student.subjects

console.log(science) // 'A' 
```

Enter fullscreen mode Exit fullscreen mode

在析构赋值中，等号的左边是目标，右边是源。这种区别是说明这种分配方式的下几个概念的基础。

[![source-target-img](img/546585ed1ed67e215a9ea112c37b57da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8dvDmnJ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/62pljdkhm8uwp18gfukn.png)

## 省略

在处理数组时，可以跳过目标中用逗号分隔的值，这样就可以在赋值中访问需要的值。

这种技术被称为省略，它被定义为说话时省略一个声音或音节。我最喜欢的省略例子是“y'all”。不过，回到 JavaScript。

```
const arr = ['hey', 'hello', 'sup', ‘destructuring is cool’];
const [greet, , , message] = arr
console.log(message) // 'destructuring is cool' 
```

Enter fullscreen mode Exit fullscreen mode

## 默认值

如果你的对象中的值可能作为未定义的返回，你可以在目标中分配默认值以避免程序中出现错误。

```
const { a = 5, b = 12 } = { a : 200 }
console.log(a) // 200
console.log(b) // 12 
```

Enter fullscreen mode Exit fullscreen mode

## 改派

也可以在析构目标本身中重新分配变量的名称。这有助于使变量更具语义，以便以后在程序中使用。

```
const obj = { greeting: 'HEY', wait: 'HOLD UP' }
const { greeting : sup, wait : chill } = obj
console.log(sup) // 'HEY'
console.log(chill) // 'HOLD UP' 
```

Enter fullscreen mode Exit fullscreen mode

## 嵌套访问

建立在上面研究的再分配语法之上的是嵌套访问的思想。这种模式对于访问深度嵌套的对象很有用，这些对象包含您想要从中提取值的单独对象。吸收这个想法将有助于保持你的代码干燥。

这里的关键区别在于，在目标中冒号后面的变量中，我们用另一对大括号将右边的变量括起来，以访问其中存储的值。因为变量名对应于嵌套在父对象中的对象的键，所以我们能够将析构语法分层并访问我们需要的嵌套值。

```
const nestedObj = {
  students:{
    autumn:{
      graduated: 1322
    }, 
    winter:{ 
      total: 1337
    }
  }
}

const { autumn: {graduated}, winter: {total} } = nestedObj.students

console.log(graduated, '/////', total)

> 1322 '/////' 1337 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

析构是一种强大的赋值语法，可以进行简洁的变量声明，在程序中应用这一概念将使人们能够编写优雅、现代的 JavaScript。