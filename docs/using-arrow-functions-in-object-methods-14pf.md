# 在对象方法中使用箭头函数

> 原文：<https://dev.to/kayut/using-arrow-functions-in-object-methods-14pf>

有可能使用箭头函数来定义一个类中的方法吗？

如果可能的话。如何用 arrow 函数定义下面例子中的 bark()方法？

```
class Dog {
    constructor(name, bread) {
        this.name = name;
        this.bread = bread;
    }

    bark() {
        return `Bark Bark! My name is ${this.name}`;
    }
}
const mini = new Dog('Mini', 'Spitz');
mini.bark(); 
```

我试过了，但它显示:未捕获的 SnytaxError。

```
bark = () => {
        return `Bark Bark! My name is ${this.name}`;
    } 
```