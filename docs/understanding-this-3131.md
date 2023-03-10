# 理解“这个”

> 原文：<https://dev.to/delph/understanding-this-3131>

# ‘这’是什么？

简而言之，JavaScript 中的`this`引用的是正在执行当前函数的对象。

## 那么我们如何确定被引用的对象呢？

#### (#1。)如果函数是对象的一部分(即一个方法)，`this`引用对象。

```
const bird = {
  name: "Tweety",
  printThis() {
    console.log(this); // `this` references `bird`
  }
};

bird.printThis(); // { name: 'Tweety', printThis: ƒ } 
```

#### (#2。)如果函数是常规函数(不是对象的一部分)，`this`引用运行环境中的全局对象(即。浏览器中窗口和节点中的全局)

```
function bird() {
  console.log(this);
}

// If called in browser:

bird(); // Window {stop: ƒ, open: ƒ, alert: ƒ, ...} 
```

注意:如果一个方法有回调函数，回调函数中的`this`引用全局对象。

```
const bird = {
  birdName: "tweety",
  sounds: ["chirp", "quack", "tweet"],
  printBirdSounds() {
    this.sounds.forEach(function(sound) {
      console.log(this.birdName, sound);
    });
  }
};

bird.printBirdSounds(); // undefined 'chirp'
// undefined 'quack'
// undefined 'tweet' 
```

在上面的例子中，`printBirdSounds()`方法中的`this.sounds`正确地引用了`bird`对象，因为它遵循了上面的规则 1。

然而，由于行日志记录`this.birdName`在常规函数中，`this`现在遵循规则 2 并引用全局对象。因为在全局对象上没有`birdName`属性，所以它打印`undefined`。

要解决这个问题，请参见下面的规则 4。

#### (#3。)如果在构造函数的情况下使用`new`关键字调用函数，`this`引用一个新的空对象。

```
function Bird() {
  console.log(this);
  this.name = "tweety";
  console.log(this);
}

new Bird();
// {}
// { name: 'tweety' } 
```

#### (#4。)如果在一个箭头函数中使用了`this`,那么它的值是从它的封闭作用域(即。遵循词法范围规则)。

这是因为箭头函数没有自己的`this`值。

使用箭头函数重写规则 2 中的第二个示例，

```
const bird = {
  birdName: "tweety",
  sounds: ["chirp", "quack", "tweet"],
  printBirdSounds() {
    this.sounds.forEach(sound => console.log(this.birdName, sound));
  }
};

bird.printBirdSounds(); // tweety 'chirp'
// tweety 'quack'
// tweety 'tweet' 
```

有了箭头函数，行记录`this.birdName`现在遵循规则 1 并引用 bird 对象！

#### (#5。)如果用`bind`、`call`、`apply`来调用函数，那么函数内部的`this`就是作为实参传入的对象。

```
function bird() {
  console.log(this);
}

const tweety = {
  sound: "tweet"
};

const bindTweety = bird.bind(tweety);
bindTweety(); // { sound: 'tweet' }
bird.call(tweety); // { sound: 'tweet' }
bird.apply(tweety); // { sound: 'tweet' } 
```