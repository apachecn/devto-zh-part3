# 你如何在多台和/或新的开发者机器上管理你的点文件？

> 原文：<https://dev.to/lostintangent/how-do-you-manage-and-synchronize-your-dotfiles-across-multiple-machines-2n24>

嘿大家好！我很想知道你如何同步你的[点文件](http://dotfiles.github.io/)(例如。bash_profile，。gitconfig)和/或帮助简化设置新机器(例如崭新的 MacBook💻).

你是把它们存放在公共的还是私有的 GitHub repo 里？DropBox？别的地方？一旦克隆到目标机器，你安装点文件吗...

1.  手动(例如移动文件，运行一系列命令)
2.  通过运行与配置文件一起存储的自定义 shell 脚本
3.  使用自动化工具，例如 Ansible 或 Puppet
4.  使用“点文件管理器”，如 [GNU Stow](https://www.gnu.org/software/stow/stow.html) 、 [Dotbot](https://github.com/anishathalye/dotbot) 或[想家](https://github.com/technicalpickles/homesick)

如果你已经写了一篇关于你的设置的博文，请随意链接。否则，我很想听听什么对你有效，以及任何你可以分享的专业建议。这里有很多选择，所以我很有兴趣了解更多关于人们实际上在做什么🙌