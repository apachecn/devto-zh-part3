# JavaScript 命名参数

> 原文：<https://dev.to/daleljefferson/javascript-named-parameters-2dd6>

我发现 JavaScript 缺少命名参数令人沮丧。在这篇文章中，我将向您展示如何获得相同的效果，并且作为奖励，向您展示如何实现默认值。

## 不带命名参数

这就是你编写不带命名参数的函数的方式。

```
const formatDate = (day, month, year) => {
  return `${day}/${month}/${year}`;
};

// Somewhere else in your code.
formatDate(1, 2, 2018); // Good: Little-endian?
formatDate(2, 1, 2018); // Bad: Middle-endian?
formatDate(2018, 2, 1); // Bad: Big-endian? 
```

我一直不明白美国的中端日期格式。

## 带命名参数

在这个例子中，我使用对象析构来模拟命名参数。

```
const formatDate = ({day, month, year}) => {
  return `${day}/${month}/${year}`;
};

// Very readable, order you parameters anyway you like.
formatDate({day: 1, month: 2, year: 2018});
formatDate({month: 2, day: 1, year: 2018});
formatDate({year: 2018, month: 2, day: 1}); 
```

## 默认值

在这个例子中，我为日、月和年提供了默认值，使它们成为可选的。

```
const formatDate = ({day = 0, month = 0, year = 2018} = {}) => {
  return `${day}/${month}/${year}`;
};

// Just pass the parameters you need.
formatDate({day: 1, month: 2, year: 2018});
formatDate({month: 2, year: 2018});
formatDate({year: 2018});
formatDate({});
formatDate(); 
```