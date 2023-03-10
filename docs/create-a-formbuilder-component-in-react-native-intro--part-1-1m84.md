# 在 React Native 中创建一个 FormBuilder 组件(简介和第 1 部分)

> 原文：<https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-intro--part-1-1m84>

大家好！👋最近我一直在开发一个 React 本地应用，其中一项任务是创建 10 多个不同的表单。他们都在做几乎相同的事情，唯一的区别是每个表单有多少个字段，以及每个表单使用什么类型的字段。这让我确定了以下目标:

*   尽可能多地重用代码(嗯，这就是 React 组件的妙处，对吧？🙂);
*   能够通过简单地创建某种排序或`form's configuration object or array`来创建一个新的表单，并将其作为`prop`传递给一个`FormBuilder`组件，该组件将为我呈现表单；
*   能够通过简单地更新现有表单的配置来扩展它。

我将整理一个帖子列表，在那里我将一步一步地描述我们如何通过在 React Native 中构建一个示例应用程序来实现这些目标。我们开始吧！🏁

* * *

本系列内容:

*   第 1 部分:创建一个新的 React 本地应用程序(当前)
*   [第 2 部分:创建一个简单的工资计算器表单](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-2-2dec)
*   [第 3 部分:创建自定义表单输入和按钮组件](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-3-1aib)
*   [第四部分:关于`FormBuilder`组件的工作](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-4-5gc3)
*   [第 5 部分:即时启用/禁用表单按钮](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-5-535h)
*   [第 6 部分:创建注册表单](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-6-2ohd)
*   第 7 部分:增加对布尔字段类型的支持

最终 GitHub 回购:
[https://github.com/vasilestefirta/react-native-form-builder](https://github.com/vasilestefirta/react-native-form-builder)

# 第 1 部分:创建新的 React 原生应用

在我们可以创建一个新的 React 本地应用程序之前，我们需要确保我们的机器上安装了所有必要的工具。让我们看看 [React Native 的入门](https://facebook.github.io/react-native/docs/getting-started)文档中的说明，看看我们需要安装哪些工具。因为我将在 iOS 模拟器上运行该应用程序，而不是使用 Expo 应用程序，所以我们需要遵循`Building Projects with Native Code`部分的说明。基本上，我们需要做以下事情(请注意，这些说明是针对 Mac 用户的，如果你是 Windows 用户，那么将`Development OS`选项切换到`Windows`，将`Target OS`切换到`Android`，然后从那里开始):

*   使用[自制](http://brew.sh/)安装`Node`和`Watchman`。如果你没有`Homebrew`，你可以按照他们网站上的说明安装；
*   安装`React Native CLI`
*   安装`Xcode`和`Xcode Command Line Tools`。

此时，我们可以使用`React Native CLI`创建一个新的应用程序，只需在您的终端中运行以下命令:

```
react-native init ReactNativeFormBuilder 
```

然后在 React 本地项目文件夹中运行`react-native run-ios`:

```
cd ReactNativeFormBuilder
react-native run-ios 
```

你应该很快就会看到你的新应用程序在 iOS 模拟器中运行。

[![App Home Screen](img/a267c03a5b79de59960f01dfa526075e.png "App Home Screen")](https://res.cloudinary.com/practicaldev/image/fetch/s--4rdrPlUd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7tjrwxhhqp2367d19er7.jpeg)

现在是时候使用默认的 React 本地组件构建一个简单的表单了。转到[第 2 部分](https://dev.to/dev_nope/create-a-formbuilder-component-in-react-native-part-2-2dec)，我们将创建一个简单的工资计算器表单。