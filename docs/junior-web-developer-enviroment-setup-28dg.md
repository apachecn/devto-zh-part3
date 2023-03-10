# 初级 Web 开发人员环境设置

> 原文：<https://dev.to/robmatyszewski/junior-web-developer-enviroment-setup-28dg>

这篇文章将向你详细介绍如何将你的电脑设置成一个网站开发者。它是为 MacOS 创建的，因为我主要在 MacOS 上工作，然而，你可以很容易地为 Windows 交换那些指令。建立 web 开发环境是今天成为 web 开发人员的第一个挑战。不要放弃，马上尝试！

有几种方法可以设置您的开发环境。你可以在一个 IDE 中拥有所有的东西，或者你可以使用许多轻量级的工具来结合它们的用途。下面的简短清单反映了我最喜欢的轻量级组合工具。在某些情况下，我更喜欢使用轻量级工具，例如，对于 react，我使用一个 IDE。关注我以获得更多信息，在下一篇文章中，我将介绍 React 开发环境的基础知识。

# 议程:

*   公司自产自用
*   Git & GitHub
*   作为编辑的崇高
*   节点和 NPM
*   iTerm2

# 自制

你将需要使用 Mac 终端应用程序来安装家酿。“终端”应用程序位于“应用程序”文件夹的“实用工具”文件夹中。

家酿是一个 MacOS 的软件包管理器。不必从网上下载每一个应用程序，Homebrew 使你能够从命令行安装和控制应用程序。对于初学者来说，这是一个很好的习惯命令行的工具。作为开发人员，您应该对命令行感到放松，因此在那里尽可能多地执行任务是有意义的。此外，Homebrew 使您能够只用一个工具管理所有应用程序。你可以在他们的网站上找到所有家酿啤酒的必要说明。否则，只需在命令行(终端)中键入以下命令即可安装 Homebrew。

```
ruby -e "$(curl -fsSL
https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

要验证安装是否成功，请键入:

```
brew --version 
```

之后，家酿应该为你准备好了。当您键入 brew 时，应该可以在命令行上访问它。每当您使用 Homebrew 安装另一个工具时，请在命令行中键入 brew update，用所有最新版本的软件包更新 Homebrew 存储库。之后，您可以使用 brew install 安装所需的工具，或使用 brew upgrade 升级该工具。

# Git 和 GitHub

Git 将是你的分布式版本控制系统。它管理您机器上的本地项目和 GitHub 和 GitLab 等平台上的远程项目。迟早，你会需要它来与其他开发者合作。Git 应该已经和 Homebrew 一起安装了。键入 git - version 来验证它是否存在。否则，您可以第一次使用 Homebrew，在命令行中使用以下命令进行安装:

```
brew install git 
```

要验证安装是否成功，请键入:

```
git --version 
```

接下来，您必须创建一个 GitHub 帐户，并从终端连接它。GitHub 是一个基于 web 的托管服务，使用 Git 进行版本控制。它提供了 Git 的所有分布式版本控制和源代码管理(SCM)功能，并添加了它的其他特性。它为每个项目提供了访问控制和几个协作特性，比如 bug 跟踪、特性请求、任务管理和 wikis。要了解更多如何通过终端设置 GitHub，请点击此[链接](https://help.github.com/en/articles/set-up-git)。

# 崇高如编辑

Sublime 是开发者的顶级编辑器之一。这是每个人都有自己观点的部分。你可以使用任何你选择的编辑器或 IDE，比如 Visual Studio Code、Atom 和 VIM。

不幸的是，Sublime 不能通过自制软件直接获得。但是你可以用木桶来安装它。木桶是另一个软件包管理器。例如，Chrome 也可以安装在你的机器上。只需在终端中键入选定的命令:

```
brew install caskroom/cask/brew-cask
brew tap caskroom/versions
brew cask install sublime-text 
```

要验证安装是否成功，请键入:
subl -v

# 节点和 NPM

如果你使用 JavaScript，node 和 npm 是必须的。节点包管理器用于在命令行上将库/框架(节点包)安装到项目中。

```
brew install node 
```

要验证安装是否成功，请键入:
node -v
npm -v

# iTerm2

MacOS 和其他 OS 已经自带了命令行终端工具。因此，另一个命令行工具是可选的，但是由于各种优势，您可能希望安装 iTerm2。它带有强大的集成，美丽的主题和其他有用的实用工具，将丰富您的日常工具集。自制木桶帮助你安装它，只需输入:

```
brew cask install iterm2 
```

# 
  
汇总

这些都是建立开发环境所需的基本工具。Homebrew 是用来在命令行上为你安装所有工具的。Cask 用于安装图形用户界面应用程序，如 Chrome、iTerm2 和 Sublime。在后面的阶段，你可以选择任何你想要的代码编辑器，因为你会花大部分时间在里面。作为一名 JavaScript 开发人员，没有办法绕过 node 和 npm，因此您应该将它们与 nvm 一起安装，以管理多个节点版本。Git 和 GitHub 应该用来处理你的项目。

## 你会给我的清单添加什么吗？