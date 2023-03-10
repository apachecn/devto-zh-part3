# Prism 和关于 Regex 的一些想法

> 原文：<https://dev.to/abdurrahmaanj/prism-and-some-thoughts-on-regex-nm8>

我在浏览 prism.js 的网站时看到了这个:

> 解析器在不正确的语法上失败，正则表达式保持匹配。

作者解释了一个决定，即使用正则表达式。但是，上面的陈述有两点是错误的

*   解析器因语法不正确而失败
*   正则表达式没有到解析器的链接

## 解析器会因为语法不正确而失败吗？

那一个是容易的。难道我们不能编写出持续运行的解析器吗？这个点被第二个点清除

## Regex 和解析器有区别吗？

正则表达式是描述模式的另一种方式，可以是像语言一样的大串，也可以是像在段落中查找电子邮件地址一样的简单串。

编译器理论的书籍有时会用 regex 代替 BNF 来描述元素。从另一个角度看，regex 是如何工作的，是计算机的某种母语吗？不，已经编写了一个解析器来理解正则表达式并完成底层匹配。

## 结论

自然语言解析不时提供一些有趣的思考。这里有一个例子，作者试图说:‘这有用，随它去吧’