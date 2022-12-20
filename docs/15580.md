# 我的终端设置

> 原文：<https://dev.to/michaeldscherr/my-terminal-setup-l2k>

我想分享一下我目前的`terminal`设置。多年来，我已经能够优化我的工作流程。这使我能够:

*   更快地找到信息
*   减少我搜索命令的时间
*   总体而言，对`cli's`变得更加自在
*   看起来像个坏蛋

这些年来我的进化很有趣。一开始，我是那个使用所有可以想象的插件的开发者(为了`['terminal', 'IDE', 'etc']`)。然后我看了来自 MPJ 的关于工作场所生产力的视频。然后我开始缩小到我现在的位置。

## App

我 99%的时间都在使用`macOS`，所以我的 go to app 是 [iTerm2](https://www.iterm2.com/) 。

### 安装

您可以从 [iTerm2 的网站](https://www.iterm2.com/)下载，或者通过[Brew buck](http://caskroom.io/)安装:

*pro 提示: [Brew Cask](http://caskroom.io/) 很神奇，它可以通过一个简单的`cli`安装和管理你所有的 Mac 应用程序。*

```
brew cask install iterm2 
```

## *哦我的* ZSH

我已经试验过其他的`shell's`，比如[鱼](https://github.com/fish-shell/fish-shell)，但是它们总是太抽象，或者太复杂。

### 安装

```
# if you don't have zsh installed
brew install zsh

# install oh my zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

### 主题

我使用了 [robbyrussel](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes#robbyrussell) 主题。它给了我`git`信息，这是完美的。

它还会修剪`directory path`，因此您只能看到`current directory`。这避免了过长的文件夹路径占用宝贵的空间。

```
# ~/.zshrc
ZSH_THEME="robbyrussell" 
```

### 插件

我只使用几个插件:

*   [z](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#z) - *原生插件，不需要安装*
*   [zsh 完成量](https://github.com/zsh-users/zsh-completions)
*   [zsh 自我暗示](https://github.com/zsh-users/zsh-autosuggestions)

#### 安装

```
# zsh completions
git clone https://github.com/zsh-users/zsh-completions \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-completions

# zsh autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions \
    ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions 
```

然后更新你的`~/.zshrc` :

```
# ~/.zshrc

plugins=(
  z
  zsh-completions
  zsh-autosuggestions
)

autoload -U compinit && compinit 
```

然后`source`你的配置:

```
source ~/.zshrc 
```

## 奖励:键盘绑定

```
# ~/.zshrc

bindkey -e

# allows you to use the `option + [left, right]` key
# to skip words in a command you're writing.
bindkey '\e\e[C' forward-word
bindkey '\e\e[D' backward-word

# allows you to use the `[up, down]` key on a partial command
# to search your bash history for similar commands
bindkey '\e[A' history-search-backward
bindkey '\e[B' history-search-forward 
```

## 结论

我确实相信在没有工具和有太多工具之间有一个快乐的中间点。你可以有足够的危险，但不足以压倒你。

要更深入地了解我日常使用的东西，请查看我网站上的[使用页面。](https://www.michaeldscherr.com/uses/)