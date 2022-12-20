# ASDF，你所有语言的版本管理器

> 原文：<https://dev.to/logicmason/asdf-the-package-manager-for-all-your-languages-2omg>

## 为什么用 ASDF？

ASDF 是编程语言的版本经理。这有点像 RVM 是 Ruby 或 NVM 是 Node，但它也支持 Erlang、Elixir、Haskel、Ocaml、PHP、Python、Rust 和许多其他语言。

## 先决条件

本指南假设您有自制程序和 Xcode 命令行工具，没有其他工具。要查看新安装的 macOS Mojave 的设置，请参见[这个简短的视频](https://youtu.be/Og4L5ijxqpA)。

[https://www.youtube.com/embed/YuOnN2bVeFY](https://www.youtube.com/embed/YuOnN2bVeFY)

## 克隆回购

上的 ASDF 回购页面会有克隆最新回购 github 的说明:[https://github.com/asdf-vm/asdf](https://github.com/asdf-vm/asdf)

```
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.7.1 
```

接下来，将它添加到您的 shell 中:

```
echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc
echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc 
```

重启你的 shell(或者打开一个新的标签页)并运行`asdf`来验证它是否已经安装。

## 安装插件依赖项

```
brew install \
  coreutils automake autoconf openssl \
  libyaml readline libxslt libtool unixodbc \
  unzip curl gpg wxmac 
```

## 基本命令

*   `asdf plugin-list-all`:显示所有可用的插件(即语言)
*   `asdf plugin-add <language>`:安装*语言*
*   `asdf list-all <language>`:显示*语言*的所有可用版本
*   `asdf list <language>:`显示*语言*的所有安装版本
*   `asdf install <language> <version>`:
*   `asdf current`:显示当前启用的语言
*   `asdf global <language> <version>`:启用所选语言版本

##安装节点
节点需要额外的步骤:

```
bash ~/.asdf/plugins/nodejs/bin/import-release-team-keyring
asdf plugin-add nodejs
asdf list-all nodejs
asdf install nodejs <version>
asdf global nodejs <version> 
```

##安装 Erlang

```
asdf plugin-add erlang
asdf list-all erlang 
```

根据 ASDF Erlang 插件报告的自述文件，我们需要向安装命令传递几个标志:

```
export KERL_CONFIGURE_OPTIONS="--disable-debug --without-javac"
asdf install erlang <version>
asdf global erlang <version> 
```

## 安装仙丹

```
asdf plugin-add elixir
asdf list-all elixir
asdf install elixir <version>
asdf global elixir <version> 
```

## 安装 Ruby

```
asdf plugin-add ruby
asdf list-all ruby
asdf install ruby <version>
asdf global ruby <version> 
```

## 验证你有你需要的东西

```
asdf current 
```

## 安装凤凰框架

如果你正在[炼金术士训练营](https://alchemist.camp)上阅读这篇文章，你很可能在用 ASDF 做长生不老药，并且还想建立凤凰城:

```
mix local.hex
mix archive.install hex phx_new 1.4.5 
```

## 搞定！

ASDF 是一个非常方便的工具，用于设置开发机器和保存您可能需要的任何语言的版本。相对于几种特定语言的工具，有一个统一的工具是一个很大的进步。

ASDF 还支持通过`.tool-versions`文件进行项目配置，以及本安装指南中未涉及的许多其他内容。

*最初发表于[方士营](https://alchemist.camp/episodes/asdf-language-versions)T3】*