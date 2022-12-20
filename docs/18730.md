# 变量与对象列表

> 原文：<https://dev.to/miku86/list-of-variables-vs-object-2fe9>

最近，我收到了一个关于 JavaScript 的问题:

> 我不确定传递一个对象而不是变量列表有什么帮助…

[带有更精确解释的代码](https://repl.it/@miku86/list-of-vars-vs-objects)

之前:

```
function createMessageWithListOfVars(firstName, lastName, age) {
  return `${firstName}  ${lastName} is ${age} years old.`
}

console.log(createMessageWithListOfVars("Andreas", "Max", 30)); 
```

Enter fullscreen mode Exit fullscreen mode

之后:

```
function createMessageWithObjectDestruct({ firstName, lastName, age }) {
  return `${firstName}  ${lastName} is ${age} years old.`
}

console.log(createMessageWithObjectDestruct({
  firstName: "Andreas",
  age: 30,
  lastName: "Max"
})); 
```

Enter fullscreen mode Exit fullscreen mode