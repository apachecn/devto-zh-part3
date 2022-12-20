# 如何组织你的组件

> 原文：<https://dev.to/spencercarli/how-to-organize-your-components-1a34>

编写代码可能很容易——但是如何组织它呢？

我和许多使用 React Native 的开发人员一起工作过，看到了各种组织代码的方式。从将所有内容放入一个文件到平均少于 10 行的文件。

在本教程中，我想给你一个平衡的方法。一种组织你的代码的方法，使它易于使用和维护，而不是一个管理/坚持的负担。

## 项目整体结构

在本教程中，我们只介绍了 UI 组件，但是我想全面介绍一下我的 React 本地项目通常是如何构造的。

项目目录结构

```
/app
  /assets
  /components
  /config
  /navigation
  /screens
  /util
  index.js 
```

没有提到的是 React 原生文件的其余部分。我喜欢把*我的*代码封装到一个`app`或`src`目录中，这样我就一直知道什么是我的。这也使得升级我的项目更加容易，并且减少了合并冲突的可能性。

## 我的分量哲学

在继续之前，我想说，在本教程的上下文中，当我说组件时，我指的是我的应用程序的 UI 组件。它们通常只是处理数据的显示，然后一个不同的组件(我的屏幕)将处理这些数据。

例如:我有一个带有 3 个文本输入和一个按钮的登录屏幕。我将对此进行结构化，以便文本输入组件显示数据并处理样式。当用户输入文本/按下按钮时，这些文本将被传递到*屏幕*来决定如何处理。

这是我对所有组件采用的方法。

## 按“功能区”组织

随着应用的增长，你将拥有更多组件。随着组件复杂性的增加，你会想把它们分解成更小的部分，这样它们更容易推理和维护。

当这种情况发生时，您可能会得到一个巨大的组件目录！这很好——即使在我下面的例子中，我也认为这非常好。

大量组件

```
/components
  TextInput.js
  Switch.js
  Button.js
  List.js
  ListItem.js
  Loading.js
  Calendar.js
  CalendarItem.js
  CardContainer.js
  CardBodyImage.js
  CardBodyText.js
  Header.js
  HeaderLeftButton.js
  HeaderCenterContent.js
  HeaderRightButton.js
  ... 
```

但是很难弄清楚它们是如何相互关联的。假设您已经在卡片组件、标题组件、表单、列表等之间共享了样式。你把那些共享的逻辑放在哪里？

这就是为什么我喜欢按功能区域将组件分解成更深一层的原因。让我们以上面的例子为例，把它组织得更深一层。

按功能区域组织的组件

```
/components
  /Form
    TextInput.js
    Switch.js
  /List
    List.js
    ListItem.js
  /Calendar
    Calendar.js
    CalendarItem.js
  /Card
    CardContainer.js
    CardBodyImage.js
    CardBodyText.js
  /Header
    Header.js
    HeaderLeftButton.js
    HeaderCenterContent.js
    HeaderRightButton.js
  Loading.js
  Button.js 
```

我们增加了一层嵌套，但它有助于按功能区域组织组件。我们一看就知道有什么联系。

现在，如果我们需要一组组件之间的共享样式，我们只需将它放在该目录中。简单。

最后一件事——我喜欢让一个组件功能区导出一个 API。它让我随心所欲地重新命名/重组事物。这意味着我向导出组件的每个目录添加了一个`index.js`。

表单/索引. js

```
import TextInput from './TextInput.js';
import Switch from './Switch.js';

export { TextInput, Switch }; 
```

这样做的另一个好处是减少了我从其他屏幕/组件的输入。而不是必须做一个

```
import TextInput from '../components/Form/TextInput';
import Switch from '../components/Form/Switch'; 
```

我可以要一份进口的

```
import { TextInput, Switch } from '../components/Form'; 
```

## 避免深层嵌套内容

现在对这种方法做一个简单的说明。我强烈建议你不要陷得比这更深。除此之外，它还可能成为一个主要的棘手问题。

如果这很痛苦，你不会到处坚持下去，这会导致不一致。不一致导致混乱。混乱导致天网。**不要**创造天网。

这里有一个例子。假设我们在`config/colors.js`中有一套标准颜色。从我们的`TextInput.js`文件中得到的结果应该是这样的

```
import colors from '../../config/colors.js'; 
```

我没意见。但是如果我们深入了解，你会发现越来越多。

最大的挑战是看看你有多少个目录。两个我很容易理解。我要开始数数了。

警惕深嵌套。

## 保持柔韧

最后，我想提醒你保持灵活性。对我有效的不会 100%对你有效。保持灵活。有时将一个功能区域放在一个目录中是有意义的(例如，一个表单)。其他时候没有原因(比如负载指示器)。保持灵活性，在你的应用中建立系统/规则，并遵守它们。

当你不再需要它们时，就修改它们。你会有明确的经验，知道*为什么*某些东西不工作，这样你就可以继续前进并修复。

不要花太多时间担心“完美”的代码组织。我保证不管你花 10 分钟还是 10 小时来设置它，你最终都会讨厌它的某些东西。

> 想要更深入地创建一个出色的组件库来构建您的应用程序吗？查看我在 React Native School 上的“[用故事书构建组件库](https://www.reactnativeschool.com/building-a-component-library)”课程！我们涵盖了设置和使用故事书，将一个界面分解成组件，如何构建它们，等等！