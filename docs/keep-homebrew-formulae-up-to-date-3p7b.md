# 保持自制配方最新

> 原文：<https://dev.to/stephencweiss/keep-homebrew-formulae-up-to-date-3p7b>

与清理我的 git 分支一样，今天我回忆起我可以用命令`brew upgrade`轻松地更新我的自制公式。

这启动了一个过程，可能需要一两分钟，取决于你上次升级的时间和你有多少公式。

升级过程将:

1.  需要的话更新家酿。
2.  添加新公式
3.  删除公式
4.  升级过时的软件包
5.  如果已经超过 30 天，运行`brew cleanup`

```
$ brew upgrade
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 2 taps (homebrew/core and homebrew/cask).
==> New Formulae
catch2 deno include-what-you-use pprint
clzip erlang@21 lizard spice-gtk
csvq heatshrink molten-vk spirv-tools
==> Updated Formulae
harfbuzz ✔ fluxctl mailutils raylib
imageoptim-cli ✔ fn mame re2
node ✔ fobis mat2 rebar3 
```

虽然这很容易做到每隔几周做一次(我已经在我的事情清单上设置了一个提醒，就在磨我的厨房刀具旁边)，但我的下一个挑战是使用“慢性工作”来安排它——这是我以前从未做过的事情！

同样值得注意的是，如果您想升级某个特定的公式，您可以使用`brew upgrade <formula>`(通过检查版本(`brew <formula> -v`)，您可能会注意到它已经过时了)。