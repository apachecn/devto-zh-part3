# 为 JavaScript 的怪异之处辩护

> 原文：<https://dev.to/andychiare/-in-defense-of-javascript-oddities-4eeb>

我的会员们！法庭的女士们先生们！我的委托人经常不公平地成为嘲笑和公众嘲笑的对象。太多次开发者，尤其是其他语言的开发者，[取笑过他](https://charlieharvey.org.uk/page/javascript_the_weird_parts)，[嘲笑过他](https://www.destroyallsoftware.com/talks/wat)。他们坚持考虑，可以说，[诡异](https://www.smashingmagazine.com/2011/05/10-oddities-and-secrets-about-javascript/)，[诡异](https://medium.com/javascript-non-grata/the-top-10-things-wrong-with-javascript-58f440d6b3d8)，[极客](http://www.davidarno.org/2010/05/18/why-javascript-is-a-toy-language/)。

对我的客户进行这些攻击的大部分原因都是基于所谓的怪事，例如

```
[] == ![]    // -> true 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
NaN === NaN             // -> false
Math.max()>Math.min()   // -> false 
```

Enter fullscreen mode Exit fullscreen mode

甚至

```
[] == ''   // -> true
[] == 0    // -> true
[''] == '' // -> true
[0] == 0   // -> true
[0] == ''  // -> false
[''] == 0  // -> true 
```

Enter fullscreen mode Exit fullscreen mode

我们在说什么？有哪些古怪之处？

奇怪的是，如果你向我的客户提交这个请求

```
{} + [] 
```

Enter fullscreen mode Exit fullscreen mode

他用`0`回答？哪里奇怪了？
也许答案中的陌生感不如问题中的陌生感多。如果你问我的委托人这样一个卑鄙的问题，你怎么能指望得到一个有意义的答案呢？
我的客户回答`0`，但他也不妨回答`42`。我不明白为什么一个不同的答案会被认为没有这个奇怪。也许在这种情况下，他最好像其他语言一样，提出一个例外，生气，可能还会侮辱对方？没有，先生们。我的委托人有幽默感，无意冒犯任何人。他意识到了这个要求的荒谬，于是用同样的语气回应。

法庭上的先生们，典型的反对意见是，不仅我的委托人是奇怪的，而且他甚至不一致，因为下面的请求

```
[] + {} 
```

Enter fullscreen mode Exit fullscreen mode

他没有再次用`0`回应，而是用`[object Object]`回应。

即使在这里，陌生在哪里？谁说数组和对象之间的`+`运算是可换的？没有人对`"foo" + "bar"`的结果与`"bar" + "foo"`的结果不同感到惊讶。但是如果当你改变操作数的顺序时，我的客户给出了不同的答案，那么他就奇怪了。

另一个反对我的客户的理由是它不是真正的面向对象语言，因为它没有类，或者说[它拥有的类不是真正的类](https://medium.com/@andrea.chiarelli/a-matter-of-class-9c0a0162674b)。
第一:谁说 OOP 语言一定要有类？
第二:好的，我的客户在支持`new`关键字和`class`关键字方面有点含糊不清。但是他真诚地这样做了，觉得自己和其他冷落他的语言是平等的。

我不能隐藏我的客户与其他语言有一点不同的事实，但也没有不同到值得被嘲笑和侮辱的程度。

事实是，那些指责我的委托人奇怪的人并不了解他，也不想试图了解和理解他。他们如此习惯于僵硬的语言，没有丝毫幽默感(我敢说，没有灵魂),以至于他们把笑话误认为奇怪和不一致。
但如果这些先生们试着更好地了解他，[与我的客户建立亲密关系](http://2ality.com/2012/01/object-plus-object.html)，他们会发现[万事皆有因](https://github.com/denysdovhan/wtfjs)，问题可能不在于我的客户给出的答案，而在于开发商提出的问题。

*本帖原载于[我的媒体简介](https://medium.com/@andrea.chiarelli/in-defense-of-javascript-oddities-32a80a747cf6)T3*