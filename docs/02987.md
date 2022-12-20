# 请记住，使用 Object.assign()可能会指向一个空对象

> 原文：<https://dev.to/alexmacarthur/remember-to-probably-target-an-empty-object-with-object-assign-3274>

回想一下，2014 年合并对象是什么样子。JavaScript 运行时本身并不支持这一特性，通过遍历 StackOverflow 找到类似这样的问题解决方案是很常见的:

```
function merge(obj1, obj2) {
  var newObject = {};

  for (var attributeName in obj1) {
    newObject[attributeName] = obj1[attributeName];
  }

  for (var attributeName in obj2) {
    newObject[attributeName] = obj2[attributeName];
  }

  return newObject;
}

var var1 = {
  key1: "value1"
};

var var2 = {
  key2: "value2"
};

console.log(merge(var1, var2));

// { key1: "value1", key2: "value2" } 
```

Enter fullscreen mode Exit fullscreen mode

尽管自那以后 JavaScript 取得了进步，但我仍然怀念这种方法。当你给方法赋值时，发生了什么就非常清楚了。接收这两个对象，并返回一个新的、单独的对象，包含这两个对象的属性和值。你的两个参数都没有被修改，你可以继续在其他地方使用`var1`和`var2`,毫发无损。

最近，我犯了一个错误，认为`Object.assign()`以类似的方式运行。我想要一个新的合并对象，但是我还想保留我传递给方法的值以备后用。

```
let var1 = {
  key1: "value1"
};

let var2 = {
  key2: "value2"
};

let var3 = Object.assign(var1, var2); 
```

Enter fullscreen mode Exit fullscreen mode

我*期望*这给我的三个变量留下如下值:

```
// What I THOUGHT I'd get:

var1 = {
  key1: "value1"
};

var2 = {
  key2: "value2"
};

var3 = {
  key1: "value1"
  key2: "value2"
}; 
```

Enter fullscreen mode Exit fullscreen mode

但我得到的不是这个。相反，`var1`已经被突变，赋予了与`var3` :
完全相同的值

```
// What I ACTUALLY got:

var1 = {
  key1: "value1"
  key2: "value2"
};

var2 = {
  key2: "value2"
};

var3 = {
  key1: "value1"
  key2: "value2"
}; 
```

Enter fullscreen mode Exit fullscreen mode

看着[的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)找`Object.assign()`，这很有道理。语法如下:

```
Object.assign(target, ...sources) 
```

Enter fullscreen mode Exit fullscreen mode

对它的功能描述非常清楚:

Object.assign()方法用于将所有可枚举自身属性的值从一个或多个*源*对象复制到一个*目标*对象。它将返回目标对象。

该方法不保证不改变目标对象。它会复制属性给它，然后把变异后的版本传给你。

显然，这个问题的解决方案非常简单。传递一个新的空对象作为目标，就可以开始了:

```
-let var3 = Object.assign(var1, var2);
+let var3 = Object.assign({}, var1, var2); 
```

Enter fullscreen mode Exit fullscreen mode

多了几个角色，少了很多挫败感，不再有惊喜突变。