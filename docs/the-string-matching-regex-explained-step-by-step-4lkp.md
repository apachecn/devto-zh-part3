# 逐步解释的字符串匹配正则表达式

> 原文：<https://dev.to/xowap/the-string-matching-regex-explained-step-by-step-4lkp>

我很确定，如果堆栈溢出调查问开发人员他们最害怕的是什么，第一名会是正则表达式。虽然有些简单的问题做起来并不复杂，但有一个特别的问题是我十多年来一直在回避的，直到我最终试图理解它...匹配一个字符串文字！

字符串文字是向您的编程语言传达您希望作为字符串对象加载的字符串的方式。基本上:

```
const foo = "bar"; 
```

Enter fullscreen mode Exit fullscreen mode

这里的字符串文字是`"bar"`。

虽然通常是由语言来处理，但是可能有几个原因需要您自己来解析这个字符串，最有可能的是当您用一种语言分析另一种语言时。我上一次这样做是在编写工具来修补 WordPress SQL 转储时。

这很容易做到，除非你需要处理`"bar \" baz"`或`"bar\xa0!"`。在本文中，我们将介绍解析字符串不同部分的方法。

> — **注** —
> 
> 本文是在考虑 JSON-ish 字符串的基础上编写的，但是将探讨各种解析问题和解决方案。当然，这不是一个权威的指南，存在许多不同的选择，这只是其中的一部分。
> 
> 正则表达式语法来自 JavaScript。
> 
> 本文中的所有正则表达式都链接到 Regex101，以帮助您解码和测试表达式。不要犹豫，点击链接！

## 最简单的情况

现在，我们只是尝试解析一个简单的字符串，没有任何花哨的东西。我们将考虑下面的测试用例:

```
"bar"
const foo = "bar";
foo("bar", "baz"); 
```

Enter fullscreen mode Exit fullscreen mode

