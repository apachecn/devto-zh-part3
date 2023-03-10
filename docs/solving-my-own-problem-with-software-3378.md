# 用软件解决我自己的问题

> 原文：<https://dev.to/papaponmx/solving-my-own-problem-with-software-3378>

TLDR:我做了一个手机专用的 PWA。
链接到 app https://simpleunitconverter.netlify.com/[➡](https://simpleunitconverter.netlify.com/)t5】链接到回购 https://github.com/papaponmx/simple-unit-converter[➡](https://github.com/papaponmx/simple-unit-converter)

嘿伙计们，这是一个简短的帖子，只是想让你们知道我刚刚推出了一个 PWA，它解决了一个非常私人的问题。

## 问题

我去健身房，三种情况重合:

*   我使用一个应用程序，允许你使用磅或公斤，但不能两者都用。
*   我的健身房用磅来表示机器，用公斤来表示哑铃。
*   因此，我需要一种方法来不断地将单位从磅转换成千克。

现在，让我们探索替代方案:

*   我可以在精神上做到这一点，但我不会太懒。
*   有一个 app，但是我不想看广告，也不想在我的设备上用 25 MB 这么简单的 app 空间，也不想看广告。
*   我可以谷歌一下，但这意味着要打开移动数据。这看起来微不足道，但我开始收到社交应用程序的通知。问题是，他们让我训练的时间比我应该训练的时间多，让我更容易因为状态不好而受伤。
*   有一个这样的网站。有点，但是他们没有完全响应。
*   创建一个连接到云的 PWA，以便跟踪您自己的培训。我当时没有想到这一点

## 解🤓

利用我的知识，制作一个具有以下特征的 PWA:

*   ️Bidirectional 千克到磅的单位转换🏋️‍
*   默认为深色主题🌑
*   已测试屏幕阅读器🔉
*   没有用户跟踪🔍
*   没有广告📈
*   不💩
*   开放源码📚

### 科技栈

*   托管于 [Netlify](https://netlify.com) 。
*   创建 React 应用程序作为样板文件。
*   使用 React 钩子和上下文 API 进行状态管理。
*   样式组件。

链接到 app https://simpleunitconverter.netlify.com/[➡](https://simpleunitconverter.netlify.com/)链接到回购 https://github.com/papaponmx/simple-unit-converter[➡](https://github.com/papaponmx/simple-unit-converter)

干杯伙计们。