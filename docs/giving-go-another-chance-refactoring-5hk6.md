# 再给一次机会:重构

> 原文：<https://dev.to/detunized/giving-go-another-chance-refactoring-5hk6>

在这个会话中，我想添加另一个命令。我想我必须找出一些公共代码，并把它放在其他地方。所以我开始复制一个文件并尝试构建它。然后我有了另一个去 WTF 的时刻。显然，我不能在不同的文件中有相同名称的变量或函数。我没想到会碰到。我想这有点像 C 语言，只是会有一个链接错误。但是到目前为止，我为这门语言建立的任何直觉都在这一点上消失了。

我开始四处搜索，发现一个包中的所有文件本质上都可以被视为一个文件。对于文件来说，没有变量或函数这样的东西。但是可以有很多。任何以大写字母开头的内容都是公开的，并从包(而不是文件)中导出。以小写字母开头的标识符是私有的或隐藏的，不向包的用户公开。

我通常一直在重构。我四处移动代码。我一直在添加、删除和重新添加功能。我不断地给所有东西重新命名，直到我对结果满意为止。一般来说，Go 不会阻止这种开发方式，但是它不允许有任何未使用的变量或导入，这使得开发变得有点困难。这些导入很容易通过`goimports`或`goreturns`格式化工具来修复。必须手动移除局部变量。这让我的进度慢了不少。我宁愿让编译器给我显示一个警告。我会让东西先工作，然后我会清理警告。Go 不原谅也不忘记。

作为这次会议的结果，我已经将所有共享代码提取出来并藏在一个单独的文件中。我现在有两个命令:`in`和`out`。东西收拾好了，准备做成一个很棒的 app。

下次我开始写更严肃的东西时。不要再说婴儿用品了，毕竟我是一名高级开发人员。

* * *

实现这一目标的谷歌搜索:

*   转到私人功能
*   转到文件的本地函数

* * *

耗时:35 分钟
总耗时:7:10 小时