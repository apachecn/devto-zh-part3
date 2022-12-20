# 优雅的 JavaScript 代码——组合两个字符串

> 原文：<https://dev.to/deadlybyte/elegant-javascript-code-combining-two-strings-3e4e>

今天我实现了一个函数( **combineLabels** )将两个字符串组合在一起，下面是对该函数的要求

*   返回使用提供的分隔符组合的两个字符串
*   如果第二个字符串是 **falsy** ，则返回第一个字符串
*   如果第一个字符串是 **falsy** ，则返回第二个字符串
*   返回一个空字符串是两个字符串都是 **falsy**

现在，我可以使用多个 if 语句来实现这一点，但是下面的方法更优雅，代码更少，并且可以处理上述所有场景。

```
const combineLabels = (label1, label2, separator = ' - ') => {
  return [ label1, label2 ].filter(Boolean).join(separator);
} 
```

让我解释一下代码是怎么回事。首先，我创建了一个由传递给函数的两个标签组成的数组。然后神奇的是，通过执行`.filter(Boolean)`，它从数组中排除了所有计算结果为 **falsy** 值('、未定义、null 等)的项。一旦项目被排除，`join`方法就使用提供的分隔符将字符串连接在一起。当数组中没有字符串或只有一个字符串时，将忽略分隔符。

下面的清单显示了调用该函数的示例，以及返回值。

```
combineLabels('Label 1', 'Label 2'); // returns 'Label 1 - Label 2'
combineLabels('Label 1', 'Label 2', ','); // returns 'Label 1,Label 2'
combineLabels('Label 1', undefined); // returns 'Label 1'
combineLabels('Label 1', null); // returns 'Label 1'
combineLabels('Label 1', ''); // returns 'Label 1'
combineLabels(undefined, 'Label 2'); // returns 'Label 2'
combineLabels(null, 'Label 2'); // returns 'Label 2'
combineLabels('', 'Label 2'); // returns 'Label 2'
combineLabels('', ''); // returns ''
combineLabels(undefined, undefined); // returns ''
combineLabels(null, null); // returns ''
combineLabels(undefined, null); // returns ''
combineLabels(undefined, ''); // returns ''
combineLabels(null, undefined); // returns ''
combineLabels(null, ''); // returns '' 
```

那么，你今天写了哪些优雅的代码呢？