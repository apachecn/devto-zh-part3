# 午夜时分

> 原文：<https://dev.to/emgodev/middle-of-the-night-4el1>

所以我在睡觉的时候醒了，因为我厌倦了睡觉(是的厌倦了疲倦)，我真的想做点什么。我决定跳上我的电脑，希望这个一直困扰我的 bug 不要再继续下去了...).计算机从睡眠/暂停/休眠状态恢复，屏幕黑屏，鼠标光标消失。重启电脑似乎是唯一的解决办法，我想这很好，因为由于这个问题，我已经养成了保存所有东西的习惯。在放弃寻找修复方法后，我真的厌倦了这样的技术错误，我有点相信这是 Ubuntus 的错。知道我应该扩展我的视野到其他发行版，我开始研究它们。

在我开始搜索后不久，我的选择主要是 arch、Mint 或一些不知名的东西，比如 Debian。我在想，如果我重新开始，我想挑战自己更多的移动接近那些开发者的根源，毛绒轻量级系统是一个加号；好像我处理的技术问题还不够多似的。我去“/media”找到我的驱动器，想知道我的资料可以备份到哪里，但我想知道我还剩多少空间？我已经养成了避免使用任何图形显示的习惯，但是我从来不记得那个命令(它是`df`)。

在发现我有多少空间后，我想如果我已经完成了常用命令列表该有多好。一两个月前，我已经花了很多时间写了一些可爱的化名。我甚至开始将我自己的配置文件保存在一个独立的文件夹中，我一直在电脑之间切换。这在我与 EMACS 的键绑定中也派上了用场。我刚刚把*主*配置复制到它所属的任何目录来更新它们。

总之，长话短说，我不觉得一个文件是解决办法，我的意思是我已经半心半意地开始这样的文件，而我一直在学习，他们从来没有去任何地方。一个半成品的 excel 电子表格比它需要的更复杂，一个半成品的纯文本文档没有正确的标记/格式。我想要我自己的手册页，或者至少是一个命令；我知道如何编写 bash 脚本，但是我可以在我正在使用的终端中使用它。那就方便了！

我在 linuxcommand.org 的[上找到了一个很棒的简短教程，它解释了命令是如何成为 bash 脚本的，它们是如何被保存在`$PATH`超级全局类标识符中的特定目录中的计算机读取的。它包含一个由冒号分隔的搜索目录字符串。标准实践似乎表明用户将个人脚本放在`/home/user/bin`下，所以我这样做了，并且我的第一个命令工作了！](//linuxcommand.org)

现在我迷上了，其他文章讨论使用函数而不是别名，当然这两者都可以在 bash 脚本命令中使用。然后它使用了生成一个 HTML 页面的例子——我更多地使用了 EMACS Dired 和缩写，但是仍然从一个终端函数生成了一个完整的 HTML 样板目录！帮我报名！-对于一个应用程序，在这里使用一个*脚本/文档*，它与 I/O 重定向有关，它与`cat`命令一起使用，而不是与`echo`一起使用，看起来我们可以通过管道将它发送到某个 make file 命令；下一篇文章看起来要讨论变量了，这太好了！

刚才我想，这有点疯狂，我应该写下来。凌晨 5 点 20 分完成这个，这是我周六半夜睡到一半的时候做的事情。感谢阅读！