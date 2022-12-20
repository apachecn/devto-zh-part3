# 用这些建议清理你的代码！

> 原文：<https://dev.to/dechamp/clean-up-your-code-with-these-tips-4p5f>

## TL:DR

见底部；

## 击穿

看完讨论 [If/else 还是 just if？这让我想到，它可能对你们中的一些人有所帮助，让我分享一些我喜欢遵循的模式，帮助我保持我的代码更小、更干净。](https://dev.to/tyrw/ifelse-or-just-if-1kie)

坚持你已经习惯的或者你从一开始就学会的编码模式/风格是非常容易的。这将有希望挑战你，看看你是否可以提高。

我将用我最近工作的一篇文章中的一个例子向你展示一个典型的场景以及如何清理它。

下面是一些开发人员编写它的方式。

*注意:*我是以 es6 为例进行编码的。

我的代码需要确认主机名是否可用，检查它是否是我正在使用的本地主机站点，如果是，则将我的 cookies 设置为不安全。这是必需的，因为否则，chrome 和其他一些浏览器在标记为安全时将不允许通过 localhost 存储 cookie。看这里[深埋在栈流中](https://stackoverflow.com/a/11957810/11016029)

```
let secure;

// Check if window.location.hostname is set.
if (
    typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname')
) {
    const domain = window.location.hostname;

    if (domain == "localhost" || domain == "127.0.0.1") {
        secure = false;
    } else {
        secure = true;
    }
} else {
    secure = true;
}

export default new Cookie({secure: secure}); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能会马上注意到一些事情。例如通过将顶部的`let secure`设置为`let secure = true`来去除两个“else”语句。

[房产价值速记](https://ariya.io/2013/02/es6-and-object-literal-property-value-shorthand)的使用怎么样？`{secure: secure}`现在变成了`{secure}`。

```
let secure = true;

// Check if window.location.hostname is set, 
if (
    typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname')
) {
    const domain = window.location.hostname;

    if (domain == "localhost" || domain == "127.0.0.1") {
        secure = false;
    } 
}

export default new Cookie({secure}); 
```

Enter fullscreen mode Exit fullscreen mode

我们清理了一下，但我们肯定可以做得更好。任何时候你看到一个 sub“if”语句，你都应该问自己“我该如何清理它？”。如果你知道如何避免的话，你很少需要子“如果”语句。

让我们首先将子“if”语句拆分出来，放在主“if”语句的下面。

我们可以通过将上面的“domain”变量初始化为“null”或“false”(我更喜欢 null，请随意讨论)，然后通过 window.location.hostname 将域设置为主机名(如果它可用的话)

接下来，我们更新我们的 sub“if ”,现在检查“domain ”,它在最初检查 localhost/127.0.0.1 的基础上具有一个 [truthy](https://www.sitepoint.com/javascript-truthy-falsy/) 值。

这提醒了我，让我们用一些正则表达式来清除对 localhost/127.0.0.1 的检查。`domain == "localhost" || domain == "127.0.0.1"`现在变成了`/^(localhost|127\.0\.0\.1)$/.test(domain)`

如果你不喜欢正则表达式，你可以使用 vojtechp 中的这个光滑的提示来使它更容易阅读。

```
const localDomains = [ 'localhost', '127.0.0.1' ];
const isLocal = domain && localDomains.includes(domain); 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以用`Set`
做一个 [miniscruff](https://dev.to/miniscruff) 提到的更干净的版本

```
const localDomains = new Set([ 'localhost', '127.0.0.1' ])
const isLocal = localDomains.has(domain) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道为什么要在使用变量之前声明它们，请阅读[总是声明局部变量](https://www.w3schools.com/js/js_best_practices.asp)。

这让我们有了下文。

```
let secure = true;

// Declared domain variable as a let
let domain = null;

// Check if window.location.hostname is set, 
if (
    typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname')
) {
    domain = window.location.hostname;
}

// Moved out and now checks additionally "domain" is set
if (domain && /^(localhost|127\.0\.0\.1)$/.test(domain)) {
    secure = false;
} 

export default new Cookie({secure}); 
```

Enter fullscreen mode Exit fullscreen mode

希望到现在为止，你已经开始看到每一次，我们是如何改进代码的。

那么我们能走多远呢？让我们看看我们还能做什么。

对我的编码风格的一个主要改进，我从一个随机的博客中学到的。我真的希望我能给他们信任，但不幸的是那是很久以前的事了，我忘了是谁了。

他们展示的是，当涉及到两个或更多值时，将逻辑从 if 语句中移出，并将它们分配给变量。我将不得不写另一个关于这个的帖子，因为你可以用它变得真正有创造力，但是现在我们将保持它简单。

所以我们现在会从

```
if (
    typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname')
) 
```

Enter fullscreen mode Exit fullscreen mode

至

```
const hostnameAvailable = typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname'); 
```

Enter fullscreen mode Exit fullscreen mode

这其中的力量在于，你可以开始清理你的 if 语句，使之更具可读性，或者甚至一起远离它们(在合理的范围内)。

所以知道了这个新技巧，我们就带着这个想法前进。

如果你密切关注，你会发现我们正在构建“如果”语句，彼此独立...因此，我们似乎可以利用[三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

将我们学到的将“如果”逻辑移至变量的新技巧与三元相结合，您就可以做到这一点！

```
let secure = true;

const hostnameAvailable = typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname');
const domain = hostnameAvailable ? window.location.hostname : null;
const isLocal = domain && domain.match(/localhost|127\.0\.0\.1/);

if (isLocal) {
    const secure = false; 
}

export default new Cookie({secure}); 
```

Enter fullscreen mode Exit fullscreen mode

但是，德尚...那个“如果”语句！哦，对了，我们也能修好它。利用翻转真/假与[逻辑非"！"](https://www.sitepoint.com/javascript-double-negation-trick-trouble/)。看看双逻辑非，也在同一页上。

```
const hostnameAvailable = typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname');
const domain = hostnameAvailable ? window.location.hostname : null;
const isLocal = domain && /^(localhost|127\.0\.0\.1)$/.test(domain);
const secure = !isLocal;     

export default new Cookie({secure}); 
```

Enter fullscreen mode Exit fullscreen mode

哇，看起来好多了！

## 总结起来

*   我们了解到，在顶部声明变量有助于消除“else”语句。

*   分解子“如果”语句。

*   将“如果”逻辑移到变量中，然后使用三进制，有助于更容易阅读和链接。

*   当对单个变量进行多次检查时，正则表达式允许进一步清理。

*   使用“非”运算符触发布尔值。

## 更多提示！

```
// instead of this
if (foo === 'a') {
    bar = 'first';
} else {
    bar = 'second';
}

return bar;

// use return to clean it up!

if (foo === 'a') {
    return 'first';
}

return 'second'; 
```

Enter fullscreen mode Exit fullscreen mode

```
// instead of this

const foo = bar ? bar : baz;

// do this
const foo = bar || baz; 
```

Enter fullscreen mode Exit fullscreen mode

这个是由用户[凯伊斯](https://dev.to/kayis)
提供的

```
let secure = true;

try {
  secure = !window.location.hostname.match(/localhost|127\.0\.0\.1/);
} catch (e) {}

export default new Cookie({ secure }); 
```

Enter fullscreen mode Exit fullscreen mode

在评论中添加你的，我会把它们添加到这里并给你加分！

## TL:DR

从

```
let secure;

// Check if window.location.hostname is set.
if (
    typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname')
) {
    const domain = window.location.hostname;

    if (domain == "localhost" || domain == "127.0.0.1") {
        secure = false;
    } else {
        secure = true;
    }
} else {
    secure = true;
}

export default new Cookie({secure: secure}); 
```

Enter fullscreen mode Exit fullscreen mode

至

```
const hostnameAvailable = typeof window !== 'undefined'
    && window.hasOwnProperty('location')
    && window.location.hasOwnProperty('hostname');
const domain = hostnameAvailable ? window.location.hostname : null;
const isLocal = domain && /^(localhost|127\.0\.0\.1)$/.test(domain);
const secure = !isLocal;     

export default new Cookie({secure}); 
```

Enter fullscreen mode Exit fullscreen mode

## 跟进

我真的希望你学到了一些新的技巧。看到自己的代码变得如此小而清晰是一件非常有趣的事情。如果我的一些聪明的读者，教我更多的技巧，我不会感到惊讶！

你喜欢我的帖子吗？有问题吗？我错过了什么或者犯了什么错误吗？让我知道！

-说吧

* * *

瓦里马德有限责任公司。

目前的项目有[https://charactergenerator4000.com](https://charactergenerator4000.com)和 [https://coder.exchange](https://coder.exchange) 。请检查它们，并让我们知道你的想法。