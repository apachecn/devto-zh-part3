# 编写更快的 JavaScript

> 原文：<https://dev.to/yashints/write-faster-javascript-18f7>

大多数时候，我们编写的代码都是从互联网上复制粘贴过来的。如今，StackOverflow 是寻找各种问题解决方案的主要来源。但是，在没有真正了解幕后发生了什么的情况下，盲目地复制粘贴代码可以吗？

# 一点脉络

当我说 **StackOverflow** 不应该盲目使用时，不要误会我的意思。对于世界各地开发人员面临的大多数日常问题和 bug，这是一个很好的信息来源。只是我们应该更主动一点，在所有可行的选择中做出最佳选择。

让我给你看一些例子，一段代码可以用多种方式编写，最明显的选择不一定是最好的。

## 链接数组循环链接

假设我们有一个包含名称和年龄属性的`200,000`对象的数组。我们想要所有 22 岁以下的人的名字(假设我们有他们中的`100,000`)。这是大多数人可能使用的解决方案:

```
const under22People = originalList
  .filter(x => x.age < 22)
  .map(x => x.name) 
```

Enter fullscreen mode Exit fullscreen mode

自从`ES5`和`ES6`被引入后，一组很好的数组方法出现了。`ES6s`更干净的语法使得为了产生我们想要的结果而链接方法变得更容易。问题在于我们如何使用这些好方法，却没有意识到它们是如何执行的。

乍一看，这似乎是一个非常好的代码，但是如果我们仔细观察，我们有一个循环，它在过滤时运行了`200,000`次，在选择名字时又运行了`100,000`次。

在这里我们只需要循环一次这些项目。那么让我们来看看如何重写这段代码:

```
const under22People = []
originalList.forEach(({ age, name }) => {
  age >= 18 && under22People.push(name)
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在这段代码只运行了`200,000`次。它看起来不如链式方法好，但是它确实有更好的性能。

您甚至可以使用`reduce`方法来做同样的事情:

```
const under22People = originalList.reduce(
  (acc, { age, name }) => {
    return age >= 18 ? [...ac, name] : acc
  },
  []
) 
```

Enter fullscreen mode Exit fullscreen mode

它看起来的确可读性差了很多，但对我们来说，它做了完全相同的工作。

## 滥用箭头功能

当你写`JavaScript`的时候，你可以问自己一个很好的问题:你是否知道传统函数和箭头函数(又名胖函数)之间的区别。

从 MDN Web 文档:

> 箭头函数表达式在语法上是常规函数表达式的紧凑替代，尽管它没有绑定到`this`、`arguments`、`super`或`new.target`关键字。箭头函数表达式不适合用作方法，也不能用作构造函数。

在你问之前，不，它们不是 JavaScript 中类似于`C#`的匿名函数。但是现在你唯一需要关心的是他们没有范围。

他们的许多用例之一就是在课堂上使用它们。考虑在`React`中你不再需要手动绑定你的函数，比如:

```
this.handleClick = this.handleClick.bind(this) 
```

Enter fullscreen mode Exit fullscreen mode

取而代之的是，你应该这样写你的类:

```
class MyComponent extends Component {
  handleClick = () => {
    // ...
  }

  render() {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能知道，通常的功能在原型中定义，并将在所有实例中共享。如果我们有一个`N`组件的列表，这些组件将共享相同的方法。因此，如果我们的组件被点击，我们仍然调用我们的方法`N`次，但是它将调用相同的原型。由于我们在原型中多次调用同一个方法，`JavaScript`引擎可以优化它。

另一方面，对于类属性中的箭头函数，如果我们正在创建`N`组件，这些`N`组件也将创建`N`函数。通过查看 transpiled 版本，您可以看到类属性是在构造函数中初始化的。这意味着如果我们点击`N`组件，将会调用`N`不同的功能。

所以下次你写新的闪亮的`React`组件时考虑这种方法。

## 嵌套函数 VS IIFEs

将一个函数嵌套在另一个函数中似乎是将一些逻辑与外界隔离的好主意。考虑以下代码:

```
function doSomething(arg1, arg2) {
  function nestedHelper(arg) {
    return process(arg)
  }

  return nestedHelper(arg1) + nestedHelper(arg2)
} 
```

Enter fullscreen mode Exit fullscreen mode

上述代码的问题在于，每次调用`doSomething`时，`nestedHeler`都会被重新创建。为了防止这种情况，你可以使用一个`IIFE`(立即调用函数):

```
const result = (function() {
  function privateHelper(arg) {
    var result = process(arg)
    return result
  }

  return function(arg1, arg2) {
    return (
      privateHelper(arg1) + privateHelper(arg2)
    )
  }
})() 
```

Enter fullscreen mode Exit fullscreen mode

当执行这段代码时，嵌套方法将只创建一次🤷‍♂️.

# 尽可能使用集合而不是数组

一个`array`和一个`Set`之间最明显的区别是`array`是一个*索引的*集合，而`Set`是基于键的。

那么为什么你应该使用`Set`？

### 搜索一个项目

使用`indexOf()`或`includes()`来检查一个数组中是否存在一个项目是很慢的。在`Set`你可以用`has()` :
找到一个非常简单的项目

```
const mySet = new Set([1, 1, 2])

console.log(mySet.has(2)) // true 
```

Enter fullscreen mode Exit fullscreen mode

### 删除一个项目

在`Set`中，您可以根据项目的值删除项目。在数组中，等效的是基于元素的`index`使用`splice()`。如前所述，依赖指数是缓慢的。

```
const mySet = new Set([1, 2, 3, 4, 5])

mySet.delete(1) 
```

Enter fullscreen mode Exit fullscreen mode

### 插入一个项目

使用`push()`或`unshift()`将项目添加到`Set`比添加到数组要快得多。

```
const mySet = new Set([1, 2])

mySet.add(3) // Successfully added 
```

Enter fullscreen mode Exit fullscreen mode

### 储存楠

您不能使用`indexOf()`来查找值`NaN`，而`Set`能够存储这个值。

### 删除重复项

对象只存储唯一的值。如果您希望避免存储重复项，这与数组相比是一个显著的优势，因为数组需要额外的代码来处理重复项。

```
const mySet = new Set([1, 1, 2])

mySet.add(3) // Successfully added

console.log(mySet.values()) // 1,2,3 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

在现实世界的业务应用程序中编写代码时，有许多这样的例子需要您小心谨慎。速度是每个 web 应用程序最重要的部分之一，考虑到以上几点，将会提高代码的性能，从而让用户更满意😊。

希望这能帮助你开始思考，除了在网上找到的第一个解决方案之外，还有什么其他方法可以解决同样的问题。