# 转向原生 vim 插件管理

> 原文：<https://dev.to/deusmxsabrina/moving-to-native-vim-plugin-management-1hpa>

我想从一个免责声明开始:
使用插件管理器是 100%容易的，我选择这样改变我的工作流程，因为我有一些我想从中获得的知识:

*   更少的配置/更少的依赖插件(通过引入安装插件的摩擦力，这会让我重新考虑我是否真的需要安装它。)
*   通过移除我的插件管理器在幕后做的魔术，更好地了解 vim 运行时。
*   更好的 git 知识(我以前从未使用过子模块！)

如果你正在寻找一个好的 vim 插件管理器，它不会试图为你做太多事情，我强烈推荐 [vim-plug](https://github.com/junegunn/vim-plug) ，在使用它的近 2 年中，它从未让我失望过。(如果有的话，这篇文章是关于我让它失望的。😞

说完了，让我们来谈谈这个过程吧！

我如何移动我的插件是按照以下步骤进行的:

*   从我的中删除 Plug 命令。使用`:PlugClean`命令卸载 vimrc
*   基本上是将从[这个要点](https://gist.github.com/manasthakur/d4dc9a610884c60d944a4dd97f0b3560)中获得的知识应用到我自己的设置中。
*   我已经用一个简单的 github repo 对我的点文件[进行了版本控制，所以我直接跳到了](https://harfangk.github.io/2016/09/18/manage-dotfiles-with-a-git-bare-repository.html)[安装插件](https://gist.github.com/manasthakur/d4dc9a610884c60d944a4dd97f0b3560#installing-plugins)，运行

`dotfiles submodule init`

首先初始化我的 dotfiles repo 以使用子模块，然后对于我想要安装的每个插件:

`dotfiles submodule add https://github.com/some-username/some-plugin.git pack/plugins/start/some-plugin`

除此之外，要点中的说明适用。为了有助于长期维持，我计划给一些更新和删除插件的命令加别名。我已经给更新命令起了别名:
`alias update_plugins=‘dotfiles submodule foreach git pull origin master’`

当这些更改被上传到我的另一台笔记本电脑上时，一切都很顺利！我提取了 repo，然后需要初始化该 repo 的子模块:
`dotfiles submodule update --init --recursive`

到目前为止，我喜欢这个新设置(请记住，我还没有删除插件)，除了最初的迁移过程需要一段时间之外，它是一个无缝的调整，我喜欢将一台计算机的一些插件克隆到`.vim/pack/plugins/start/`中的粒度，我不想在没有明确忽略它的情况下实际提交到我的点文件 repo。

我想做的下一步是在这个设置上玩可选的插件加载！这是我现在使用经理最大的缺点。总的来说，这是一个漫长但值得的过程，因为我从中获得了我想要的知识。

下面是我参考的要点，它们是很好的资源，我强烈推荐它们:

*   [在 Vim 中管理插件:基础知识](https://gist.github.com/manasthakur/ab4cf8d32a28ea38271ac0d07373bb53)
*   [使用 git 子模块对 Vim 插件进行版本控制](https://gist.github.com/manasthakur/d4dc9a610884c60d944a4dd97f0b3560)

感谢 [Manas Thakur](https://github.com/manasthakur) 提供这些资源！