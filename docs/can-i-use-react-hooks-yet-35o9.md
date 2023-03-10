# 我可以使用 React 钩子了吗？

> 原文：<https://dev.to/httpjunkie/can-i-use-react-hooks-yet-35o9>

**更新:2019 年 2 月 06 日 [React 16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) 推出钩子作为稳定特性！**

简短的回答是...是的。当我最初写这篇文章时，我们还在测试阶段，现在 React 16.8 已经发布了钩子作为一个稳定的特性，你应该制作你的代码分支，并开始慢慢地将小组件转换成钩子，这是开始学习的最好方式。另外，如果你在过去的 6 个月里写了任何不使用钩子的教程，一个很好的练习就是简单地把旧文章转换成钩子。开始做任何事情，时机已经到来！

然而，并不是每个人都会立即转向钩子；通过对其中几个进行重构，你会发现容易和难改变的地方。

显然，这项工作将从组件级开始。对于我的公司来说，一个简单的例子就是浏览和更新 StackBlitz 演示来展示如何使用我们的组件和钩子。最简单的起点是类似于我们的 KendoReact[drop down button](https://www.telerik.com/kendo-react-ui/components/buttons/dropdownbutton/)组件的东西，我们当前的示例只展示了如何通过一个包装类来使用它们，就像这个 StackBlitz 演示中那样:

[StackBlitz 演示:类风格语法](https://stackblitz.com/edit/2019-r1-drop-down-button?file=app%2Fmain.js)

但是根据我在下面的第一篇文章(状态和效果)中学到的基础知识。我可以把类风格的 StackBlitz 演示换成 Hooks，只需安装包含 Hooks 的 React 的最新测试版:

`npm i react@next react-dom@next`

和导入`useState`:

`import React, { useState } from 'react';`

此时，我可以将类重构为一个功能组件，去掉对`.this`和构造函数的引用。并使用`setState()`替换该语句。

相反，正如你在[重构的 StackBlitz 演示](https://stackblitz.com/edit/2019-r1-drop-down-button-hooks?file=app%2Fmain.js)中看到的，我们可以在对`useState()`的同一个调用中创建状态变量及其更新方法。

`const [value, setValue] = useState('Components');`

如果需要，我们可以设置任何默认状态值。然后创建一个我们可以调用的方法，这个方法又可以调用 update 方法。更新我们的组件来调用这个新的`changeValue()`函数，我们已经使用基本的钩子使演示比以前简单多了。

```
const ButtonContainer = () => {
  const sizes = ['Homepage', 'Components', 'Changelog', 'Pricing'];

  const [value, setValue] = useState('Components');
  const changeValue = (event) => setValue(event.target.value);

  return <DropDownList data={sizes} value={value} onChange={changeValue}/>
} 
```

你可以在这本详尽的分步指南中找到更多关于钩子的信息。我有上面的例子和关于[状态和效果](https://www.telerik.com/kendo-react-ui/react-hooks-guide/#toc-hooks-for-state-and-effects)、[上下文](https://www.telerik.com/kendo-react-ui/react-hooks-guide/#toc-hooks-for-context)、[减速器](https://www.telerik.com/kendo-react-ui/react-hooks-guide/#toc-hooks-for-reducers)、[自定义钩子](https://www.telerik.com/kendo-react-ui/react-hooks-guide/#toc-custom-react-hooks)和[管理组件](https://www.telerik.com/kendo-react-ui/react-hooks-guide/#toc-managing-control-state-of-a-kendoreact-component)的控制状态的章节。

我真的建议打开钩子，玩一些简单的东西。这是通向更高级的东西的大门，也是一次大开眼界的经历，让我感到很高兴成为一名 react 开发人员，并有时间研究和学习它，这对我很有价值。我希望对你也是如此！

编程中出现的某些东西只是技术和语法上的突破性变化，有时甚至更大的变化会让你觉得事情真的在变得更好。这是我脑海中的一件事。我很高兴 React 花了这么多时间，感觉就像他们用钩子打了一个本垒打。

以下是我一路走来使用的一些很棒的资源:

[文档教程](https://reactjs.org/docs/hooks-intro.html)
[制作 React 钩子的意义](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)