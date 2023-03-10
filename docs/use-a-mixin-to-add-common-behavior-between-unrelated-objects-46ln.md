# 使用 Mixin 在不相关的对象之间添加公共行为

> 原文：<https://dev.to/vish448/use-a-mixin-to-add-common-behavior-between-unrelated-objects-46ln>

行为是通过继承共享的。然而，有些情况下，继承并不是最好的解决方案。继承不适用于不相关的对象，如鸟和飞机。它们都能飞，但鸟不是一种飞机，反之亦然。

对于不相关的对象，最好使用 mixins。mixin 允许其他对象使用函数集合。

```
 let flyMixin = function(obj) {
  obj.fly = function() {
    console.log("Flying, wooosh!");
  }
}; 
```

The flyMixin takes any object and gives it the fly method. Let's see an example.

```
 let bird = {
  name: "Donald",
  numLegs: 2
};

let plane = {
  model: "777",
  numPassengers: 524
};

flyMixin(bird);
flyMixin(plane); 

```

在这里，bird 和 plane 被传递到 flyMixin，然后它将 fly 函数分配给每个对象。现在鸟和飞机都能飞了:
 `bird.fly(); // prints "Flying, wooosh!"

plane.fly(); // prints "Flying, wooosh!"` 

注意:mixin 如何允许不相关的对象 bird 和 plane 重用相同的 fly 方法。