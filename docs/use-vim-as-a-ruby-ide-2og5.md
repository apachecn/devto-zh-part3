# 使用 Vim 作为 Ruby IDE

> 原文：<https://dev.to/spacevim/use-vim-as-a-ruby-ide-2og5>

# 使用 Vim 作为 Ruby IDE

这是将 SpaceVim 用作 Ruby IDE 的通用指南，包括层配置和使用。将涵盖以下每个部分:

*   启用语言层
*   代码完成
*   语法林挺
*   跳转到测试文件
*   运行代码
*   代码格式
*   REPL 支持 ###启用语言层

要在 SpaceVim 中添加 Ruby 语言支持，需要启用`lang#ruby`层。按`SPC f v d`打开 SpaceVim 配置文件，添加以下代码片段:

```
[[layers]]
  name = "lang#ruby" 
```

要了解更多信息，你可以阅读 lang#ruby 层文档。

### 代码补全

图层将自动加载 Ruby 插件，除非它在你的`init.toml`中被覆盖。完成菜单将在您键入时打开。

[![rubycomplete](img/532304a464c9a9bcfe2908c2b2af4de2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p-rKSlD8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/13142418/53355518-20202080-3964-11e9-92f3-476060f2761e.png)

### 语法林挺

默认情况下，棋盘格图层处于启用状态。该层通过 [neomake](https://github.com/neomake/neomake) 提供异步语法林挺。它将异步运行 rubocop。

通过 gem 安装 rubo COP:

```
gem install rubocop 
```

[![rubylint](img/04d2854ad7a147957c6bf864b4036e00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pBlLnQbY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/13142418/53347011-32459300-3953-11e9-9ca2-3e07f832db5a.png)

### 跳转到测试文件

SpaceVim 使用内置插件来管理项目中的文件，您可以在项目的根目录下添加一个`.project_alt.json`，内容如下:

```
{
  "src/*.rb": {"alternate": "test/{}.rb"},
  "test/*.rb": {"alternate": "src/{}.rb"}
} 
```

通过这种配置，您可以通过命令`:A`在源代码和测试文件之间跳转

### 运行代码

要运行当前脚本，您可以按`SPC l r`，一个分割窗口将被打开，脚本的输出将显示在该窗口中。它异步运行，不会阻塞您的 Vim。

[![rubyrunner](img/1587943eaa701cfc9c8cb221802df808.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TBVcz9e2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/53300165-6b600380-387e-11e9-852f-f8766300ece1.gif)

### 代码格式化

默认情况下，格式层也是启用的。在这一层，你可以使用键绑定`SPC b f`来格式化当前的缓冲区。使用此功能前，请安装 rufo:

```
gem install rufo 
```

[![formatruby](img/9f79314eba9620a79c8c10d0030617db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nl7WCzU1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/53301042-3c02c400-3889-11e9-9918-430ad6a7f08f.gif)

### REPL 支持

用`SPC l s i`开始一个`irb`次 REPL 过程。REPL 进程启动后，你可以向下级进程发送代码。所有按键绑定都以`SPC l s`为前缀，包括发送行、发送选择甚至发送整个缓冲区。

[![rubyrepl](img/8104249c2526833ad153e66dd0365635.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MdOV11GV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/53347455-1098db80-3954-11e9-87c3-13a027ec88f6.gif)