# Reiwa - JavaScript 国际日期格式与日本的新帝国时代

> 原文：<https://dev.to/girlie_mac/reiwa-javascript-international-date-format-and-japan-s-new-imperial-era-2plj>

由于日本明仁天皇即将退位，日本政府于 4 月 1 日宣布，下一任天皇将被称为天皇(令和)时代。

你可能会想，“等等，日本还没有采用公历制度呢！?"是的，日本在 100 多年前就这样做了，然而，日本使用“西方”日历和独特的日本帝国年，这是基于公元前 660 年神武天皇建立日本的传说。

无论如何，我想在我博客中谈论的不是日本历史，而是 JavaScript `Intl.DateTimeFormat`对象，它支持特定语言和地区的日期和时间格式。

ECMAScript 国际化 API 最初于 2010 年推出，目前是第 6 版，用于帮助在 Javascript 中本地化日期、数字和货币的输出。而这一点现在已经得到了各大浏览器的很好支持。(见[我能用](https://caniuse.com/#search=Intl)吗)

`Intl`对象的属性之一是 [`DateTimeFormat`](https://tc39.github.io/ecma402/#datetimeformat-objects) ，它启用语言和地区特定的日期和时间格式。

所以没错，用`Intl.DateTimeFormat`对象，可以自动打印出日本当地的日期和时间与日本帝国时代！

## 使用 DateTimeFormat

在没有指定地区/语言的情况下，`DateTimeFormat`使用默认地区和默认时区，所以当我在美国的机器上:

```
new Intl.DateTimeFormat().format(Date.now()) 
```

Enter fullscreen mode Exit fullscreen mode

以美国英语返回今天的日期:

```
"4/2/2019" 
```

Enter fullscreen mode Exit fullscreen mode

### 指定语言环境

您可以通过使用地区标识符(语言代码和国家/地区代码)作为`locales`参数来获得本地化的日期和时间，例如:

```
new Intl.DateTimeFormat('ru-RU').format(Date.now())

// "02.04.2019" 
```

Enter fullscreen mode Exit fullscreen mode

而日本是:

```
new Intl.DateTimeFormat('ja-JP').format(Date.now())

// "2019/4/2" 
```

Enter fullscreen mode Exit fullscreen mode

好的，但是你仍然可以在这里看到公历年。那么如何才能让它与帝国年完全本土化呢？

嗯，地区标识符`ja-JP`是不够的，您需要添加一个扩展名`-u-ca-japanese`。我不知道它到底是什么意思，但我能说的是`ja-JP-u-ca-japanese`是一个笨拙复杂的本地代码。

> 编辑:谢谢， [Jean-Christophe Helary](https://twitter.com/brandelune) 让我知道标识符中的`-u`表示扩展，`-ca`表示定义了一种日历，在本例中是`-japanese`日历。
> 有关语言环境数据的更多信息，请参见: [Unicode 技术标准#35](http://www.unicode.org/reports/tr35/#Locale_Extension_Key_and_Type_Data)

```
new Intl.DateTimeFormat('ja-JP-u-ca-japanese').format(Date.now())

// "31/4/2" 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们知道这是 31 年。但是今年是哪一年呢？怎么能知道皇名呢！？

要获得整个交易，使用`options`参数，在本例中使用`era` :

```
new Intl.DateTimeFormat('ja-JP-u-ca-japanese', {era:'long'}).format(Date.now())

// ta-da! "平成 31 年 4 月 2 日" 
```

Enter fullscreen mode Exit fullscreen mode

哦耶，现在你明白了平成 31 年四月 2 日"!

让我们再试试泰国的语言环境:

```
new Intl.DateTimeFormat('th-TH-u-nu-thai', {era:'long'}).format(Date.now())

// "๒ ๔ พุทธศักราช ๒๕๖๒" 
```

Enter fullscreen mode Exit fullscreen mode

哦，我不知道该怎么读，但是现在是佛教日历中的 2562 年！

您可以在 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat)上找到选项的详细信息，并了解更多关于此主题的信息

另外，我很快写了一个叫做**it Is Reiwa(令和)还没？**上【https://reiwa-yet.glitch.me/】下[上](https://reiwa-yet.glitch.me/)。

这个应该说**不**到 2019 年 5 月 1 日，皇太子德仁成为新天皇，帝制年应该改成令和元年，*如果新时代在你的浏览器中实现了！！！*那么，让我们更新您的浏览器，看看吧！