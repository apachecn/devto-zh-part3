# 箭头功能与正常功能

> 原文：<https://dev.to/tiagodanin/arrow-function-vs-normal-function-55gl>

# O que é Arrow 函数？

创建函数的更简单的替代方法，其某些特性与常规函数不同。

```
// Syntax
const soma = (a, b) => a + b
const mult = (a, b) => {
    return a * b
} 
```

Enter fullscreen mode Exit fullscreen mode

## 这个(Contexto)

其默认上下文为全局上下文。请看一个比较示例:

```
const a = () => this === object
const b = function() {
    return this === object
}

const object = {
    a,
    b
}

console.log(object.a()) //false
console.log(object.b()) //true 
```

Enter fullscreen mode Exit fullscreen mode

在 Chrome 中，默认上下文是‘t0’，不久:

```
const a = () => this === window
...
console.log(object.a()) //true 
```

Enter fullscreen mode Exit fullscreen mode

## 论据(Argumentos)

没有`Arguments`，在某些情况下，如 Node.Js，它可以接收全局参数。

```
const a = () => arguments //Uncaught ReferenceError: arguments is not defined
console.log(a(true)) 
```

Enter fullscreen mode Exit fullscreen mode

在常规函数中，我们会有一个输出:`{ '0': true }`

## 建设者(建设者)

Sem `Constructor` .

```
const a = () => true
console.log(new a()) //TypeError: a is not a constructor 
```

Enter fullscreen mode Exit fullscreen mode

## 功能名称

在 Arrow 中，名称是接收角色的标识符。在常规情况下，我们可以定义函数的名称，如下例所示，我将该名称设置为“`d`”。

```
const a = () => true
const b = function () { return true }
const c = function d () { return true}

console.log(a.name) //a
console.log(b.name) //b
console.log(c.name) //d 
```

Enter fullscreen mode Exit fullscreen mode

## 返回目标

```
const casaA = () => {cozinha: true}
const casaB = () => ({cozinha: true})
const casaC = function () {
    return {cozinha: true}
}

console.log(casaA()) //undefined
console.log(casaB()) //{ cozinha: true }
console.log(casaC()) //{ cozinha: true } 
```

Enter fullscreen mode Exit fullscreen mode

**注:** A `casaA`返回`undefined`，因为函数没有返回。你没看到吗？我将以正常模式挽救`casaA`和`casaB`:T6]

```
const casaA = function () {
    {cozinha: true}
}
const casaB = function () {
    return {cozinha: true}
}

console.log(casaA()) //undefined
console.log(casaB()) //{ cozinha: true } 
```

Enter fullscreen mode Exit fullscreen mode

明白了吗？所以我们在第一个例子中使用括号，`casaB`。

veja mais em MDN:[developer . Mozilla . org/pt-BR/docs/Web/JavaScript/Reference/Functions/Arrow _ Functions](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Functions/Arrow_functions)