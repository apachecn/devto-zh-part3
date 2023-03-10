# 发现你的代码气味！

> 原文：<https://dev.to/andy/reek---find-your-code-smells-4pl2>

我最近在看 RailsConf 2016 (代码气味的伟大介绍 btw)的 [Sandi Metz 的演讲时发现了 Reek。](https://youtube.com/watch?v=PJjHfa5yxlU)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [特罗斯纳](https://github.com/troessner) / [臭气](https://github.com/troessner/reek)

### 红宝石气味检测仪

<article class="markdown-body entry-content container-lg" itemprop="text">

[![reek logo](img/bd6fc96d85df00c0f763ebad22e34de2.png)](https://github.com/troessner/reeklogo/reek.text.png)

# 红宝石气味检测仪

**目录**

*   [概述](https://github.com/troessner/reek#overview)
*   [快速入门](https://github.com/troessner/reek#quickstart)
*   [例子](https://github.com/troessner/reek#example)
*   [支持的 Ruby 版本](https://github.com/troessner/reek#supported-ruby-versions)
*   [固定气味警告](https://github.com/troessner/reek#fixing-smell-warnings)
*   [来源](https://github.com/troessner/reek#sources)
*   [代码气味](https://github.com/troessner/reek#code-smells)
*   [配置](https://github.com/troessner/reek#configuration)
    *   [命令行界面](https://github.com/troessner/reek#command-line-interface)
    *   [配置文件](https://github.com/troessner/reek#configuration-file)
        *   [配置加载](https://github.com/troessner/reek#configuration-loading)
        *   [配置选项](https://github.com/troessner/reek#configuration-options)
    *   [生成“待办事项”列表](https://github.com/troessner/reek#generating-a-todo-list)
    *   [小心多个配置文件](https://github.com/troessner/reek#beware-of-multiple-configuration-files)
    *   [源代码注释](https://github.com/troessner/reek#source-code-comments)
*   [用途](https://github.com/troessner/reek#usage)
*   [开发臭气/贡献](https://github.com/troessner/reek#developing-reek--contributing)
*   [输出格式](https://github.com/troessner/reek#output-formats)
*   [使用轨道](https://github.com/troessner/reek#working-with-rails)
*   [集成](https://github.com/troessner/reek#integrations)
    *   [编辑集成](https://github.com/troessner/reek#editor-integrations)
    *   [使用或支持我们的项目](https://github.com/troessner/reek#projects-that-use-or-support-us)
    *   [杂项](https://github.com/troessner/reek#misc)
*   [兄弟姐妹](https://github.com/troessner/reek#brothers-and-sisters)
*   [贡献者](https://github.com/troessner/reek#contributors)
*   [附加资源](https://github.com/troessner/reek#additional-resources)
    *   [杂项](https://github.com/troessner/reek#miscellaneous)
    *   [更多信息](https://github.com/troessner/reek#more-information)

## 概观

*   [![Build Status](img/cfae0f91de99032bf6bae4b3d71d61ab.png)](https://travis-ci.org/troessner/reek?branch=master)
*   [![Gem Version](img/0aeb7ab5d92d578a08d74bf6c17aa8ff.png)](https://badge.fury.io/rb/reek)
*   [![](img/81a77b0afb44d58eb81342dea578d521.png)](https://camo.githubusercontent.com/50fa78c76ed041712bdcd511401c2f69e1f0b57192ce5891cedb584ec54d5ef9/687474703a2f2f696d672e736869656c64732e696f2f6769746875622f7461672f74726f6573736e65722f7265656b2e737667)
*   [![](img/f9e51d244d222fddda79dc1d8c86e07a.png)](https://camo.githubusercontent.com/9f6b5dca9fd95975898fb95c2cc5b995351004a6c4443ccb13e1916a97bb122a/687474703a2f2f696d672e736869656c64732e696f2f62616467652f6c6963656e73652d4d49542d627269676874677265656e2e737667)
*   [![Inline docs](img/c57529fe88e03b1556921cbc2f0b9171.png)](https://inch-ci.org/github/troessner/reek)
*   [![Code Climate](img/3561cd10bd3ea8fb7b1d19fbc3d03795.png)](https://codeclimate.com/github/troessner/reek)
*   [![codebeat](img/1cf92a30c2f3f19c14a823e9b9fbea57.png)](https://codebeat.co/projects/github-com-troessner-reek)
*   [![](img/fec13d20b5b9ea543b40585c80dd3cf5.png)](https://camo.githubusercontent.com/a7a5d507497c580f58b0073402db1489c149d00175ed0e9b525c3cef0fa1aeb2/687474703a2f2f727562792d67656d2d646f776e6c6f6164732d62616467652e6865726f6b756170702e636f6d2f7265656b3f747970653d746f74616c)
*   [![](img/ae75da1908536bc03d2719c6020d9526.png)](https://camo.githubusercontent.com/8129dbed1fccb538dfa131554e991017f38ef0e0661f493538a8a8ba321d43f6/687474703a2f2f727562792d67656d2d646f776e6c6f6164732d62616467652e6865726f6b756170702e636f6d2f7265656b3f6c6162656c3d646f776e6c6f6164732d63757272656e742d76657273696f6e)

## 快速启动

Reek 是一个工具，它检查 Ruby 类、模块和方法，并报告它发现的任何代码气味。

关于[代码气味](https://github.com/troessner/reekdocs/Code-Smells.md)和恶臭的精彩介绍，请查看[的这篇博文](https://blog.codeship.com/how-to-find-ruby-code-smells-with-reek/)或[的那篇](https://troessner.svbtle.com/the-latest-and-greatest-additions-to-reek)。还有 [RubyConfBY](http://rubyconference.by/) 的[这个演讲](https://www.youtube.com/watch?v=pazYe7WRWRU)(如果你喜欢的话，也有[幻灯片](http://talks.chastell.net/rubyconf-by-lt-2016/))。

通过 rubygems 安装它:

```
gem install reek
```

Enter fullscreen mode Exit fullscreen mode

像这样运行它:

```
reek [options] [dir_or_source_file]*
```

Enter fullscreen mode Exit fullscreen mode

## 例子

想象一个源文件`demo.rb`包含:

```
# Smelly
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/troessner/reek)

我喜欢这样的工具，因为你可以运行它们，让计算机告诉你哪些代码可能有问题。另外:

*   适用于 Ruby 2.3、2.4 和 2.5
*   Rails 几乎不需要配置
*   简单的常规配置
*   有完整的自述文件

我还没怎么用过，好像是比较受欢迎的宝石。你听说过吗？你对此有什么想法？