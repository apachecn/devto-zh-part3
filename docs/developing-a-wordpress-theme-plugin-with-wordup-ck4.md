# 用 wordup 开发一个 WordPress 主题/插件

> 原文：<https://dev.to/shry/developing-a-wordpress-theme-plugin-with-wordup-ck4>

作为 wordup 的开发者，我想展示一下如何用 wordup 简单快捷地建立一个新的 WordPress 项目

## 什么是 wordup？

Wordup 是一个开源的开发工具包，用于设置和管理你的本地 WordPress 主题/插件开发，因此你可以完全专注于编码。

wordup-cli 是用 nodejs 编写的命令行界面，使用 docker-compose。

它作为一个包在 [npm](https://www.npmjs.com/package/wordup-cli) 上提供

## 必填项

您需要在系统上安装 nodejs 和 docker。

## 初始化一个新项目

我们使用 wordup-cli 作为本地 devDependency。

为此，我们将通过`npx`运行 wordup-cli 命令。

`npx`属于 npm 并执行命令，即使它没有安装在您的系统上。

开始您的第一个项目运行(在您选择的文件夹中):

```
npx wordup-cli init 
```

您将被引导至一些问题。之后，wordup 将自动安装所有的容器和组件。

**注意:**如果你使用 wordup-cli 作为 devDependency，确保在每个 wordup 命令前使用`npx`。

你可以在 GitHub 上找到完整的 wordup 文档

## 开始发育

在编辑器中查看新创建的项目文件夹。位于/src 文件夹中的所有文件都属于你想要开发的 WordPress 插件/主题。

该文件夹之外的文件通常属于您的项目。

在 package.json 文件中，您可以看到 wordup 项目设置。

## 导出您的项目

在一些编码之后，你想要发布你的 WordPress 插件/主题。

你可以在你的项目文件夹中运行:

```
npx wordup export 
```

根据`.distignore`和您的 Wordup 设置，wordup 会将您的/src 文件夹导出为 zip 文件。

你也有可能出口你的整个项目。[导出单据](https://github.com/wordup-dev/wordup-cli#wordup-export-type)

## 快乐编码！！

欲了解更多信息，请访问 [wordup.dev](https://wordup.dev)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[word up-dev](https://github.com/wordup-dev)/[word up-CLI](https://github.com/wordup-dev/wordup-cli)

### Wordup 是一个完全集成的 WordPress 开发平台。在本地开发插件和主题。在云端预览。WP 中的自动更新。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Wordup CLI

[![Wordup](img/64a967ef003620676f24d2dacaa8102b.png)](https://camo.githubusercontent.com/14051d952b6c286808efb5ce5d83fdc28c17f4e2/68747470733a2f2f7777772e776f726475702e6465762f77702d636f6e74656e742f75706c6f6164732f323032302f30352f6c6f676f5f736f6369616c2e706e67)

[![Version](img/04a7d46641f3850dec5c038b2929176f.png)](https://npmjs.org/package/wordup-cli)[![Downloads/week](img/1ba3443ed75d3c8b941b714aea6863c2.png)](https://npmjs.org/package/wordup-cli)[![Wordup docs](img/6998ee69f8ff93817247afa1f81b2a8c.png)](https://docs.wordup.dev)[![Twitter Follow](img/188c17799e64e247a49897713806b286.png)T11】](https://twitter.com/wordup_dev)

> <g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji>T2】警告:1 . 0 . 0 主版本不向后兼容

Wordup CLI 是一个开源的开发工具包，用于设置和管理你的本地 WordPress 主题/插件开发，因此你可以完全专注于编码。

它基于 docker-compose，使用 docker 容器化的 LAMP-stack，包含你当前项目所需的所有 WordPress 插件/主题。

*   [特性](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#features)
*   [安装/要求](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#installation--requirements)
*   [文档](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#documentation)
*   [用途](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#usage)
*   [命令](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#commands)
*   [Visual Studio 代码的文字处理](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#wordup-for-visual-studio-code)
*   [了解更多信息](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#learn-more)
*   [许可/贡献](https://raw.githubusercontent.com/wordup-dev/wordup-cli/master/#license--contributing)

# 特征

使用 wordup-cli 可以做什么:

*   <g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">💡</g-emoji> **快速测试新想法**——在 wordup 中开发你的新 WordPress 主题/插件项目。
*   <g-emoji class="g-emoji" alias="stopwatch" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/23f1.png">⏱</g-emoji> **加速你的开发** -在几分钟内安装一个新的项目
*   <g-emoji class="g-emoji" alias="hammer_and_wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e0.png">🛠️</g-emoji> **样板文件**——用 WordPress 的官方源代码搭建你的主题/插件(例如[下划线](https://github.com/automattic/_s))。你也可以在你的源代码中添加像 *Gutenberg* 块这样的代码片段。
*   <g-emoji class="g-emoji" alias="gear" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2699.png">⚙️</g-emoji>T2】自动安装 …

</article>

[View on GitHub](https://github.com/wordup-dev/wordup-cli)