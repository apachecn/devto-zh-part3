# bash 脚本的新手？这里有一些建议

> 原文：<https://dev.to/pencillr/newbie-at-bash-scripting-heres-some-advice-j1a>

当我开始使用 bash 编写脚本时，我首先无法理解它。我相对年轻，我困惑背后的原因可能是。当时我来自 python，感觉好像每次我想做什么事情，我都需要一些特殊的符号或咒语才能实现。Bash 是强大的，它是超级宽容的，正因为如此，很容易用它做一些愚蠢的事情。

##### 需要澄清的是，这篇博客更多的是关于 bash 中的良好约定，而不是[它的语法](https://devhints.io/bash)或者[如何用它做基本的事情](https://zwischenzugs.com/2018/01/06/ten-things-i-wish-id-known-about-bash/)。

## 这是如何开始一个 bash 脚本的

我想谈谈 set builtin，但是我意识到这不是开始 bash 脚本的方式。这是怎么回事:

```
#!/usr/bin/env bash 
```

Enter fullscreen mode Exit fullscreen mode

这叫做*射棒*。通过将这作为第一行，您将确保使用默认的 bash 运行，这是它在您的`$PATH`环境变量中的第一个引用。这是一条捷径，但却是一个可以遵循的好习惯。(除非安全性是一个问题，并且您担心有人会篡改您的`$PATH`环境变量。)

## 现在真的。这就是启动 bash 脚本的方式

```
#!/usr/bin/env bash
set -euo pipefail 
```

Enter fullscreen mode Exit fullscreen mode

set builtin 在我们的 bash 工具包中是一个非常复杂和有用的元素。为了说明我的观点，我会把它简化一点，但是这里有一个解释: **set** 允许你配置【bash 在某些关键场景中如何表现。
**让我们简单地过一遍:**

*   选项将导致 bash 脚本在命令失败时立即退出。这通常是对默认行为的巨大改进，在默认行为中，脚本只是忽略失败的命令并继续下一行。
*   使 bash shell 将未设置的变量视为错误并立即退出。
*   `set -o pipefail`将管道的退出代码设置为最右边命令的代码，以非零状态退出，如果管道的所有命令都成功退出，则设置为零。实际上，如果没有这个选项，`set -e`本身是没有意义的，因为如果一个命令管道在中间某个地方出现错误，它不会退出脚本。

## 好吧，我做了设定的事情，这让我很难受。怎么处理？

很肯定你会因为这样或那样的原因而陷入困境。例如，您可能决定可选地提供一些参数，然后`set -u`会踢您的屁股。这时候你做:

```
if [[ -z "${oxygen:-}" ]]; then echo "Huston we have a problem!"
fi 
```

Enter fullscreen mode Exit fullscreen mode

该条件检查给定参数`oxygen`是否未设置(或空字符串)。重点在`:-`表达式上。没有这个，由于`set -u`，脚本将失败，因为它将不能做参数扩展。使用`:-`表达式将保持不变，我们将忽略这个错误。顺便说一下，这就是添加默认参数的方式。如果您的参数未设置，则`:-`后面的内容将被用作默认值。像这样:`"${oxigen:-nitrogen}"`

另一个常见的问题是，当您预期某件事会失败或者您想要明确地处理它时。自然地，`set -eo pipefail`将退出你的脚本，如果任何东西敢以非零退出状态退出。为了处理这个问题，你需要*# pipe true*:

```
echo 'Here comes the rough part'
my_fate=$("${dark_cellar}"/russian_roulette.py --load "${bullet}" || :)
echo 'Keep going, whatever' 
```

Enter fullscreen mode Exit fullscreen mode

表达式`|| :`将抹去你的错误，让你的脚本运行。

## 我会做事。但我做得对吗？

正如我们所说的，在 bash 中有很多方法可以做一些事情。幸运的是，在线社区很棒，很容易找到你想要的[一行程序](http://www.bashoneliners.com/)。但这是一个安全的解决方案吗？我学到的一件事是:总有*你没有想到的场景。
当你有一个 Linux 专家朋友并能发现你所有的错误，这很好。但是如果你没有，你还是可以有一个好的*静态分析器*！*

#### 遇见[脱壳](https://www.shellcheck.net/)。

如果使用 bash，也要使用 shellcheck。它警告您可能会导致不可预见的后果的错误和常见的不良做法。很容易设置，apt 就是这样:

```
sudo apt update
sudo apt-get install shellcheck 
```

Enter fullscreen mode Exit fullscreen mode

您可以简单地以`shellcheck myscript.sh`的身份为您的任何脚本运行它，它会让您一目了然！

## 允许并不意味着你不应该模块化

如果你最终在某个公司做 DevOps 的工作，你可能会注意到每个 bash 脚本基本上都是一个有序的操作列表。不像 python 脚本那样，社区实践推动你创建函数，并确保保持[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)。但是谁说 bash 里不能这样呢？参见:

```
function get_to_the_choppa() {
    local mate=$1
    echo "${mate}! Get to the choppa!"
}

get_to_the_choppa "Jack" 
```

Enter fullscreen mode Exit fullscreen mode

通常情况下，如果您不知道 bash 的行为方式，它会让您不知所措，所以让我们看看最常见的情况:

*   使用`local`变量。函数中声明的所有内容在函数范围之外都是可用的。为了避免这种情况，您必须显式地将变量声明为局部变量。
*   选择一个**约定**并保持一致。可以用几种方法来声明函数。一定要坚持一个。

```
function take_pills() {
    echo "Get better"
}
function dink_tea {
    echo "Get better"
}
rest_in_bed() {
    echo "Get better"
} 
```

Enter fullscreen mode Exit fullscreen mode

*   可以用`echo`返回。Bash 有一个`return`语句，但是您只能用它来指定函数的退出状态。为了*从你的函数中获取*值，解决方案可以是在函数中设置全局变量而不使用 local 关键字，但是最好是显式的！你可以捕捉你的函数的回声。

```
function interrogate() {
    echo "It was Hank!"
}
who_to_blame=$(interrogate) 
```

Enter fullscreen mode Exit fullscreen mode

## 了解 Linux

如果您是 bash 新手，那么可以肯定 Linux 对您来说也是陌生的。我不是解释 Linux 所有基础知识的最佳人选，但是我可以分享一个令人惊奇的免费资源，我在开始的时候也用过。
如果还有一些话题让你困惑，你可以在 [linuxjourney](https://linuxjourney.com/) 阅读所有相关内容。它对每一个基本的和高级的概念都有简单易懂的描述，它有一个非常干净的用户界面，让人赏心悦目。这个页面如此简单而令人惊叹，它可以教一只猴子使用 Linux。**来看看吧！**

## 最后，两点建议

听我的两个建议，当事情不像预期的那样起作用时，应用它们。那时我发现这些步骤非常重要，所以我把它们写在便利贴上，贴在我的显示器底部。他们在这里:

#### 第一步:*再读一遍那个代码。*

有什么不对劲吗？不要惊慌。我告诉你会发生什么:一段时间会过去，你会发现你犯的错误。可能在你的鼻尖上。你可能认为你做的一切都很好，但你使用的一些组件是冲突的？转到第二步。

#### 第二步:*几乎总是你。*

您可能认为有些组件不兼容。或者您使用的版本不支持某些用例。但通常不是。通常是你搞砸了。回到第一步。