# 维护大型 JavaScript 应用程序

> 原文：<https://dev.to/molily/maintaining-large-javascript-applications-4aoj>

我们从长期维护大型 JavaScript 应用程序中学到的经验。

在[我们的机构](https://9elements.com)，一个客户项目通常会持续几个月。从第一次接触客户和设计阶段到实施和初始启动，一个项目大约需要半年时间。但是有时我们会在几年的时间里开发和维护一个特定的软件。

例如，我们在 2012 年为贝塔斯曼基金会启动了 [GED VIZ](https://viz.ged-project.de) ，在 2013 年发布，每隔几年就会添加新的功能和数据。2016 年，我们将核心可视化变成了一个可重用的库，并进行了重大重构。欧洲中央银行(ECB)至今仍在使用流数据可视化引擎。另一个长期项目是[经合组织数据门户](https://data.oecd.org)前端:我们在 2014 年开始实施，我们仍在扩展代码库。

在主要开发阶段之后，我们应用修正并添加新特性。通常没有预算进行重大的重构，甚至重写。因此，在一些项目中，我坚持使用 4-6 年前写的代码和当时流行的库栈。

## 小改进代替大改写

提到的两个项目都是相当大的客户端 JavaScript 应用程序。如今，多年来关于维护现有 JavaScript 代码库的博客文章已经很少了。不过，您会发现许多关于用目前流行的任何 JavaScript 框架重写前端的帖子。

<figure>

[![Satirical book cover: Rewriting Your Front End Every Six Weeks by @ThePracticalDev](img/82a54c0d9355ae902925af1b3c1b0d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9q7sdXrU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://9elements.com/img/rewriting-frontend.jpg)

<figcaption>[@ThePracticalDev](https://twitter.com/ThePracticalDev/status/715623065078644738)</figcaption>

</figure>

迁移到一组新的库和工具是一项巨大的投资，可能很快就会有回报。它可以简化维护。这可能会降低变革的成本。它允许更快地迭代和更快地实现新特性。它可以减少误差，提高鲁棒性和性能。最终，这种投资可能会降低总拥有成本。

但是当一个客户不能进行这种投资时，我们会寻找如何逐步改进现有代码库的方法。

## 从长期项目中学习

对于一些 web 开发人员来说，受困于现有的代码库是一场噩梦。对于他们最近没有编写的代码，他们以贬义的方式使用“遗留”一词。

对我来说，恰恰相反。在几年的时间里维护一个项目的代码比多个短暂的、一劳永逸的项目教会了我更多的软件开发知识。

最重要的是，它让我面对我几年前写的代码。几年前我所做的决定对今天的整个系统都有影响。从长远来看，我今天所做的决定将决定这个系统的命运。

我经常在想:今天我会做些什么不同的事情？有哪些需要改进的地方？像每一个开发者一样，我有时会有摧毁一切，从头开始构建的冲动。

但大多数时候，我对现有代码的问题更加微妙:今天，我会用不同的结构编写相同的逻辑。让我向您展示我在 JavaScript 代码中发现的主要结构问题。

## 避免复杂的结构

我说的“复杂”不仅仅是指大。每个重要的项目都有很多逻辑。许多案例需要考虑和测试。要处理的数据不同。

复杂性来自于不同关注点的交织。没有人能完全避免这种情况，但是我已经学会了先[分离关注点](https://en.wikipedia.org/wiki/Separation_of_concerns),然后以可控的方式将它们带回来。

让我们看看 JavaScript 中简单和复杂的结构。

### 功能

最简单的可重用 JavaScript 代码是一个函数。特别是一个 *[纯函数](https://medium.com/@jamesjefferyuk/javascript-what-are-pure-functions-4d4d5392d49c)* ，它得到一些输入并产生一个结果(返回值)。该函数将所有必需的数据作为参数显式获取。它不会更改输入数据或其他上下文数据。这样的函数易于编写、易于测试、易于文档化，并且易于推理。

编写好的 JavaScript 不一定需要高级设计模式。首先也是最重要的，它需要以一种巧妙而有益的方式使用最基本的技术的技能:用[做对一件事](https://en.wikipedia.org/wiki/Single_responsibility_principle)的函数来构建你的程序。然后将低级功能组合成高级功能。

JavaScript 中的函数是成熟的值，也称为一级对象。作为一种多聚合语言，JavaScript 允许强大的函数式编程模式。在我的职业生涯中，我只接触了 JavaScript 函数式编程的皮毛，但是理解基础知识已经有助于编写更简单的程序。

### 物体

下一个复杂结构是一个对象。最简单的形式是，对象将字符串映射到任意值，没有逻辑。但是它也可以包含逻辑:当函数被附加到一个对象上时，它就变成了方法。

```
const cat = {
  name: 'Maru',
  meow() {
    window.alert(`${this.name} says MEOW`);
  }
};
cat.meow(); 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中的对象无处不在，用途广泛。一个对象可以作为一个参数包，附带几个处理函数。一个对象可以组合相关的值，也可以构造一个程序。例如，您可以将几个相似的函数放在一个对象上，让它们对相同的数据进行操作。

### 类

JavaScript 中最复杂的结构是类。它是对象的蓝图，同时也是这些对象的工厂。它混合了原型继承和对象的创建。它将逻辑(功能)与数据(实例属性)交织在一起。有时在构造函数上有一些属性，叫做“静态”属性。像“singleton”这样的模式用更多的逻辑重载了一个类。

类是面向对象语言中常见的工具，但是它们需要设计模式的知识和对象建模的经验。尤其是在 JavaScript 中，它们很难管理:构建继承链、[对象组合](https://en.wikipedia.org/wiki/Composition_over_inheritance)、应用 mixins、超级调用、处理实例属性、getter&setter、方法绑定、封装等。ECMAScript 既没有为常见的 OOP 概念提供标准的解决方案，社区也没有就类的使用达成一致的最佳实践。

如果类有一个明确的目的，那么它们就是合适的。我已经学会了避免在课堂上增加更多的关注。例如，[有状态反应组件](https://reactjs.org/docs/state-and-lifecycle.html)通常被声明为类。这对于特定的问题领域是有意义的。它们有一个明确的目的:将道具、状态和两者上运行的一些功能组合在一起。类的中心是`render`函数。

我不再用更多松散相关的逻辑来丰富这些类。值得注意的是，React 团队正在慢慢地从类转移到有状态的功能组件。

同样，Angular 中的[组件类是几个关注点的交集:使用`@Component()`装饰器应用的元数据字段。基于构造函数的依赖注入。状态作为实例属性(输入、输出以及自定义公共和私有属性)。这类课程一点也不简单，也不单一。只要它们只包含所需的特定于角度的逻辑，它们就是可管理的。](https://angular.io/guide/architecture-components)

### 选择结构

这些年来，我得出了这些准则:

1.  使用最简单、最灵活和最通用的结构:函数。如果可能的话，让它成为一个纯函数。
2.  尽可能避免在一个对象中混合数据和逻辑。
3.  尽可能避免使用类。如果你使用他们，让他们做一件事。

大多数 JavaScript 框架都有自己的结构化代码的方式。在 React 和 Angular 等基于组件的 UI 框架中，组件通常是对象或类。选择组合而不是继承很容易:只需创建一个新的轻量级组件类来分离关注点。

这并不意味着人们需要坚持这些结构来建模业务逻辑。最好把这种逻辑放到函数中，从 UI 框架中分离出来。这允许分别发展框架代码和业务逻辑。

## 模块，很多

管理 JavaScript 文件和外部库之间的依赖关系曾经是一团糟。在 9elements，我们是 CommonJS 或 AMD 模块的早期采用者。后来，社区决定采用标准的 ECMAScript 6 模块。

模块成了 JavaScript 中一种基本的代码结构。这取决于它们带来简单还是复杂的用法。

随着时间的推移，我对模块的使用发生了变化。我过去常常用多个导出创建相当大的文件。或者，单个导出是一个巨大的对象，它将一堆常量和函数组合在一起。今天，我试图创建具有一个或几个出口的小型扁平模块。这导致每个函数一个文件，每个类一个文件，依此类推。文件`foo.js`应该是这样的:

```
export default function foo(…) {…} 
```

Enter fullscreen mode Exit fullscreen mode

如果您更喜欢命名导出而不是默认导出:

```
export function foo(…) {…} 
```

Enter fullscreen mode Exit fullscreen mode

这使得单个函数更容易引用和重用。根据我的经验，很多小文件不会带来很大的成本。它们允许更容易地在代码中导航。此外，声明特定代码段的依赖关系也更加有效。

## 避免创建无类型的对象

JavaScript 最好的特性之一是对象文字。它允许您快速创建具有任意属性的对象。上面我们已经看到一个例子:

```
const cat = {
  name: 'Maru',
  meow() {
    window.alert(`${this.name} says MEOW`);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 对象符号是如此简单和富于表现力，以至于它变成了一种今天普遍存在的独立数据格式:JSON。但是在 ECMAScript 版本的发展过程中，object literal 获得了越来越多超出其最初目的的特性。像 [Object Rest/Spread](https://github.com/tc39/proposal-object-rest-spread) 这样的新 ECMAScript 特性允许更自由地创建和混合对象。

在小型代码库中，动态创建对象是一种生产力特性。但是，在大型代码库中，对象文字会成为一种负担。在我看来，这类项目中不应该存在任意属性的对象。

问题不在于对象本身。问题是对象不符合中央类型定义。它们通常是运行时错误的来源:属性可能存在也可能不存在，可能有某种类型也可能没有。该对象可能具有所有必需的属性，但还不止这些。通过阅读代码，您无法判断一个对象在运行时将具有哪些属性。

JavaScript 没有类型定义，但是有几种方法可以更好地控制对象的创建。例如，一个函数可以用来创建所有看起来相似的对象。该函数确保所需的属性存在且有效，或者具有默认值。另一种方法是使用一个创建绝对简单的[值对象](https://en.wikipedia.org/wiki/Value_object)的类。

同样，函数可以在运行时检查参数是否可用。可以使用`typeof`、`instanceof`、`Number.isNaN`等显式检查类型。或者隐式地使用[鸭子打字](https://en.wikipedia.org/wiki/Duck_typing)。

更彻底的解决方案是用类型定义来丰富 JavaScript，比如 TypeScript 或 Flow。例如，在 TypeScript 中，首先要为重要的数据模型定义接口。函数声明其参数和返回值的类型。假设编译器可以访问所有调用，TypeScript 编译器确保只传递允许的类型。

## 健壮代码

这些准则涉及守则的整体结构。在多年从事大型 JavaScript 项目的工作中，我学到了更多的技术和实践。大多数影响 JavaScript 应用程序的健壮性:理解 JavaScript 程序如何失败以及如何防止失败。我在一本免费的在线书籍中汇编了这些技巧:

**[健壮的客户端 JavaScript——开发者指南](https://molily.de/robust-javascript/)**

## 鸣谢

感谢 9elements 的设计师 Susanne n hler 创作了这幅挑逗性的插图。

学到了什么？与他人分享这篇文章或者
随时[加入我们的简讯](https://mailchi.mp/af3f70f18955/9elements-lets-stay-in-touch)。

*最初发布于[9elements.com](https://9elements.com/io/maintaining-large-javascript-projects/)2019 年 1 月 15 日。*