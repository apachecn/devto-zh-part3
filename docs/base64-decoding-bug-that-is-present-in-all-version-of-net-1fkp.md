# Base64 解码错误，出现在所有版本的。网

> 原文：<https://dev.to/detunized/base64-decoding-bug-that-is-present-in-all-version-of-net-1fkp>

一个阳光明媚的早晨，我坐在笔记本电脑前重构一些 C#代码。一切都进行得非常顺利，这将是富有成效的一天。然后我在一个常量字符串上加了一个过多的等号，事情就变得一团糟。生产力消失了。周日去了和平重构。甚至太阳也决定躲在云后面。

在花了 30-40 分钟试图找出我做错了什么之后，我意识到那不是我。是微软。显然，我无意中发现了 Base64 解码函数中的一个古老错误。自从在中引入了`Convert.FromBase64String`之后，这个 bug 就一定存在了。2003 年净 1.1。哇哦。那是旧的。而且繁殖起来也不是很难。给你:

```
Convert.FromBase64String("abc=="); 
```

从技术上讲，这是一个非法的 Base64。合法版本是`"abc="`。注意只有一个填充字符`=`。Base64 编码用一个 ASCII 字符表示二进制输入的每 6 位。这意味着 Base64 编码字符串中的每 4 个字符代表 3 个字节。当编码数据不是 3 字节的倍数时，Base64 编码器会添加填充字符，使 Base64 成为 4 个字符的倍数。这使得`"abc="`成为一个正确填充的 Base64 字符串。再加一个`=`就无效了。

Base64 `"abc="`解码为两个字节`[105, 183]`。这是正确的。在末尾添加另一个填充字符不会真正改变编码值。就像在句末加了个空格。是的，它在那里，但是它没有改变句子的意思。酪 NET 不这么认为。`"abc=="`解码为`[109]`的一个字节。不仅变短了，这很奇怪，因为我们把输入变长了。它也变得不同了。第一个字节从 105 变成了 109。也没有抛出异常。再加一个`=`，你就会得到一个异常。太神奇了！

[代码](https://dotnetfiddle.net/JarxXF):

```
using System;

public class Program
{
    public static void Main()
    {
        DecodeAndPrint("abc=");
        DecodeAndPrint("abc==");
    }

    static void DecodeAndPrint(string base64)
    {
        Console.WriteLine(
            "'{0}' -> [{1}]",
            base64,
            string.Join(", ", Convert.FromBase64String(base64)));
    }
} 
```

输出:

```
'abc=' -> [105, 183]
'abc==' -> [109] 
```

而*真正*神奇的是，这么多年都没有人发现这一点。或者它被发现了，但是没有被修复。Base64 是通过网络进行信息交换的基础。到处都在用。然而。这么多年来，NET 一直带着一个有缺陷的 Base64 解码器。

一开始我不敢相信，开始调查。我在谷歌上搜索了一会儿，并没有发现什么。然后我在 [StackOverflow](https://stackoverflow.com/q/54852219/362938) 上发帖，但在那里也没什么运气。一旦我弄清楚发生了什么，我甚至不得不[回答](https://stackoverflow.com/a/54852796/362938)我自己的问题。在 GitHub 上搜索了一段时间后，我偶然发现了一个[补丁](https://github.com/dotnet/corefx/pull/30814)。2018 年 7 月做的网芯。所以最新的。NET Core 版本正确地处理了这个问题，并抛出了一个异常:

```
Unhandled Exception: System.FormatException: The input is not a valid Base-64 string as it contains a non-base 64 character, more than two padding characters, or an illegal character among the padding characters.
   at System.Convert.FromBase64CharPtr(Char* inputPtr, Int32 inputLength)
   at System.Convert.FromBase64String(String s)
   at Program.DecodeAndPrint(String base64) in ./base64/Program.cs:line 13
   at Program.Main() in ./base64/Program.cs:line 8 
```

他们花了大约 15 年的时间找到并解决这个问题。有意思的是，没有人真正尝试过具体修复。这发生在他们[重写代码以提高速度的时候](https://github.com/dotnet/corefx/pull/30814#issue-199014852):

> 转换。FromBase64()有一个微妙的错误，字符串末尾的第二个非法填充字符
> 通过将第五个字符丢弃到最后一个字符
> 导致
> 解码“成功”。
> 
> 我们在优化。NetCore 2.1。将测试添加到文档 bug 和
> 中，确保我们不会退步。

所以这是固定的。网芯 2.2。但在目前最新版本的中还是坏了。NET 框架 4.7.2。而且看起来在[单声道里也坏了](https://repl.it/repls/FriendlyOnlyVerification)。

的解决方法。NET 4.7.2 将会用类似这样的内容来重新填充不正确的字符串:

```
// This only works for base64 without spaces or linebreaks.
string Repad(string base64)
{
    var l = base64.Length;
    return l % 4 == 1 && base64[l - 1] == '='
        ? base64.Substring(0, l - 1)
        : base64;
} 
```

*原载于[detunized.net](https://detunized.net/posts/2019-03-06-base64-decoding-bug-that-is-present-in-all-version-of-.net/)T3】*