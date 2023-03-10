# 使用默认值进行赋值的 JavaScript 析构

> 原文：<https://dev.to/zaiste/javascript-destructuring-assignment-with-default-values-2ip8>

析构赋值是一种新的语法，用于获取数组或对象的一部分，并以更方便的方式将其赋给变量。使用析构赋值时也可以设置默认值。

```
const user = {
  name: 'Zaiste',
}
const { name, age } = user;

console.log(age); // undefined 
```

`const`创建变量，所以在前面的例子中`age`是`undefined`。

```
const user = {
  name: 'Zaiste',
}
const { name = 'Basia', age = 21 } = user;

console.log(name); // Zaiste
console.log(age); // 21 
```

只有当变量不存在或它们的值被设置为`undefined`时，析构赋值中的默认值才起作用。任何其他值，包括`null`、`false`和`0`，都绕过析构语句中的默认值。

```
const dummy = {
  name: undefined
}

const { name = 'Basia' } = dummy;
console.log(name) // Basia 
```

而对于`null` :

```
const dummy = {
  name: null
}

const { name = 'Basia' } = dummy;
console.log(name) // null 
```

您可以在析构赋值中将默认值与重命名结合起来。

```
const user = {
  name: 'Zaiste',
}
const { name: author = 'Basia', age = 21 } = user;

console.log(author); // Zaiste 
```

这创建了`author`变量，而`name`只是一个标签，用于指定要销毁的字段——它并不作为一个单独的变量存在。