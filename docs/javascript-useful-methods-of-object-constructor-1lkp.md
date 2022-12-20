# Javascript:对象构造函数介绍。

> 原文：<https://dev.to/yongliang24/javascript-useful-methods-of-object-constructor-1lkp>

# 概观

在 Javascript 中，我们可以使用内置的对象构造函数创建一个对象包装器。有很多方法可以使用这个构造函数，比如 Object.assign()。这是一个关于对象构造函数的简短介绍。让我们详细演练一些常见的方法。

### 对象.分配()

Object.assign()将其他对象值复制到一个新的目标对象。

```
let obj1 = {a: 1, b: 2, c: 3};
let obj2 = {d: 4, e: 4, c: 6};
let newObj = Object.assign({}, obj1, obj2);
console.log(newObj);
{ a: 1, b: 2, c: 6, d: 4, e: 5 } 
```

请注意，obj1 中的 c: 3 没有复制到 newObj，因为 obj2 具有相同的键 c: 6，并且被 obj1 上的值覆盖。同样，如果没有空的花括号，第一个参数将被修改并与 obj2 合并。
(被称为破坏性的)

```
let obj1 = {a: 1, b: 2, c: 3};
let obj2 = {d: 4, e: 4, c: 6};
let newObj = Object.assign(obj1, obj2);
console.log(newObj);
{ a: 1, b: 2, c: 6, d: 4, e: 5 }
console.log(Obj1);
{ a: 1, b: 2, c: 6, d: 4, e: 5 }
console.log(Obj2);
{d: 4, e: 4, c: 6} 
```

### Object.values()

Object.values()将返回一个数组，包含正在解析的对象的所有值。

```
let testObj = {name: 'Vegas', age: 25, hairColor: 'dark'};
Object.values(testObj);
console.log(valueObj);
Array ["Vegas", 25, "dark"] 
```

### Object.keys()

与 Object.values 类似，Object.keys 将返回一个包含所有 keys 属性的数组，如下:

```
let testObj = {name: 'Vegas', age: 25, hairColor: 'dark'};
Object.keys(testObj);
console.log(valueObj);
Array ["name", "age", "hairColor"] 
```

### Object.entries()

Object.entries 返回键和值，如下:

```
let testObj = {name: 'Vegas', age: 25, hairColor: 'dark'};
valueObj = Object.entries(testObj);
console.log(valueObj);
Array [Array ["name", "Vegas"], Array ["age", 25], Array ["hairColor", "dark"]] 
```

### 结论

当我们处理数据集(如 json 格式的数据)时，对象构造函数及其方法非常有用。要了解更多，所有其他对象构造器方法都可以在这里找到:

[Mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)