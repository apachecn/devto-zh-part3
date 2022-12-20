# 让你的函数调用更具可读性

> 原文：<https://dev.to/hamatti/make-your-function-calls-more-readable-12n1>

## 简介

> “的确，花在阅读和写作上的时间比远远超过 10 比 1。作为编写新代码工作的一部分，我们不断地阅读旧代码。...[因此，]使其易于阅读会使其更易于书写。”-罗伯特·c·马丁，《干净的代码:敏捷软件工艺手册》

编写易于理解、修改和扩展的代码是软件开发人员的一个好目标。但是很难。如此之难，以至于已经有很多关于这个话题的书被写出来，世界各地的人们一直在开会讨论这个话题。

如果你一直在编写或阅读大量的 Javascript，你可能见过这样的函数调用:

```
getUnicorns(10, 5, 10, true);
getUnicorns(7); 
```

这段代码很难读懂。那些数字和布尔值是什么意思？

这些功能在野外成长。也许它开始是一个非常简单的函数，随着新的需求，人们只是一个接一个地添加这些参数，最后一些对代码有新看法的人会感到非常困惑。或者也许它以四个论点开始，但是写它的人在那之后立即打电话，知道他们想要做什么，并且在头脑中有每件新鲜事物。

不管是怎么发生的，都有问题。在未来，有人(另一个人，甚至是未来的你)会遇到这个代码，为了理解发生了什么，他们必须做研究，并记住很多东西。所有这些都分散了他们构建高质量代码的注意力。

不可读的代码吸引不可读的代码(和错误🐜).

## Python 是怎么做到的？命名参数

学习另一种语言是帮助我们成为更好的开发人员的好方法。同时，对于您编写的代码，坚持惯用的风格也很重要。所以这是一种优雅的平衡。

在 Python 中，有一种称为*的机制，命名参数*(也称为*关键字参数*)，当函数被调用时，这些参数必须被命名。

```
def get_unicorns(amount, min_age = 0, max_age = 100, only_magical = false):
    pass

get_unicorns(10, min_age=5, max_age=10, only_magical=true) 
```

容易读多了！

## 让我们把 JS 代码做得更好

不幸的是，在 Javascript 中我们没有命名参数。然而，随着 [ES6 对象析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring)，我们可以做一些类似的事情。

### 对象析构快速入门

```
/* Extract properties you're interested in */
const { name, age } = unicorn

/* is the same as */
const name = unicorn.name
const age = unicorn.age

/* ----- */

/* Give default values to destructured properties */
const { name, age = 5 } = unicorn

/* which is same as */
const name = unicorn.name
const age = unicorn.age !== undefined ? unicorn.age : 5

/* ----- */

/* Give default value to function parameter */
function getUnicorns(amount, options = {}) 
```

### Javascript 中的“命名参数”

利用我们对对象析构的了解，让我们的原始代码更具可读性。

```
function getUnicorns(amount, {minAge = 0, maxAge = 99, onlyMagical = false} = {}) {}

getUnicorns(10, {minAge: 5, maxAge: 10, onlyMagical: true});
getUnicorns(7);
getUnicorns(5, {onlyMagical: true}); 
```

就像在我们的 Python 例子中，像这样通读代码更令人愉快，我们可以专注于重要的部分:逻辑和目标。

除了提高可读性之外，这种对象析构方法的另一个好处是，您不必担心参数的顺序，甚至不必担心它们是否都存在。

如果你觉得函数定义中的析构变得拥挤，尤其是缺省值，你可以把它提取到函数体:

```
function getUnicorns(amount, opts = {}) {
    const {
      minAge = 0,
      maxAge = 99,
      onlyMagical = false
    } = opts
    /* rest of the function body here */
} 
```

上一个例子的缺点是，您看不到编辑器的内联自动完成或文档中可用的默认值和选项。

## 用 Typescript 进一步改进

如果您使用的是 Typescript，那么使用这种方法可以获得更多的好处。

```
type UnicornFilter = {
  minAge?: Number;
  maxAge?: Number;
  onlyMagical?: Boolean;
}

function getUnicorns(
  amount: Number,
  {minAge = 0, maxAge = 99, onlyMagical = false}: UnicornFilter = {},
) {}

getUnicorns(10, {minAge: 5, maxAge: 10, onlyMagical: true});
getUnicorns(7);
getUnicorns(5, {onlyMagical: true}); 
```

通过将类型定义为`UnicornFilter`，我们获得了一些东西:

1.  你不会用不应该有的参数来调用函数
2.  您为参数传入正确的值
3.  编辑器中的自动完成功能变得更好，因为它知道会发生什么。

## 结论

在处理代码时，牢记可读性是值得挑战的。通常情况下，随着时间的推移，小的变化越积越多，事情就会变糟。开始简单，然后被要求“就加这一个东西”，不一定考虑长远的可读性。

当你添加一些小的东西时，要勇于重构你的代码。当代码和它所做的事情在你脑海中记忆犹新的时候总是最容易做到的，而不是 6 个月后你的新团队成员看着它并试图弄清楚它应该做什么的时候。