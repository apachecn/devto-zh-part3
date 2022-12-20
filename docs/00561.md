# 我如何为开发设置我的 Mac？

> 原文：<https://dev.to/sakko/how-i-setup-my-mac-for-development-2obp>

每次换了新 Mac 或者升级 MacOS，痛苦都是重装一切。

我必须记下必须要做的事情，然后在谷歌上搜索一堆东西。

所以我决定写这篇博客供我个人使用，并希望它对其他人有用。

您可能看到了一些我可以改善开发环境的东西。请评论，这样我可以成为一个更好的开发者。

> 注:我刚刚得到了一个新的 Mac Mini 2018 i5 SSD256GB RAM20GB，我在 Mojave OS 上

# 检查/设置我的个人文件夹

我有时看到人们在个人文件夹中使用长用户名。我通常选择简短易记的用户名。我的是`sakko`。

要进行检查，请键入 open terminal 并键入`pwd`

```
$ pwd
/Users/sakko 
```

如果不是你喜欢的，现在就想办法改变，然后再继续。几个月后要改变这一点将极其困难。

# 安装 Xcode

从这里[https://developer.apple.com/download/more/](https://developer.apple.com/download/more/)或苹果应用商店下载 Xcode。

然后尝试创建一个单页应用程序 iOS 项目。运行项目以查看它是否成功构建。

# 安装 Xcode 命令行工具

打开终端并键入

```
$ xcode-select --install 
```

然后点击`install`继续

# 配置 git 配置

打开终端，输入这些(替换你的名字和电子邮件)

```
$ git config --global user.name "John Doe"
$ git config --global user.email "john.doe@gmail.com" 
```

# 设置全局。gitignores

Mac 有一些烦人的文件，git 可以忽略。我们来补充一下。

```
$ nano ~/.gitignore 
```

然后加上这些

```
# Node
npm-debug.log

# Mac
.DS_Store
.AppleDouble
.LSOverride

# Thumbnails
._*

# Files that might appear in the root of a volume
.DocumentRevisions-V100
.fseventsd
.Spotlight-V100
.TemporaryItems
.Trashes
.VolumeIcon.icns
.com.apple.timemachine.donotpresent

# Directories potentially created on remote AFP share
.AppleDB
.AppleDesktop
Network Trash Folder
Temporary Items
.apdisk

# Windows
Thumbs.db

# WebStorm
.idea/ 
```

完成后，通过运行
来全局激活它

```
$ git config --global core.excludesfile ~/.gitignore 
```

# 安装 NVM

对于 nodejs 开发，打开[https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm)

复制安装线(例如)

```
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash 
```

打开`~/.bash_profile`并将`source ~/.bashrc`添加到第一行。

打开`~/.bashrc`，查看这些线是否存在。(如果没有，补充一下)

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion 
```

如果您在项目中使用`.nvmrc`来激活类似`.ruby-version`的节点版本，那么也将这些添加到您的`.bashrc`T3 中

```
enter_directory() {
if [[ $PWD == $PREV_PWD ]]; then
    return
fi

PREV_PWD=$PWD
if [[ -f ".nvmrc" ]]; then
    nvm use
    NVM_DIRTY=true
elif [[ $NVM_DIRTY = true ]]; then
    nvm use default
    NVM_DIRTY=false
fi
}

export PROMPT_COMMAND=enter_directory 
```

然后安装 nodejs(仅限您使用的版本)

```
$ nvm install 10.16.0
$ nvm install node    # this will install latest 
```

# 安装 Ruby

我是一名 Rails 开发人员，所以我也需要 ruby。我选择 rvm(像 nvm 一样)来管理版本和 gemsets。

只需运行下面的命令并按照提示进行操作。

```
$ \curl -sSL https://get.rvm.io | bash -s stable 
```

> 完成后，重启终端一次，加载 rvm。

安装 ruby，我目前用的是`2.5.3`和`2.6.3`

```
$ rvm install 2.5.3
$ rvm install 2.6.3 
```

我不喜欢安装文档(以节省空间)，所以我通常会忽略它。(不确定这是否还有必要)

```
$ echo "gem: --no-document" >> ~/.gemrc 
```

然后安装最新版本的 Rails

```
$ gem install rails 
```

# 家酿、Postgresql、MySql、ElasticSearch、Redis 等 app

RVM 会为你安装家酿，所以你不需要重新安装。

所以让我们安装其他应用程序

```
# image processing
$ brew install imagemagick gs vips

# postgresql
$ brew install postgresql
$ brew services start postgresql # to start service

# mysql
$ brew install mysql
$ brew services start mysql # to start service

# redis
$ brew install redis
$ brew services start redis # to start service

# memcached
$ brew install memcached
$ brew services start memcached # to start service 
```

安装 ElasticSearch 需要 Java8，你可以从这里下载。

*   [https://www . Oracle . com/tech network/Java/javase/downloads/JDK 8-downloads-2133151 . html](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
*   [https://www . Oracle . com/tech network/Java/javase/downloads/JRE 8-downloads-2133155 . html](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)

同意条款或使用 Homebrew 的 openjdk】

```
$ brew tap AdoptOpenJDK/openjdk
$ brew cask install adoptopenjdk8 
```

然后安装 ElasticSearch

```
# elasticsearch
$ brew install elasticsearch
$ brew services start elasticsearch # to start service 
```

# Android 开发+ React-Native

最好的教程是官方的。

[https://Facebook . github . io/react-native/docs/getting-started](https://facebook.github.io/react-native/docs/getting-started)

# 我使用的其他应用

*   Atom.io
*   虚拟代码
*   崇高
*   iTerm2
*   阿尔弗烈德
*   1 密码
*   小告密者
*   iStat Menus

# 原子包

我通常从这些包开始，(你需要先从 atom 应用菜单`install shell command`)

```
apm install atom-beautify blame console-log editorconfig emmet es6-javascript language-docker language-javascript-jsx language-plantuml language-vue linter linter-swagger linter-ui-default plantuml-preview prettier-atom rails-snippets react react-es6-snippets ruby-block set-syntax 
```

# 崇高

如果我想快速打开一些文件(尤其是 JSON)，我通常会使用 Sublime。因为它是我能找到的最快的编辑器，拥有我需要的所有功能。这些是我平时用的包。

```
Babel
Emmet
GitGutter
JsPrettier
JSX 
```

# 其他物品，

Docker -还没有，我正在保存我宝贵的 256GB 固态硬盘 T _ _ _ _ T

python，gcloud，ansible，等等等等.....不胜枚举。稍后安装它们。

我还有什么要补充的吗？