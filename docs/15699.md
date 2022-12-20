# 使用箭头函数定义方法

> 原文：<https://dev.to/kayut/using-arrow-function-to-define-a-method-2em3>

嗨，

我开始学习 React，现在我有点困惑在哪里使用 Arrow-function 来定义一个方法，在哪里不使用它。

# 首先让我们检查对象的字面量

这段代码有效:

```
const person = {
  points: 23,
  score() {
    return this.points++;
  }
};

person.score(); // Works 
```

但是下面的代码不起作用，因为我们将 score()方法定义为一个箭头函数:

```
const person = {
  points: 23,
  score: () => {     // Arrow function
    return this.points++;
  }
};

person.score(); // Doesn't work 
```

不要通过 arrow-function 在对象中定义方法。
**以上说法正确吗？**

# JavaScript 类呢？

这段代码有效:

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

下面的代码使用箭头函数语法来定义 bark()方法，它也可以工作:

```
class Dog {
  constructor(name, bread) {
    this.name = name;
    this.bread = bread;
    this.bark = () => {     // Arrow function
      return `Bark Bark! My name is ${this.name}`;
    }
  }
}
const mini = new Dog('Mini', 'Spitz');
mini.bark(); 
```

带走:通过 arrow-function 在类中定义方法是很好的。

**以上说法正确吗？**

# React 类(类组件)呢？

甚至推荐使用 arrow-function 来定义 React 类中的方法，作为创建“自动绑定”方法的一种方式，例如，可以被事件处理程序使用但仍然绑定到类的方法。

**以上说法正确吗？**