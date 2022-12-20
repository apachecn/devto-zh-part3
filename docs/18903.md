# bashme，关于你的第一个命令行界面

> 原文：<https://dev.to/agurodriguez/bashme-the-first-command-line-interface-about-you-3oc0>

嗨！这是我的第一个帖子。我很高兴能够在一段时间阅读了许多精彩的帖子后与社区分享一些东西。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[【agrodriguez】](https://github.com/agurodriguez)/[【bash me】](https://github.com/agurodriguez/bashme)

### 👨‍💻👩‍💻关于你的第一个命令行界面

<article class="markdown-body entry-content" itemprop="text">

# 痛打我

Bashme 是一个图书馆，它提供了一个向人们讲述你的古怪方式。创建它的目的是将它用作简历的替代品，但它可以有其他用例。它的目标是可定制、可扩展和易于使用。

## 演示

在我的个人网站玩:[http://agurodriguez.net](http://agurodriguez.net)

## 入门指南

安装它:

```
npm install bashme 
```

使用它:

```
var bashme = new Bashme.Bashme()
bashme.use(new Bashme.GitHub('agurodriguez'))
bashme.show(document.querySelector('#bashme'));
```

完整代码在[示例](example)文件夹中。

> 如果用 react，可以试试 [react-bashme](https://github.com/agurodriguez/react-bashme) 。

## 一点代码

### `Bashme`实例

是作为库的入口点的主类。它负责处理终端的输入/输出，并运行与每个给定命令相关的任务。

它有两个重要的方法…

</article>

[View on GitHub](https://github.com/agurodriguez/bashme)

如果能听到您的反馈并知道您是否觉得有用以及如何改进，那将是非常棒的。

谢谢大家！