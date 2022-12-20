# 新关键字

> 原文：<https://dev.to/shaneallantaylor/micro-concepts-in-object-oriented-programming-the-new-keyword-3ikh>

## 让我们利用一些句法上的糖- `new`。

关键字`new`为我们做了一堆干净的事情，但是为了充分理解它的作用，我们必须快速回顾一下其他的事情:

### JavaScript 中的  *函数既是对象**又是**函数。*

这意味着这个代码**实际上工作** :

```
function sayHey() {
  console.log('Heeeeey');
}

sayHey.target = 'Tang';
console.log(sayHey.target); // will log 'Tang' to the console 
```

**爽！**

所以函数既是对象又是函数——我们可以用圆括号(`sayHey()`)访问函数位，我们可以用点符号(`sayHey.something`)访问对象位。

函数的对象部分**总是**在那里，它**总是**有一个原型键在里面。存储在 prototype 键中的值呢？*这是一个很大的空对象，这正是我们想要存储函数的地方。*

现在我们需要谈谈 JavaScript 中`new`为我们做的很酷的事情。`new`关键字通过执行以下操作修改紧随其后的函数的执行上下文:

*   它创建一个标签为`this`的属性，并为该标签分配一个空对象
*   在上面的`this`对象上，隐藏属性`__proto__`被赋予对被调用函数的原型属性的引用
*   只要被调用的函数没有返回对象，它就返回存储在`this`属性中的对象(为了澄清这一点，h/t 到[托马斯·布罗耶](https://dev.to/tbroyer)

我们必须手动完成所有这些工作，但是关键字`new`为我们做了**所有这些工作。**

最后，我们来看看它的运行情况:

```
function createCar(color, speed) {
  this.color = color;
  this.speed = speed;
}

createCar.prototype.go = function () {
  console.log('I prefer to drive ' + this.speed + '.');
}

const myCar = new createCar('red', 'fast');
myCar.go(); // will log 'I prefer to drive fast.' to the console
console.log('myCar is', myCar.color); // will log 'myCar is red' to the console 
```