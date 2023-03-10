# JavaScript ES8 Object.entries/values

> 原文：<https://dev.to/daleljefferson/javascript-es8-object-entries-values-12f>

JavaScript 正在飞速发展。在本文中，我将简要概述新的对象静态方法。ES8(又名 2017)现在有 Object.values 和 Object.entries 来伴随 Object.keys。

## Object.keys

使用 Object.keys 迭代 JavaScript 对象的键。

```
const countries = {
  FJ: "Fiji",
  CL: "Chile"
};

Object.keys(countries); // ['FJ', 'CL'] 
```

## 对象值

现在我们可以对价值观做同样的事情。

```
const countries = {
  FJ: "Fiji",
  CL: "Chile"
};

Object.values(countries); // ['Fiji', 'Chile'] 
```

## 对象.条目

但是如果你想同时做这两件事，会发生什么呢？

```
const countries = {
  FJ: "Fiji",
  CL: "Chile"
};

Object.entries(countries); // [['FJ', 'Fiji'], ['CL', 'Chile']] 
```

让我们使用模板字符串和数组析构来映射国家。

```
const countries = {
  FJ: "Fiji",
  CL: "Chile"
};

Object.entries(countries).map(([code, name]) => `${name} (${code})`);
// ['Fiji (FJ)', 'Chile (CL)'] 
```

Object.values 和 Object.entries 在所有现代浏览器和 node 8 中都可用。