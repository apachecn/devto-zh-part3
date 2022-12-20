# 用 JS pt 1: primitives 编写 JSON 解析器

> 原文：<https://dev.to/tttaaannnggg/writing-a-json-parser-in-js-pt-1-primitives-h72>

我将教一些学生如何构建一个 JSON 解析器，所以我会先给你们讲解一下。

我们可以通过理解`JSON.stringify`开始我们的旅程，因为我们基本上要逆转它所做的一切。让我们通过它输入一些原始值。

```
JSON.stringify(true) // returns 'true'
JSON.stringify(null) // returns 'null'
JSON.stringify('hello') // returns '"hello"'
JSON.stringify("hello") // returns '"hello"'
JSON.stringify(123) // returns '123' 
```

Enter fullscreen mode Exit fullscreen mode

给了我们一个字符串。这很好，因为我们可以简单地一行一行地遍历我们的字符串，并找出我们需要采取什么样的行动来创建反映这个字符串内容的数据。

## 我们是如何对此进行推理的？

幸运的是，字符串的第一个字符实际上足以让我们知道我们得到了什么类型的数据。让我们从思考原语开始。它们如下:

*   线
*   布尔型
*   空
*   不明确的
*   数字

让我们来看看这个列表，并讨论如何识别每一个。幸运的是，每种类型的数据都以不同的字符开头，所以我们可以简单地检查 JSON 字符串的第一个字符来判断它是什么。

### 字符串

在 JSON 格式中，字符串的第一个字符总是双引号。如果我们在 JSON 字符串的开头看到一个双引号，我们可以简单地去掉字符串的第一个和最后一个字符(开始和结束引号)并返回结果字符串。

我们将从这里开始我们的代码，通过构建一个 JSON 原语解析器。

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们给它我们的`'"hello"'`，我们将得到`'hello'`。

所以让我们继续下一种类型

### 布尔

这里有两种可能性，一个`true`或者一个`false`。我们将检查一个`t`或`f`，并相应地返回值

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

### Undefined / Null

我们将把这两个压缩成一个，因为它与布尔基本上是相同的概念。`'u'`或`'n'`而不是`'t'`或`'f'`T4】

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
  if(jstring[0] === 'u') return undefined;
  if(jstring[0] === 'n') return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 数字

最后，如果我们的第一个字符是一个数字，我们可以简单地使用`Number`将其从字符串转换为数字。这有点不同，因为 0 到 9 之间的任何字符都会让我们知道我们在处理一个数字。

就个人而言，我认为最好的方法是避免创建额外的数据结构，并避免创建 10 个以上的`if`检查。

```
function JSONParser(jstring){
  if(jstring[0] === '"') return jstring.slice(1, jstring.length-1);
  if(jstring[0] === 't') return true;
  if(jstring[0] === 'f') return false;
  if(jstring[0] === 'u') return undefined;
  if(jstring[0] === 'n') return null;
  if(jstring.charCodeAt() >= 48 && jstring.charCodeAt() <= 57) return Number(jstring); 
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么是`charCodeAt`？`charCodeAt`检查特定字符的 ASCII 码，0-9 是连续的。因此，如果我们的角色介于 48 和 57 之间(包括 48 和 57)，我们知道它是一个数字。在这种情况下，我们可以简单地在我们的字符串上调用`Number`，它会根据需要将字符串转换成 int 或 float。

接下来，我们将处理复合数据，从[数组](https://dev.to/tttaaannnggg/json-parser-pt2-arrays-2gbn)开始