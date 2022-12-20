# 带有样式组件的故事书中的默认样式

> 原文：<https://dev.to/tomekbuszewski/default-styles-in-storybook-with-styled-components-58dp>

最近我在为我的新项目写一本故事书。虽然我喜欢它提供的封装，但是像重置这样的全局样式仍然很常见。即使变化不大，我还是想在我的每个故事里都有它们。

## 全局复位

我的全局样式是使用[样式的重启](https://github.com/alexruzzarin/styled-reboot)创建的，这是对[样式组件](https://www.styled-components.com/)的补充。这是一个可配置的引导复位-我一直在寻找的东西。

代码本身看起来非常简单:

```
import { createGlobalStyle } from "styled-components";
import reboot from "styled-reboot";

import design from "./";

const options = {
  bodyColor: design.brand("violet"),
  bodyBg: design.brand("white"),
  fontSizeBase: "8px",
  linkColor: design.brand("base"),
  linkHoverColor: design.brand("base"),
};

const GlobalStyle = createGlobalStyle` ${reboot(options)} `;

export default GlobalStyle; 
```

这给了我们一个可以彼此相似放置的组件。比如像这样:

```
import GlobalStyle from "../globalStyle";

const App = () => (
  <React.Fragment>
    <GlobalStyle />
    <div>Hello</div>
  </React.Fragment>
); 
```

很棒的东西，但是我如何在我的故事中使用它呢？

## 故事书

我使用故事书作为我开发 UI 的主要环境。所以很明显，我希望它尽可能地反映实际结果。这里必须包含全局复位。

我的第一个方法是简单地给每个故事添加`<GlobalStyle />`组件，就像这样:

```
storiesOf("UI", module)
  .add("Button", () => (
    <React.Fragment>
      <GlobalStyle />
      <Button type="button">Hello</Button>
    </React.Fragment>
  )); 
```

虽然这肯定有效，但它给了我额外的开销。我必须记住包括重置，每次导入它等。

我懒得做这个。我必须想出更好的办法。

然后我想起了故事书提供的[装饰者](https://storybook.js.org/addons/introduction/)选项！

> 装饰者是包装一个故事的包装组件或故事书装饰者。
> 
> — <cite>[插件简介](https://storybook.js.org/addons/introduction/)</cite>

所以，只剩下写这样的 decorator 了。

## 书写装饰者

Storybook 的所有附加代码都在`config.js`文件中，大部分在`.storybook`目录中(如果没有另外声明的话)。我的是这样的:

```
import { configure } from '@storybook/react';

const req = require.context('../../', true, /\.stories\.tsx$/);

function loadStories() {
  req.keys().forEach(filename => req(filename))
}

configure(loadStories, module); 
```

简单浏览故事。所以，下一件事是添加装饰。

```
import { addDecorator, configure } from '@storybook/react';

import * as React from 'react';
import GlobalStyle from '.../ui/globalStyle';

const req = require.context('../../', true, /\.stories\.tsx$/);

function loadStories() {
  req.keys().forEach(filename => req(filename))
}

const withGlobal = (cb) => (
  <React.Fragment>
    <GlobalStyle />
    {cb()}
  </React.Fragment>
);

addDecorator(withGlobal);
configure(loadStories, module); 
```

让我们好好谈谈。

首先，我们导入`addDecorator`函数，它将负责将我们的插件应用到系统中。

第二，我们进口反应，因为我们将创造一点点 JSX，和风格早些时候创建的。

下一步是创建装饰器本身。只有一个参数的简单函数。我将它包装在 [`React.Fragment`](https://reactjs.org/docs/fragments.html) 中，这允许使用多个子元素，而无需创建额外的 DOM 元素。如果您在低于 16 的版本中使用 React，请将`div`视为中性包装器。

最后一步是使用`addDecorator`应用装饰器。

就这么简单。最重要的是，将`configure`作为最后一个命令执行。

现在，额外的 CSS 代码将被添加到您的每个故事中，无需任何进一步的编辑。