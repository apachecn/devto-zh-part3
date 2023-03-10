# 我们如何在 Helixa 构建前端

> 原文：<https://dev.to/omarassadi/how-are-we-structuring-front-end-at-helixa-1e78>

在 Helixa，当我们谈论前端开发时，我们正处于一个漫长旅程的开始，因为我们正在为我们所有的未来项目创造基础。

## 目标

我们将构建多个客户端 SaaS 应用程序，其中一个名为 *Account* 的中央应用程序将作为身份服务器工作。

所有未来的应用程序将需要共享多个组件，看起来很像，但有一个定制的*调色板*，以便在视觉上区分**产品**。

#### 共同语言

为了与我们的 UX/UI 设计师分享共同的语言，我们遵循*原子设计原则*(查看[布拉德·弗罗斯特关于原子设计的帖子](http://atomicdesign.bradfrost.com/chapter-2/))并开始构建*原子*、*分子*和*有机体*，我们将需要它们来实现我们产品的前端。

由于我们需要在未来的所有应用程序中共享和重用组件，我们从构建一个名为 *hx-react-components* 的 ***组件库*** 开始，这个组件库也将作为每个组件的外观和工作方式的真实来源。

## 堆栈和工具

这是 Helixa 前端团队处理的库和工具列表。

React 是一个声明式的、高效的、灵活的 JavaScript 库，用于构建用户界面。它允许你用称为“组件”的小而孤立的代码片段组成复杂的 ui。

**样式化组件** : *利用标记模板文字(JavaScript 的一个新功能)和 CSS 的强大功能，样式化组件允许你编写实际的 CSS 代码来样式化你的组件。*

**Storybook** : *Storybook 是一款开源工具，用于独立开发 React、Vue 和 Angular 的 UI 组件。它使构建令人惊叹的 ui 变得有条理和高效。*

[![Helixa Components Storybook](img/07e0a070e6ed8214fb39e8b2bbce4750.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KcvbKhL3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/11qmx99xt9nixfc8noad.gif)

故事书:[https://storybook.js.org](https://storybook.js.org)

circle ci:*circle ci 的持续集成和交付平台让各种规模的团队都可以轻松快速地大规模构建和发布优质软件。*

[![Helixa Components CircleCi](img/76d999a0623b96e288abcfd5896a6ad0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dpAwtG_D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7w080aw4cj99svgdxzat.png)

圆周率:[https://circulaci . com](https://circleci.com)

## 我们自己的组件库

第一:为什么要写组件库？

**开发速度**:不要损害库的采用，使开发人员能够协作和开发组件。

**一致性**:保持一致性，但尽可能留有发挥的余地。

**维护&标准化**:通过智能架构和工具，统一您的技术堆栈并简化维护。

**性能**:尽可能不要使用跨库，使用单个组件而不是整个库。

**可发现性**:利用可发现性平台帮助开发人员找到并使用他们需要的组件。

## 流程

这是一个*组件*文件夹的常见结构:

[![Helixa Component Folder Structure](img/e7a102f6c0ac6928ae0c70e5764cfee4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bzgajG51--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2kkodsgtujk3rwgpvv24.png)

每个组件都有:

*   *Component.js* 实现文件。
*   一个 *StyledComponent.js* 文件
*   一个 *Component.stories.js* 文件，描述 Storybook 上的 it's showcase 页面。
*   一个包含所有单元测试的 *Component.spec.js* 。

我们已经将单元测试覆盖率设定在最低 80%。

这是我们的工作方式:每个任务(新组件、修复等)都有自己的分支，并经历相同的流程:

*   将请求拉至主机
*   回顾
*   合并

每次任何一个*分支*出现新的提交，我们的 **CI** (CircleCi)都会运行一个**单元测试**列表。

在*主*分支上的每次提交时，我们的 **CI** 运行多个顺序作业:

*   运行单元测试
*   构建故事书静态版本
*   在 AWS 上部署新版本([http://components . helix a . ai](http://components.helixa.ai))
*   更新*包*版本(通过 *npm 版本*
*   在 **NPM** 发布新包
*   将更改推送到 git

## 处理 SVGs 图标

所有应用程序都需要**图标**，这就是我们在 Helixa 消费**SVG**的方式。

我们有一个 git 存储库，UX/UI 团队可以很容易地添加 Svg 文件。
在 master branch 的每一次推送中，神奇的[哈士奇](https://www.npmjs.com/package/husky)都会为我们运行一个脚本:

[https://jsfiddle.net/assadiomar/u6r7mef8/embedded/js//dark](https://jsfiddle.net/assadiomar/u6r7mef8/embedded/js//dark)

基本上所有添加到 */src* 文件夹的 svg 文件都使用 **svgo** 进行优化，它们的 svg 单声道路径被提取出来(多亏了 [extract-svg-path](https://www.npmjs.com/package/extract-svg-path%20package) )。

然后，icons.json 文件被写成这样:

```
{
  "alert": "M18,26h4v4h-4V26z M20,10c1.1,0,2,0.9,2,2v8c0,1.1-0.9,2-2,2s-2-0.9-2-2v-8C18,10.9,18.9,10,20,10z M20,40c11,0,20-9,20-20 S31,0,20,0S0,9,0,20S9,40,20,40z",
  "add": "M37.1,22.9H22.9v14.3c0,1.6-1.3,2.9-2.9,2.9s-2.9-1.3-2.9-2.9V22.9H2.9C1.3,22.9,0,21.6,0,20s1.3-2.9,2.9-2.9 h14.3V2.9C17.1,1.3,18.4,0,20,0s2.9,1.3,2.9,2.9v14.3h14.3c1.6,0,2.9,1.3,2.9,2.9S38.7,22.9,37.1,22.9",
  "arrow_down": "M1.7,16.5L16.1,32c2.2,2.3,5.7,2.3,7.9,0l14.4-15.5c3.5-3.8,1-10.2-4-10.2H5.5C0.6,6.4-1.9,12.8,1.7,16.5",
  "arrow_up": "M38.3,23.5L23.9,8c-2.2-2.3-5.7-2.3-7.9,0L1.6,23.5c-3.5,3.8-1,10.2,4,10.2h28.9C39.4,33.6,41.9,27.2,38.3,23.5",
  "chevron_down": "M22.5,30.6L39,14.2c1.4-1.4,1.4-3.7,0-5c-1.4-1.4-3.7-1.4-5,0L20,23L6.1,9.2c-1.4-1.4-3.7-1.4-5,0s-1.4,3.7,0,5 l16.4,16.4C18.8,32,21.1,32,22.5,30.6",
  "chevron_up": "M17.6,9.2L1.1,25.6c-1.4,1.4-1.4,3.7,0,5c1.4,1.4,3.7,1.4,5,0l14-13.8L34,30.6c1.4,1.4,3.7,1.4,5,0s1.4-3.7,0-5L22.6,9.2 C21.3,7.8,19,7.8,17.6,9.2",
  "delete": "M6.7,35.6c0,2.4,2,4.4,4.4,4.4h17.8c2.4,0,4.4-2,4.4-4.4V13.3c0-2.4-2-4.4-4.4-4.4H11.1c-2.4,0-4.4,2-4.4,4.4 V35.6z M33.3,2.2h-5.6l-1.6-1.6c-0.4-0.4-1-0.6-1.6-0.6h-9.3c-0.6,0-1.2,0.2-1.6,0.6l-1.6,1.6H6.7c-1.2,0-2.2,1-2.2,2.2 c0,1.2,1,2.2,2.2,2.2h26.7c1.2,0,2.2-1,2.2-2.2C35.6,3.2,34.6,2.2,33.3,2.2z",
  "done": "M12.3,29.3L4.1,21C3.1,20,1.6,20,0.7,21c-0.9,0.9-0.9,2.4,0,3.4l10,10c0.9,0.9,2.4,0.9,3.4,0L39.3,9.1 c0.9-0.9,0.9-2.4,0-3.4c-0.9-0.9-2.4-0.9-3.4,0L12.3,29.3z",
  "download": "M22.2,21.5l4.2-4.2c0.9-0.9,2.3-0.9,3.1,0c0.9,0.9,0.9,2.3,0,3.1l-8,8c-0.9,0.9-2.3,0.9-3.1,0l-8-8c-0.9-0.9-0.9-2.3,0-3.1 c0.9-0.9,2.3-0.9,3.1,0l4.2,4.2V2.2C17.8,1,18.8,0,20,0s2.2,1,2.2,2.2V21.5z M35.6,22.2v11.1c0,1.2-1,2.2-2.2,2.2H6.7 c-1.2,0-2.2-1-2.2-2.2V22.2c0-1.2-1-2.2-2.2-2.2S0,21,0,22.2v13.3C0,38,2,40,4.4,40h31.1c2.4,0,4.4-2,4.4-4.4V22.2 c0-1.2-1-2.2-2.2-2.2S35.6,21,35.6,22.2z",
  "edit": "M0,32.1v6.8C0,39.5,0.5,40,1.1,40h6.8c0.3,0,0.6-0.1,0.8-0.3l24.3-24.2l-8.3-8.3L0.3,31.3 C0.1,31.6,0,31.8,0,32.1 M39.4,9c0.9-0.9,0.9-2.3,0-3.1l-5.2-5.2c-0.9-0.9-2.3-0.9-3.1,0L27,4.7l8.3,8.3L39.4,9",
 ...
} 
```

现在，任何消费项目将不再需要指向一些托管的 svg 文件，而是可以通过简单地传递在 *icons.json* 文件中描述的路径来动态地重新创建 svg:

```
import React from 'react';
import { StyledIcon } from './StyledIcon';
import icons from 'hx-icons/icons';

const Icon = ({ className, ...rest }) => {
  const fallback =
    'M79.1545988,120.621554 C75.1703482,120.614249...';

  if (!rest.icon) {
    return null;
  }

  const d = icons[rest.icon] ? icons[rest.icon] : fallback;

  return (
    <StyledIcon {...rest}>
      
        <path d={d} />
      
    </StyledIcon>
  );
}; 
```

## 下一步

我们只是在这条道路的起点，但对接下来会发生什么感到非常兴奋。

我们已经开始测试令人惊叹的 e2e 框架 [Cypress](https://www.cypress.io/) ，看起来很有希望。我们很快会在未来的博客文章中谈到这一点，所以保持联系。

你喜欢这种方式吗？如果你有兴趣在 Helixa 工作，看看我们的[职业页面](https://www.helixa.ai/about-helixa#careers)