# JavaScript 的新类字段会搬起石头砸自己的脚！

> 原文：<https://dev.to/trusktr/javascript-s-new-class-fields-will-shoot-you-in-the-foot-473j>

TLDR，将以下内容粘贴到你的 Chrome 控制台中:

```
class Animal {
    #sound = 'rustling noise in the bushes'

    get sound() { return this.#sound }
    set sound(val) { this.#sound = val }

    makeSound() {
        console.log(this.#sound)
    }
}

const a = new Animal
a.makeSound() // 3.14

class Lion extends Animal {
    sound = 'RAWR!'
}

const lion = new Lion
lion.makeSound() // BUG! Expected "RAWR!" but got "rustling noise in the bushes" 
```

这个例子并不像你预期的那样工作，因为表达式`sound = 'RAWR!'`使用了`[[Define]]`语义，而不是`[[Set]]`语义。

什么是`[[Define]]`和`[[Set]]`语义？

让我解释一下如何使用不带类字段的普通 ES6 代码。

使用`[[Set]]`语义，`Lion`类可以写成这样:

```
class Lion extends Animal {
    constructor() {
        super()
        this.sound = 'RAWR!'
    }
} 
```

该版本将在表达式`this.sound = 'RAWR!'`中触发超类 getter/setter。`lion.makeSound()`的结果将是`"RAWR!"`，因为该值将存储在`Animal`类的私有`#sound`字段中。

现在，使用`[[Define]]`语义，我们将改为在实例上“定义”属性，如下所示:

```
class Lion extends Animal {
    constructor() {
        super()
        Object.defineProperty(this, 'sound', { value: 'RAWR!' })
    }
} 
```

这个版本就是类字段做的。类字段直接定义了`this`上的属性，因此`sound`字段不会触发 Animal 内部的超类 getter/setter，因此`'RAWR!'`值永远不会存储在 Animal 的私有`#sound`字段中。因此，我们被击中了脚。

在 JS `class` es 的设计中，类字段不作为一等公民保留继承。

这就是为什么[许多人不喜欢 class-fields 提议](https://github.com/tc39/proposal-class-fields/issues/100)的原因之一，而且不幸的是，尽管社区中有如此多的反对，Chrome 的 V8 引擎已经在提供该功能。

下面是向 Chrome 报告的*设计缺陷*:https://bugs.chromium.org/p/chromium/issues/detail?[id=962192](https://bugs.chromium.org/p/chromium/issues/detail?id=962192)

自从 ES6 功能开始推出以来，没有任何功能比当前的类字段提议更具争议性和更不受欢迎。

如果你对(私有或公共)职业领域有任何想法，请在那些 GitHub 问题中留下你的想法。我们获得的社区投入越多越好。