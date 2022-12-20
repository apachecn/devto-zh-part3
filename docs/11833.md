# 使用 Fish Shell 功能增强您的工作流程

> 原文：<https://dev.to/josef/enhancing-your-workflow-with-fish-shell-functions-3hl4>

首先也是最重要的，这是我的第一篇博文！我过去从来没有真正写过博客，但是如果你想看看我的其他博客文章，你可以去 josefaidt.me/blog。

* * *

围绕 JavaScript 社区的工具是不可思议的。外面有很多有用的工具，比如 Webpack、Rollup、create-react-app、Gatsby、VSCode extensions，还有 npm & Yarn！尽管如此，我觉得我们经常忽略了我们最重要的工具——命令行。

虽然起初令人望而生畏，但是命令行已经一次又一次地用诸如 Homebrew 和 Git 这样的实用程序证明了它的可靠性和可操作性。甚至像`cat`和`grep`这样的低级、开箱即用的实用程序也非常有用。如果您不是那种喜欢用点击操作来做一些单调的复制文件或查看文件内容的人，那么您可能已经知道 bash/fish 函数为显著改进您现有的工作流所提供的价值。但是，公用事业不一定非要在最微小的方面变得*强烈*有用。

当您正在调试应用程序时，键入诸如`yarn workspace someWorkspace run someScript`之类的命令会变得很乏味。或者如果使用全局配置文件，甚至可以使用`eslint --config ~/.config/.eslintrc.js someDirectory`在 ESLint 中指定一个配置文件。一些命令或单词在输入时很容易被弄糊涂(例如“组件”和“工作区”)。这就是函数发挥作用的地方。

## 鱼类功能

在这里，我们将探索 [fish shell](https://fishshell.com/) 中的函数，特别是我们如何使用它的函数来缩短我们的日常命令，比如上面提到的两条。与传统的 bash 别名相比，它的语法有所不同，但是我希望通过几个例子指导您，让您很快上手并运行起来。

让我们从`yarn workspaces`开始，使用一个别名，它既短又灵活。通过`code ~/.config/fish/config.fish`打开你的鱼配置文件，输入如下:

```
function ywrs
  command yarn workspaces $argv
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您刚刚创建了您的第一个函数！让我们来分解一下:

*   `function`:申报关键字
*   我们的别名，这是我们能够在终端上输入的内容
*   `command yarn workspaces $argv`
    *   `command`:告诉 shell“执行”以下命令所需的关键字，就像您通常在终端中输入的那样
    *   我们正在包装的命令
    *   `$argv`:将剩余的参数传递给定义的命令
    *   `ywrs info`将因此执行`yarn workspaces info`

为了测试您的新函数，您需要通过重新评估`config.fish`文件来重新加载 fish 配置(当创建一个新的 shell 会话时也要这样做):`source ~/.config/fish/config.fish`。

既然你已经尝试过了，让我们再尝试几个例子。

### 纱线工作区

```
function ywr
  command yarn workspace $argv
end 
```

Enter fullscreen mode Exit fullscreen mode

类似于最初的例子，这将允许我们通过一个既短又容易键入的关键字在工作空间中轻松地执行命令。

### ESLint

```
function eslint
  command eslint --config ~/.eslintrc.js $argv
end 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们通过指定一个配置文件来扩展现有的全局`eslint`命令，该配置文件现在可以在整个项目中使用，而无需设置项目级 ESLint 点文件。关于 global ESLint 和 VSCode 的更多信息，请查看我的另一篇[博文](https://dev.to/blog/2019/01/global-eslint/)。

### *激烈*举例

上面的 ESLint 示例允许您对单个配置文件使用`eslint`命令，但是自动化项目级安装怎么样呢？下面我们将发现如何利用函数、内部函数和一点 bash 逻辑来安装我们所有的 ESLint 依赖项，并将我们的全局配置文件复制到工作目录。

```
function yawn
    if test (count $argv) -lt 1; or test $argv[1] = "--help"
        printf "Don't yawn too loud now, I need a package name"
    else if test (count $argv) -eq 1
        switch $argv[1]
            case 'eslint'
                _install_eslint
            case '*'
                echo "Doesn't look like I have that package, try again."
        end
    else echo $argv
    end
end

function _install_eslint
    yarn add -D \
        eslint babel-eslint eslint-loader \
        prettier eslint-config-prettier eslint-plugin-prettier \
        eslint-config-standard eslint-plugin-standard \
        eslint-plugin-node \
        eslint-plugin-jsx-a11y \
        eslint-plugin-promise \
        eslint-plugin-import \
        eslint-plugin-react \
    ;and cp ~/.config/.eslintrc.js .
end 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们不会对这个例子进行太深入的探讨，但是让我们简单回顾一下它给工作流带来的价值:

*   `function yawn...`:高级别名，作为 cli 实用程序路由子命令(作为参数传递)并调用其各自的内部别名
*   `function _install_eslint...`:给定输入为`yawn eslint`，将从`yawn`调用的内部别名。
    *   安装 ESLint 依赖项
    *   复制全局 ESLint 配置文件
    *   注意:通常内部使用的命令(不是用户直接调用的)会加上一个下划线，这里我们利用这个命名法使我们的`config.fish`文件更易读

有了这些新功能，你现在可以进入一个新项目并调用`yawn eslint`来设置 ESLint。就我个人而言，我一直在使用这个工具，并且喜欢像这个例子一样自动完成单调的任务。

## 最后的想法

到目前为止，我们已经介绍了:

*   鱼类功能
*   编辑和使用鱼点文件:`config.fish`
*   重新加载点文件
*   使用函数来缩短命令
*   使用函数扩展命令
*   使用函数自动化任务

我希望我能够说服您开始使用这个功能，无论您是使用提供的示例还是创建自己的示例。说到这里，在上一个例子中，我提供了向`yawn`函数添加更多子命令的框架，尝试设置您自己的内部函数，这有助于自动化您的工作流的繁琐方面！我很想听听 Fish 功能是如何增强你的工作流程的，用你的故事发推文 [@garlicbred](https://twitter.com/garlicbred) ！

### 资源

*   [Fish 语法文档](https://fishshell.com/docs/current/index.html#syntax)
*   [Fish 变量和函数文档](https://fishshell.com/docs/current/index.html#identifiers)