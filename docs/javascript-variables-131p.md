# JAVASCRIPT 变量

> 原文：<https://dev.to/5anthosh/javascript-variables-131p>

这篇文章最初发表在我的博客上，在这里找到原文

像所有编程语言一样，变量是 JavaScript 的核心部分，它们让你存储被引用和操作的信息。

## 什么是变量？

变量只是一个地方，或者更像是一个保存信息容器。我们需要用一个描述性的名称来标记信息，这样我们的程序才能被读者更清楚地理解。我们可以通过名称来修改、替换或删除变量中的信息。

要创建(*定义*或*声明*)变量，使用`let`关键字。

```
let firstName; 
```

当我们分配或存储信息时，我们使用`=`关键字

```
let firstName;
firstName = "Deepak"; // store the string 
```

> 在命名变量时，我们使用 [camelCase](https://en.wikipedia.org/wiki/Camel_case)

在单行中，我们可以声明一个变量
并给它赋值

```
let firstName = "Deepak"; 
```

我们也可以在一个语句中一次声明多个变量

```
let firstName = "Deepak", lastName = "Kumar"; // multiple assignments 
```

您可以在变量名中使用任何 [unicode](https://en.wikipedia.org/wiki/Unicode) 字符。你甚至可以用表情符号作为变量名🎉

```
let \u1f60b1 = "iPhone"; 
```

> 不建议使用表情符号作为标识符

我们也可以将数据从一个变量复制到另一个变量

```
let firstName = "Deepak";
let firstName2 = "Suriya";
firstName = firstName2; 
```

## 变量命名

*   变量必须用唯一的名称来标识。
*   名称可以包含字母、数字、下划线和美元符号。
*   保留 like `let`不能用作变量名。
*   变量名区分大小写。

> 国家和国家是不同的变量

*   变量名必须以字母开头。
    *   变量名也可以以$和 _ 开头。

```
let $ = 4; // 4
let _ = $ + 1; // 5 
```

## 常数

用`let`声明的变量可以在以后更改或重新分配。有时你不希望你的变量像服务器的端口号一样被改变。一旦`const`被初始化，它的值就不能再被改变。尝试这样做会导致错误。

```
const PORT = 1313; // declare a constant 
```

常量用大写字母和下划线命名。

```
const IP_ADDRESS = '127.0.0.1';
const WHITE_COLOR = '#ffffff'; 
```

常量也可以用作难以记忆的值的别名，如**颜色代码**。

## 使用 var

`var`也声明了一个类似`let`的变量。你可以在旧剧本中找到`var`。

```
var score = 10; 
```

`var`与`let`略有不同。我们稍后将讨论这些差异。