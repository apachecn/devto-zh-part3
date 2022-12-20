# javascript 中的装饰模式

> 原文：<https://dev.to/mfaghfoory/decorator-pattern-in-javascript-4048>

编程世界中一个有趣的模式是装饰者模式。这是我从维基百科得到的定义。
“在面向对象编程中，装饰模式是一种设计模式，它允许动态地将行为添加到单个对象中，而不会影响同一类中其他对象的行为。”
我们可以使用它在我们的方法中添加一些额外的行为，例如记录日志、测量运行时间、防止特殊输入发送到函数等等。
我自己在自己的后端项目中用 C#使用过几次这种模式，但我很高兴用 javascript 创建一个简单的库来做这件事，所以我决定与你分享我的经验。首先，我已经为这个库创建了一个库，你可以通过这个链接访问代码:
[https://github.com/mfaghfoory/javascript-object-decorator](https://github.com/mfaghfoory/javascript-object-decorator)

js-decorator.js 文件中有一个简单的名为“装饰”的函数，它接受 5 个参数(其中 4 个是可选的)并返回一个装饰对象。

```
decorate(object, [beforeCall], [afterCall], [onCall], [onException]) 
```

例如，想象我们有一个这样的物体:

```
let product = {
  name: "tv",
  price: 600,
  printInfo() {
    console.log(`${this.name}-${this.price}$`);
  },
  logToDb() {
    //send this object to DB
  }
}; 
```

我们想记录一些函数的结果或者测量它的运行时间，这样我们就可以像这样创建我们的钩子函数，并把它们传递给“装饰”函数:

```
let beforeCall = res => {
};
let afterCall = res => {
};
let onCall = res => {
};
let onException = error => {
};
product = decorate(product, beforeCall, afterCall, onCall, onException); 
```

例如:

```
let beforeCall = res => {
  console.log("before call:" + JSON.stringify(res));
  console.time();
};
let afterCall = res => {
  console.log("after call:" + JSON.stringify(res));
  console.timeEnd();
};
let onCall = (func, args) => {
  //can modify the result like this:
  //let res = func(args);
  //return '--' + res + '--'
};
let onException = error => {
  //log error to db
};
product = decorate(product, beforeCall, afterCall, onCall, onException);
product.printInfo();

//Result:

//before call:{"method":"printInfo","args":[],"object":{"name":"tv","price":600}}
//tv-600$
//after call:{"method":"printInfo","args":[], result: undefined,"object":{"name":"tv","price":600}}
//default: 0.291015625ms 
```

我刚刚创建了这个库，所以请随意给出您的建议，我很乐意在库中应用您的好想法。

快乐编码:)