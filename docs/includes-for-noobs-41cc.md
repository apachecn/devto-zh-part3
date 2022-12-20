# 。包括 Noobs

> 原文：<https://dev.to/bananabrann/includes-for-noobs-41cc>

### **内容**

1.  紧急着陆
2.  句法
3.  在数组内搜索
4.  告诉它什么时候开始！(索引和索引底片)
5.  例子

* * *

### **迫降**

~~窥视 JavaScript 的灵魂，询问代码的意义~~ `.include`是一个 JavaScript 方法，它搜索你传递给它的任何东西，无论你告诉它去哪里搜索。然后，根据它是否找到了您要搜索的内容，返回 true 或 false。这是一个非常简单，但是非常有效的方法。对于我们这些初学者(我和你！)我们将只看语法和附加到数组变量。

##### 快速笔记

*   您不能使用`.includes`一次搜索多个项目。

* * *

### **语法**

`.includes`的语法是`.includes(value, i)`，其中`value`是我们要搜索的内容，可以是整数或字符串，而`i`是我们开始搜索的地方。记住 JavaScript 从 0 开始！

* * *

### **在数组内搜索**

假设我们有一个道德品质的数组，我们想知道数组中是否包含某些项目。有了`.includes`，我们可以轻松做到这一点！让我们来看看。

```
const morals = ["honor", "courage", "justice", "yourself"]; 
```

Enter fullscreen mode Exit fullscreen mode

我们试图找到一些勇气来继续处理 JavaScript，所以让我们用`.includes`来看看`morals`是否有我们的勇气！
要在数组上做到这一点，我们只需调用数组并在它后面放置`.includes`的语法，如下所示:

*//是电脑的反应。如果你想看，别忘了`console.log`*

```
const morals = ["honor", "courage", "justice", "yourself"];

morals.includes("courage"); 
// true
morals.includes("yourself"); 
// true
morals.includes("funny"); 
// false 
```

Enter fullscreen mode Exit fullscreen mode

也可以传入整数而不是字符串！

```
const numbers = [12, 22, 33, 44, 55];

console.log(numbers.includes(22));
// true
console.log(numbers.includes(39));
// false 
```

Enter fullscreen mode Exit fullscreen mode

如果你想玩这些例子，看看这个代码笔！

* * *

### **告诉它什么时候开始**

##### **(指数和负指数)**

现在我们已经有了`.includes`的基本知识，让我们看看第二件事`.includes`可以做什么。我们来复习一下语法:

```
.includes(value, i) 
```

Enter fullscreen mode Exit fullscreen mode

早些时候，我们将东西代入`value`部分，现在我们将东西代入`i`或索引。对于`.includes`方法，这意味着计算机将从哪里*开始*寻找数字。

看看这段代码:

```
const cities = ["Dallas", "Miami", "New York City", "Seattle"]; 
```

Enter fullscreen mode Exit fullscreen mode

当我们只是传入一个字符串进行搜索时，正如我们在上一节中所做的那样，计算机默认从 0 开始。放入一个数字，记住 JavaScript 从 0 开始，我们可以告诉 JavaScript 什么时候搜索它。如果一个值在数组内，但在索引之前，它将因此返回 false。

注意输出中的变化:

```
const cities = ["Dallas", "Miami", "New York City", "Seattle"];

cities.includes("Dallas");
// true
cities.includes("Dallas", 0);
// true
cities.includes("Dallas", 1);
// false 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？很好！现在让我们用`.includes`做最后一件事...负指数！呜！

负索引的作用与索引相同，但从末尾开始向前工作。从后面开始计数时，0 是第一个数字*而不是*，这与常规的 JavaScript 数字计数相反。所以在一个数组中，1 是-3 的索引。

让我们在显微镜下看看:

```
const people = ["Mike", "Jebs", "Sarah", "Gary", "Phil", "Merilyn", "Macy", "Stacy", "Hacy", "Lacy"];

people.includes("Lacy", -2);
// true. JS starts its search at Hacy
people.includes("Merilyn", -4)
// false. JS starts its search at Macy 
```

Enter fullscreen mode Exit fullscreen mode

* * *

恭喜你，你现在明白了这个简单而强大的小方法！走进世界，现在就用`.includes`解决所有的问题。

觉得这个博客有帮助吗？不喜欢吗？让我知道！

谢谢，祝编码愉快！
~bananabrann