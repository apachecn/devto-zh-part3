# {析构} = JavaScript

> 原文：<https://dev.to/micahriggan/destructuring--javascript-2bpi>

析构对于引用对象或数组非常有用。我发现它非常有用，而且有趣。

如果你正在编写类似
的代码

```
var thing = array[0];
var otherThing = array[1]; 
```

*或*

```
var name = thing.name;
var age = thing.age;
var height = thing.height;
var userId = thing.id; 
```

那你来对地方了。

在这篇文章中，我将展示一些简化上述例子的析构的常见用法，等等。

# 例 1:析构数组

以下是一些常见的数组析构模式:

*   获取数组的第一个元素
*   获取第一个元素，以及其余的元素
*   从数组的第一个元素获取 name 属性
*   获取 name 属性，然后将其赋给名为 firstName 的变量
*   从数组中获取第二个元素