# ECMAScript 类——保持私有

> 原文：<https://dev.to/proticm/ecmascript-classes-keeping-things-private-1d34>

> 最初发布于[devi conduct](https://devinduct.com/blogpost/23/ecmascript-classes-keeping-things-private)

## 简介

像往常一样，我们将从几个理论解释开始。ES 类是 JavaScript 中新的语法糖。它们提供了一种简洁的方式来编写和实现相同的东西，就像我们使用原型链一样。唯一的区别是它看起来更好，如果你来自 C#或 Java 世界，感觉更自然。有人可能会说它们不适合 JavaScript，但对我来说，使用类或 ES5 原型标准没有问题。

它们提供了一种更简单的方法来封装和创建一组固定的方法，这些方法在具有有效内部状态的实体上操作。基本上，我们可以写得更少，以实现更多，这是整个点。有了它们，JavaScript 正朝着面向对象的方式发展，通过使用它们，我们将应用程序分解成对象而不是函数。不要误解我的意思，将应用程序分成函数并不是一件坏事，实际上，这是一件很棒的事情，它可以为类提供一定的好处，但这是另一篇文章的主题。

从更实际的角度来说，我们可以说，每当我们想要在我们的应用程序中描述一个来自真实世界的模型时，我们都会使用一个类来完成。例如，建筑物、汽车、摩托车...等等。他们代表一个真实世界的实体。

## 范围

在服务器端语言中，我们有一些叫做*的访问修饰符*或*的可见性级别*，比如`public`、`private`、`protected`、`internal`、`package`...不幸的是，JavaScript 只支持前两种方式。我们不写访问修饰符(`public`或`private`)来声明我们的字段和 JavaScript，在某种程度上，假设你已经将所有东西的作用域设为 public，这就是我写这篇文章的原因。

请注意，我们有一种在类中声明私有和公共字段的方法，但是这些字段声明是一种实验性的特性，因此使用起来还不安全。

```
class SimCard {
  number; // public field
  type; // public field
  #pinCode; // private field
} 
```

> 没有编译器，例如巴别塔 ，不支持上述字段声明。

## 保密——封装

封装是编程中使用的一个术语，当我们想说某些东西受到保护或对外部世界隐藏时。通过保持数据私有并且仅对所有者实体可见，我们*封装了*它。在本文中，我们将使用几种方法来封装我们的数据。让我们深入研究一下。

### 1。按照惯例

这不过是伪造数据或变量的`private`状态。事实上，它们是公开的，每个人都可以访问。我遇到的两个最常见的隐私惯例是前缀`$`和`_`。如果某个东西的前缀是这些符号中的一个(通常在应用程序中只使用一个)，那么它应该作为该特定对象的非公共属性来处理。

```
class SimCard {
  constructor(number, type, pinCode) {
    this.number = number;
    this.type = type;

    // this property is intended to be a private one
    this._pinCode = pinCode;
  }
}

const card = new SimCard("444-555-666", "Micro SIM", 1515);

// here we would have access to the private _pinCode property which is not the desired behavior
console.log(card._pinCode); // outputs 1515 
```

### 2。封闭的隐私

当涉及到保持可变范围时，闭包是非常有用的。它们可以追溯到很久以前，并且被 JavaScript 开发人员使用了几十年。这种方法为我们提供了真正的隐私，并且外界无法访问这些数据。它只能由所有者实体管理。这里我们要做的是在类构造函数中创建局部变量，并用闭包捕捉它们。要使它工作，方法必须附加到实例上，而不是在原型链上定义。

```
class SimCard {
  constructor(number, type, pinCode) {
    this.number = number;
    this.type = type;

    let _pinCode = pinCode;
    // this property is intended to be a private one
    this.getPinCode = () => {
        return _pinCode;
    };
  }
}

const card = new SimCard("444-555-666", "Nano SIM", 1515);
console.log(card._pinCode); // outputs undefined
console.log(card.getPinCode()); // outputs 1515 
```

### 3。使用符号和 Getters 的隐私

符号是 JavaScript 中一种新的原始数据类型。它是在 ECMAScript 版本 6 中引入的。由`Symbol()`调用返回的每个值都是唯一的，这种类型的主要目的是用作对象属性标识符。

因为我们的目的是在类定义之外创建符号，而不是全局的，所以引入了一个模块。通过这样做，我们能够在模块级创建我们的私有字段，将它们附加到构造函数中的类对象，并从类 getter 返回符号键。注意，我们可以使用原型链上创建的标准方法来代替 getter。我选择了使用 getter 的方法，因为我们不需要调用函数来获取值。

```
const SimCard = (() => {
  const _pinCode = Symbol('PinCode');

  class SimCard {
    constructor(number, type, pinCode) {
      this.number = number;
      this.type = type;
      this[_pinCode] = pinCode;
    }

    get pinCode() {
       return this[_pinCode];
    }
  }

  return SimCard;
})();

const card = new SimCard("444-555-666", "Nano SIM", 1515);
console.log(card._pinCode); // outputs undefined
console.log(card.pinCode); // outputs 1515 
```

这里需要指出的一点是`Object.getOwnPropertySymbols`方法。这个方法可以用来访问我们想要保持私有的字段。我们类中的`_pinCode`值可以这样检索:

```
const card = new SimCard("444-555-666", "Nano SIM", 1515);
console.log(card[Object.getOwnPropertySymbols(card)[0]]); // outputs 1515 
```

### 4。与 WeakMap 和 Getters 的隐私

ECMAScript 版本 6 中也引入了`Map`和`WeakMap`。它们以键/值对格式存储数据，这使得它们非常适合存储我们的私有变量。在我们的例子中，在模块级别为每个属性定义了一个`WeakMap`，并且在类构造函数中设置了每个私有密钥。该值由 getter 类检索，同样，选择它是因为我们不需要调用函数来检索该值。

```
const SimCard = (() => {
  const _pinCode = new WeakMap();
  const _pukCode = new WeakMap();

  class SimCard {
    constructor(number, type, pinCode, pukCode) {
      this.number = number;
      this.type = type;
      _pinCode.set(this, pinCode);
      _pukCode.set(this, pukCode);
    }

    get pinCode() {
       return _pinCode.get(this);
    }

    get pukCode() {
       return _pukCode.get(this);
    }
  }

  return SimCard;
})();

const card = new SimCard("444-555-666", "Nano SIM", 1515, 45874589);
console.log(card.pinCode); // outputs 1515
console.log(card.pukCode); // outputs 45874589
console.log(card._pinCode); // outputs undefined
console.log(card._pukCode); // outputs undefined 
```

## 结论和进一步阅读

希望你会发现这些例子很有用，它们会在你的工作流程中找到一席之地。如果是这种情况，并且你喜欢你读过的东西，那就分享吧。

为了进一步阅读，我推荐关于 [JavaScript 干净代码-最佳实践](https://devinduct.com/blogpost/22/javascript-clean-code-best-practices)的帖子。

感谢您的阅读，下一篇文章再见。