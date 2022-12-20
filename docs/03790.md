# JavaScript 对象在 ES6 类静态方法之前有静态方法吗？

> 原文：<https://dev.to/ermal/did-javascript-objects-have-static-methods-before-es6-class-static-methods-al6>

我看到一个教程，里面用了几个 javascript 类

```
class Products(){}
class UI(){}
class Storage(){} 
```

Enter fullscreen mode Exit fullscreen mode

我想在野外我不会一直使用 ES6 类，所以我应该可以用 ES5 对象重建它们

```
function Car(price) {
    this.price = price,
    this.someMethod = function(){ }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是`Storage`类只有静态方法，这让我有点困惑

```
class Storage {
  static saveProducts(products) { }
  static getProduct(id) { }
  static saveCart(cart) {  }
  static getCart() { }
} 
```

Enter fullscreen mode Exit fullscreen mode

我明白区别，我们不能直接访问`Car.someMethod()`但是我们可以访问`Storage.getCar()`。

我的问题是，这是一个好的做法吗？最重要的是这是不使用类来实现这个功能的方法吗

```
function Storage(){
  this.someMethod(){ }
}
Storage.prototype.someOtherMethod = function(){ }
Storage.theStaticMethod = function(){ } 
```

Enter fullscreen mode Exit fullscreen mode

尽管我相对来说是 JavaScript 新手，但我从未见过 ES6 之前的静态方法。我总是看到通过原型创建的方法。尽管它确实通过了测试(据我所知，`Storage.theStaticMethod`的工作方式和`static theStaticMethod`完全一样),但我的理解正确吗？为什么看起来不对？