我首先想写的是 [`/".*"/`](https://regex101.com/r/z0XTpj/1) 。

[![](img/81ab92a6f0d562eaf378ba3594ef5c7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BqIl5Pp1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kwal5wcgg7u7t1dmeapg.png)

如您所见，`.`也与`"`匹配，导致匹配一次完成`"bar", "baz"`。为了避免这种情况，你可以简单地使用一个`*?`(懒惰)量词，而不仅仅是`*`。让我们试试 [`/".*?"/`](https://regex101.com/r/gpnMSD/1)

[![](img/ce9e597083487c8d235ecf6f9c038063.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DD_Fc13q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7676xs9sn1vllvoty9zl.png)

好多了！但还不够好，原因你将在下一部分了解。想想我们的真实意图:因为我们没有定义任何转义机制，所以字符串可以包含除了标志字符串结束的 `"`之外的任何字符*。*

*任何字符*都是点`.`，但是你也可以使用`[^]`语法制作黑名单。在这种情况下，`[^"]`将匹配除了`"`以外的任何字符。因此[的最终表达式](https://regex101.com/r/tk7J0U/1)将是:

```
/"[^"]*"/ 
```

Enter fullscreen mode Exit fullscreen mode

你仍然得到这个:

[![](img/ce9e597083487c8d235ecf6f9c038063.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DD_Fc13q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7676xs9sn1vllvoty9zl.png)

## 逃报

有两种方法可以避开引号。要么你加倍它`"say ""foo"""`，要么你反斜杠它`"say \"foo\""`。它因语言而异。大多数语言选择反斜杠，但是你会发现所有的东西都在那里。我们两个都要研究。

### 双精度

处理引号转义最简单的方法可能是将它们加倍。那是因为很容易去想。在字符串中，您将允许:

*   非引号— `[^"]`
*   两个相邻的引号— `""`

当把它们放在一起，你就得到 [`/"([^"]|"")*"/`](https://regex101.com/r/pxzlIc/1) 。

[![](img/d63f583c4dc9dc0478eb54e5f212ed3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mv5YgPKh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7u95qga3w18vz8u8h2de.png)

令人惊讶的是，第一次尝试就成功了！

### 反斜杠

让我们试着在测试短语上运行我们之前的简单表达式。

[![](img/d2d384e628a36c7c85e1af94dd4c520c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RTvdSstR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r8zoz94ukt3xn7hyaxyj.png)

如你所见，它并不关心反斜杠，它检测两个不同的字符串。

让我们考虑一下我们希望在两个引号之间允许什么样的内容:

*   “*不是*语录”，至少不是光秃秃的。就像上面一样。`[^"]`
*   转义引号，所以`\"`。如果把它翻译成 regex 语法，就会得到`\\"`。

这通常是通过将不同的选项放在一个匹配组中来完成的。让我们试试 [`"([^"]|\\")*"`](https://regex101.com/r/esV34C/1) 。

[![](img/42c9dfaa5b792e72a55e5ab1d5de337f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SpvhE66R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8aw0q4d18sqergbe1np.png)

哦，不，它坏了。因为没错，反斜杠确实符合`[^"]`规范。所以我们实际上需要反过来写: [`/"(\\"|[^"])*"/`](https://regex101.com/r/mbPiac/1)

[![](img/3aff3e9fe4baaa2b43a99a5de4f56b51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---QnrhD4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s15v36g5kxo4pens8tb7.png)

现在我们有进展了。但是依赖顺序有点琐碎，也不太安全。让我们修正一下我们先前说的:

*   既没有引号，也没有反斜杠— `[^"\\]`
*   转义引号— `\\"`
*   反斜杠后跟除引号之外的任何内容— `\\[^"]`

让我们试试 [`/"([^"\\]|\\"|\\[^"])*"/`](https://regex101.com/r/Ykbf1L/2)

[![](img/554c4777ccd0fee5f1704e7e2f5f4319.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZWydujwd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4efk9tcs5di4re2xbrhg.png)

这很好！但是等等，这种表达是不是有点蠢？我们来分解一下:

*   `[^"\\]`|`\\"`|`\\[^"]`——这三个中的任何一个
*   `[^"\\]` | `\\("|[^"])` —将`"`和`[^"]`组合在一起
*   `[^"\\]` | `\\.` —由于`"`和`[^"]`将一起匹配“引用或非引用”，这意味着它们将匹配任何字符，因此它们可以被替换为`.`

我们的[最终表达](https://regex101.com/r/ln80b2/1)然后是`"([^"\\]|\\.)*"/`。

我们现在有了一个全功能的字符串提取正则表达式！

## 里面的语法

我们上面看到的代码保证解析一个字符串，即使其中有一些转义符`"`。然而，这并不能保证字符串中的内的*有意义。大多数字符串解析器会寻找它们识别的模式，而不去碰其他的模式。假设我们只处理常规的`\n`、`\r`或`\t`:*

1 —文字

> `"say \"foo\"\nsay \"bar\!\""`

2-使用上面的正则表达式取消引用

> `say \"foo\"\nsay \"bar\!\"`

3-替换转义字符

> `say "foo"`
> T1】

请注意`\!`如何保持`\!`。这就是 Python 的行为。如果你在 JavaScript 中这么做，它会把它替换成`!`。这取决于定义:

*   你可以说`\X`是`X`除非找到一个模式(JavaScript 就是这么做的)
*   或者`\X`不匹配任何模式，所以它保持原样(Python 的逻辑)
*   Or `\X`不匹配任何模式，所以这是一个语法错误(例如 JSON 中发生的情况)

### JSON 转义字符

所有语言都有自己的一套转义字符，有些很通用，如`\0`或`\n`，而有些只在某些情况下存在，甚至在不同的语言中有不同的含义。因为我们需要选择一方，所以让我们担心一下 [JSON 提供了什么](https://www.crockford.com/mckeeman.html)。

#### 单字符

许多转义字符模式实际上只是一个字符。就像`\n`一样，它只是到新行的映射。对于那些你只需要存储映射和检测它。匹配它们的正则表达式是 [`/\\(["\\\/bnrt])/`](https://regex101.com/r/QqzWNH/1) ，它允许您查看哪个字符被第一组捕获。

[![](img/6f0b323152be249817edd297bfe7c0d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7DS6wkIo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/81m43dd0v7xrdu4jn062.png)

正如您可能知道的，JavaScript[string . replace()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)函数允许将一个函数作为替换。它将接收匹配的组作为参数，其返回值将被用作替换。

我们要用它来创建一个函数，来替换这些字符。

```
function subSingle(string) {
  const re = /\\(["\\\/bnrt])/g;
  const map = {
    '"': '"',
    '\\': '\\',
    '/': '/',
    b: '\b',
    n: '\n',
    r: '\r',
    t: '\t',
  };
  return string.replace(re, (_, char) => map[char]);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### Unicode

JSON 还允许您键入转义的 unicode 字符，比如`\uf00f`。是一个`\u`后跟 4 个十六进制字符。总之 [`/\\u([a-fA-F0-9]{4})/`](https://regex101.com/r/1Hzj9V/1) 。

[![](img/9b9ee15b0a85591b0e69aad0358c616b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JezZOfWC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8l7v9eeo49fv9agz401.png)

虽然我们可以将这个字符串编码成 UTF-8 或 UTF-16 格式，然后从您正在使用的语言中转换成一个内部字符串对象，但是标准库中可能已经有一个函数可以做到这一点。在 JavaScript 中是 [String.fromCodePoint()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/fromCodePoint) ，在 Python 中是内置的 [chr()](https://docs.python.org/3/library/functions.html#chr) ，在 PHP 中是相对简单的。

同样，我们将在正则表达式中使用一个替换函数来实现这一点。

```
function subUnicode(string) {
  const re = /\\u([a-fA-F0-9]{4})/g;
  return string.replace(re, (_, hexCodePoint) => (
    String.fromCodePoint(parseInt(hexCodePoint, 16))
  ));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 全 JSON 字符串解析器

我们已经看到了解析字符串及其组成部分的不同方法，现在让我们将其应用于解析 JSON 字符串文字。

代码将分为两部分:

1.  在输入文本中查找不同的字符串
2.  替换提取的字符串中的引用字符

这将是一个简单的 Vue 应用程序，它接受来自`textarea`的输入，并输出它在输入中可以找到的所有字符串的列表。

### 找到琴弦

JSON 字符串的一个重要变化是它们不允许控制字符，所以基本上禁止使用`\x00-\x19`范围。这包括换行符(`\n`)等等。让我们把找弦的表达稍微扭曲一下，变成 [`/"(([^\0-\x19"\\]|\\[^\0-\x19])*)"/`](https://regex101.com/r/m6JjKa/1) 。它匹配:

*   非控制字符(`\0-\x19`)、非引号(`"`)和非反斜杠(`\\`)
*   或者反斜杠(`\\`)后跟非控制字符(`\0-\x19`)

让我们把它转换成 JavaScript 代码:

```
function findStrings(string) {
    const re = /"(([^\0-\x19"\\]|\\[^\0-\x19])*)"/g;
    const out = [];

    while ((m = re.exec(string)) !== null) {
        if (m.index === re.lastIndex) {
            re.lastIndex++;
        }

        out.push(m[1]);
    }

    return out;
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数将简单地提取所有字符串，并将它们放入一个数组中。

### 替换字符

现在是替换转义字符的时候了。之前我们已经用了两个函数来实现，但是这很危险。举例来说:

*   字符串是`"\\ud83e\\udd37"`
*   不加引号就变成了`\\ud83e\\udd37`
*   替换单个字符`\ud83e\udd37`
*   替换 Unicode `🤷`，而它应该是`\ud83e\udd37`

因此，Unicode 和单个字符必须同时替换。为了做到这一点，我们简单地将前面的两个表达式合并成 [`/\\(["\\\/bnrt]|u([a-fA-F0-9]{4}))/`](https://regex101.com/r/rfZ0OF/2) 。

它匹配一个反斜杠`\\`，后跟:

*   `\/bnrt`人物之一
*   类似于`\uf00f`的 Unicode 码位

我们也合并一下 JS 代码:

```
function subEscapes(string) {
    const re = /\\(["\\\/bnrt]|u([a-fA-F0-9]{4}))/g;
    const map = {
        '"': '"',
        '\\': '\\',
        '/': '/',
        b: '\b',
        n: '\n',
        r: '\r',
        t: '\t',
    };

    return string.replace(re, (_, char, hexCodePoint) => {
        if (char[0] === 'u') {
            return String.fromCodePoint(parseInt(hexCodePoint, 16));
        } else {
            return map[char];
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我们是如何选择不验证转义字符的。事实上，如上所述，如果你用 JSON 写`\!`,你会得到一个语法错误。然而在这里你只会得到`\!`。这是为了简化代码。这段代码将正确解析所有有效的 JSON 字符串，但是无效的 JSON 字符串仍然会被正确解析。

### 把这一切联系在一起

现在剩下要做的就是创建一些代码来解析输入并将其转换成输出。我们可以通过 Vue 应用轻松做到这一点。

```
const app = new Vue({
    el: '#app',
    data() {
        return {
            input: `const foo = "say \\"foo\\""`,
        };
    },
    computed: {
        output() {
            return findStrings(this.input).map(subEscapes);
        },
    },
}); 
```

Enter fullscreen mode Exit fullscreen mode

观看它的实际应用:

[https://jsfiddle.net/xowap/mfc3rxq9/62//embedded/result,js//dark](https://jsfiddle.net/xowap/mfc3rxq9/62//embedded/result,js//dark)

## 结论

从最简单的字符串匹配正则表达式开始，我们已经将它发展成为一个成熟的 JSON 字符串解析器。虽然过程中有许多陷阱，但最终的代码相当简单和短小(大约 40 行)。这里应用的方法允许构建一个字符串解析器，但也可以应用于构建任何类型的基于正则表达式的代码，我希望您能够将它应用到您的项目中！