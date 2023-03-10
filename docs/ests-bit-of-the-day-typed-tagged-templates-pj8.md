# {ES/TS}今日要闻:键入标记模板

> 原文：<https://dev.to/tclain/ests-bit-of-the-day-typed-tagged-templates-pj8>

你好。今天我想谈谈我最喜欢的 javascript 特性之一。这些是带标签的模板。

## 标记了什么？

基本标记模板是 ES6+中通常使用的基本插值机制的扩展。

使用`backticks`您可以优雅地在字符串中插入值(顺便说一下，多行，:-))

```
const greet = (name) => `hello ${name}`

greet('timothee') // hello timothee 
```

在一部作品里？

通过使用带有函数标识符的反勾符号**，您基本上就拥有了一个免费的模板引擎。**

## 请举例说明！

别担心，实际上没有听起来那么可怕。

让我们假设一个函数

```
function myTemplate() {} 
```

我们稍后会看到实际的论点。

我现在可以用这个语法调用函数了。

```
myTemplate`Hello !` 
```

很奇怪不是吗？

更有趣的是，我可以用熟悉的`${}`语法传递插值。

让我们来试试:

```
const externValue = "Timothee"
myTemplate`Hello ! ${externValue}` 
```

当然，我现在可以使用函数调用周围范围的计算值。

这个语法是对 myTemplate 的一个实际函数调用

## 那么究竟有什么魔力呢？

让我们看看传递给函数的参数:

*   第一个参数是不变字符串的列表
*   `n` others 参数是使用`${}`产生的所有其他插值。

例如，如果我打这个电话:

```
myTemplate`Hello,${"Timothee"},World` 
```

第一个参数将是传递的字符串中不变的所有字符串的列表:

```
["Hello,", ",World"] 
```

当然，如果你不使用任何插值，你将在这个数组中有一个元素，整个传递的字符串。

下一个参数将是我的第一个插值，这里是一个常量字符串:`Timothee`。

因此，如果我们的模板字符串中有`n`插值，我们的第一个数组中就有`n+1`个元素，还有`n`个参数传递给函数。

## 酷，但怎么有用？

每当你需要用插值(也就是动态值)对字符串进行变换时，这是非常有用的。

例如，您可以构建一个符合 es6 原生语义的模板引擎

```
html`<h1>Hello ${name}</h1>`; 
```

其实这个已经存在:[https://lit-html.polymer-project.org/](https://lit-html.polymer-project.org/)

在 react 上下文中，这种技术用于从 CSS-in-js 解决方案的 CSS 代码中生成预样式的组件，如`emotion`或`styled-components`。

```
const PrestyledComponent = styled('div')`
/** real css here */
background-color: blue;
`

// usage
const Fragment = <PrestyledComponent /> 
```

## 我们怎样才能键入标记模板

在 typescript 中，我们可以向标记模板添加类型信息:

```
function tagged(strings: TemplateStringsArray, ...args: boolean[]){
  console.log(args)
} 
```

这个想法是将第一个**参数作为`TemplateStringsArray`类型输入，然后根据需要进行插值。**

我们举个例子。假设你想从一个`styled component`自动完成对你的主题的访问

```
export const theme = {
  'colors.hello': true
}

export type ThemeKeys =  keyof typeof theme

function themedStyled(strings: TemplateStringsArray, ...args: ThemeKeys[]){
  console.log(args)
}

themedStyled`
  background-color: ${};
` 
```

你可以免费自动完成你的主题键！

[![Autocompletion](img/c46d1ba472829175703da49b9d7b3758.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3zfwby1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vbdKAZq.jpg)

今天到此为止。下次见！