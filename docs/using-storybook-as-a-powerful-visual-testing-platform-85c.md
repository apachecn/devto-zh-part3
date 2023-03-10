# 使用故事书作为视觉测试平台

> 原文：<https://dev.to/emasuriano/using-storybook-as-a-powerful-visual-testing-platform-85c>

我使用这种测试策略(不替代其他策略)的经验，以及与我当前开发工具的集成。

我的待办事项列表中一直都有视觉测试，直到几周前，我终于决定将它标记为完成。在这篇文章中，我将分享我使用这种测试策略的经验(它不会取代其他的测试策略)以及与我当前的开发工具[故事书](https://storybook.js.org/)的集成。

如果你不知道故事书是什么，这是他们在官网上提供的定义:

> Storybook 是一个开源工具，用于独立开发 React、Vue 和 Angular 的 UI 组件。它使构建令人惊叹的 ui 变得有条理和高效。

关于**视觉测试**，这是一种测试方法，包括拍摄真实的图片，然后与之前的版本进行比较。这种比较是通过比较两张图片并检查像素是否匹配来进行的。

如果你已经知道 [*快照测试*](https://jestjs.io/docs/en/snapshot-testing) 的概念，这个也是一样的。区别在于你比较的是一张图片，而不是一些结果代码。

<figure>[![Example of Visual Testing](img/332cd64a3bebf5b45394aba80b4ba661.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GIkhwpuU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AQv-mDozlY2FZfkdr.png) 

<figcaption>视觉测试差异的例子</figcaption>

</figure>

如果你在想“但是这两个概念是如何联系在一起的？”。Storybook 坚持独立开发组件，这是进行视觉测试的最佳方案，以避免图像中不必要的差异。对这种方法的一种思考方式就像是，**故事书中定义的每个故事**在我们的应用程序中都是一个**视觉测试**。

在检查实现之前，最后一件事是，我尝试过一些优秀的服务，它们工作得非常完美，比如[珀西](https://percy.io)、[应用工具](https://applitools.com/)等等。在这篇文章中，我想展示一下实现可视化测试工作流的*手工方式*，它对于提到的服务有自己的优缺点。

[![Show time](img/8db856815ccf68977f27c44217cfc0f0.png)](https://i.giphy.com/media/UDjF1zMreMld6/giphy.gif)

让我们从一个全新的项目开始，用 [create-react-app](https://github.com/facebook/create-react-app) 引导它，并使用它的 CLI 安装 Storybook。

```
$ npx create-react-app visual-testing-with-storybook
$ cd visual-testing-with-storybook
$ npx -p @storybook/cli sb init 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，您可以通过运行`yarn storybook`来检查一切是否设置正确，您应该会看到默认情况下创建的两个故事的主页。

<figure>[![](img/e9ce92a2e1158b7670384bc9da76579c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cAHgQ7wP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A31UV2T30NYUI0lIrm7JuOA.png) 

<figcaption>故事书首页</figcaption>

</figure>

现在让我们创建一个简单的测试组件。我决定创建一个名为`DuplicationButton`的简单按钮，用户每次点击它时，props 提供的`children`都会被复制。不是真的有用，但它将作为一个很好的例子！

```
import React, { useState } from 'react';

const ButtonStyle = {
  padding: '10px',
  borderRadius: '5px',
  fontSize: '16px',
  border: 'pink 3px solid',
};

const DuplicationButton = ({ children }) => {
  const [count, setCount] = useState(1);

  return (
    <button style={ButtonStyle} onClick={() => setCount(count + 1)}>
      {new Array(count).fill(children)}
    </button>
  );
};

export default DuplicationButton; 
```

Enter fullscreen mode Exit fullscreen mode

让我们为组件添加一些故事。

```
import React from 'react';
import { storiesOf } from '[@storybook/react](http://twitter.com/storybook/react)';
import DuplicationButton from './DuplicationButton';

storiesOf('DuplicationButton', module)
  .add('Good', () => (
    <DuplicationButton>
      <span role="img" aria-label="angel">
        😇
      </span>
    </DuplicationButton>
  ))
  .add('Bad', () => (
    <DuplicationButton>
      <span role="img" aria-label="devil">
        😈
      </span>
    </DuplicationButton>
  )); 
```

Enter fullscreen mode Exit fullscreen mode

这是它在故事书里的样子。

<figure>[![Demo DuplicationButton stories](img/3528a1e7886438bc2b89f0dea448b36f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CNcfKxVX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/666/1%2AHAYpOidYjbMMumg3V9yFcA.gif) 

<figcaption>演示复制按钮故事</figcaption>

</figure>

### 基于故事生成测试🧪

为了在每个故事中实现一个测试，Storybook 中有一个非常有用的插件叫做 [storyshots](https://www.npmjs.com/package/@storybook/addon-storyshots) 。为了安装它，你需要运行:

```
$ yarn add -D @storybook/addon-storyshots react-test-renderer 
```

Enter fullscreen mode Exit fullscreen mode

然后在初始化`storyshots`的地方创建一个测试文件。姑且称之为`storyshots.test.js`。

```
// src/storyshots.test.js 
**import** initStoryshots **from**'@storybook/addon-storyshots';  

initStoryshots({ _/\* configuration options \*/_ }); 
```

Enter fullscreen mode Exit fullscreen mode

为了运行它，执行`yarn test`。现在，对于每个故事，都有一个使用*快照*的测试，您可以在其中检查故事的输出(组件渲染)是什么。这些*快照*将在我们每次运行测试时自动生成，如果它们与之前的有差异，测试将会失败。

<figure>[![Test result with storyshots](img/747216c5246cf237b12a67e2095da81f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ccfM3qCv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1008/1%2Ad2UMLYiwjtxJMMzr2VGiog.png) 

<figcaption>测试结果与故事镜头</figcaption>

</figure>

[![Visual Magic](img/1c84c354bf1d340d96202dd6a4a345f1.png)](https://i.giphy.com/media/7SKOwf1nD6j6XhfLMG/giphy.gif)

#### 我们来目测一下！🌈

在上面的代码中，函数`initStoryshots`接受一个带有选项`test`的配置对象，该选项允许我们改变每个故事/测试的比较方法。

幸运的是，有一个名为[story shot-puppet er](https://www.npmjs.com/package/@storybook/addon-storyshots-puppeteer)的附加组件，它作为名称状态创建了一个浏览器实例，导航到故事，拍摄照片并与之前的照片进行比较！为了安装它:

```
$ yarn add -D @storybook/addon-storyshots-puppeteer 
```

Enter fullscreen mode Exit fullscreen mode

然后在`storyshots`文件中，您需要覆盖`test`与来自木偶师附加组件的`imageSnapshot`的比较。此外，需要指定运行 storybook 的 URL，以便知道导航到哪里。

```
// src/storyshots.test.js
import initStoryshots from '[@storybook/addon-storyshots](http://twitter.com/storybook/addon-storyshots)';
import { imageSnapshot } from '[@storybook/addon-storyshots-puppetee](http://twitter.com/storybook/addon-storyshots-puppetee)r';

initStoryshots({
  test: imageSnapshot({ storybookUrl: '[http://localhost:9009/'](http://localhost:9009/') }),
}); 
```

Enter fullscreen mode Exit fullscreen mode

有一点要提一下，现在我们的测试依赖于运行 storybook 的一个实例。对于这种情况，我建议同时管理两个终端:在一个终端上运行 yarn storybook，在另一个终端上运行 yarn test。

<figure>[![Running visual testing along with Storybook](img/58df3a48f9b4c5494f6c390af55fe71b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0fvGga0H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3RNCgIJ0rbkGdwcukmdcqA.png) 

<figcaption>随着故事书运行视觉测试</figcaption>

</figure>

旧的快照已经过时了(你可以安全地删除它们)，因为现在我们有了一个名为`\_\_image\_snapshots\_\_`的新文件夹，每个故事都有一张照片。每次组件改变它所呈现的内容时，测试都会失败，您可以通过存储的图像快照和新图像之间的视觉差异来检查它。

在下面的区别中，我把按钮的`border-color`从`pink`改成了`blue`。原来的图像将在左边，新的在右边，中间的区别是两者都用红色变化。

<figure>[![Visual diffing in action](img/d051358b84e55b3a52ac460934f6dd6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B9GX7GTX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7Bwxj5LlRektVu42ZI5qKw.png) 

<figcaption>动作视觉差异</figcaption>

</figure>

### 奔上词坛的⚙️

让两个终端同时开放，这是我们只能在开发中做的事情。但是当涉及到自动化这项任务时，事情就有点棘手了。

幸运的是，有人考虑到了这个问题，并制作了一个名为[启动-服务器-测试](https://github.com/bahmutov/start-server-and-test)的`npm`包，它就是这么做的！您需要指定命令*“启动服务器”*，一旦服务器启动并运行，它将执行您指定的*“测试”*命令，一旦该过程完成，它将终止服务器。

```
yarn add start-server-and-test 
```

Enter fullscreen mode Exit fullscreen mode

在`package.json`中，您需要创建一个新的脚本来启动 storybook，然后监听直到 [http://localhost:9009](http://localhost:9009) 启动并运行并执行测试。

```
{  "scripts":  {  "test":  "react-scripts test --coverage",  "storybook":  "start-storybook -p 9009 -s public",  "test:ci":  "start-server-and-test storybook [http://localhost:9009](http://localhost:9009) test"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![Test CI output](img/eae267f8ec52f8e3e0c91a9cc446b447.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aJrQvkz1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKekXGREL7z3wIjT3q_Q9cw.png) 

<figcaption>测试 CI 输出</figcaption>

</figure>

### 互动视觉测试🕺

有时候你想在拍照前和故事互动。为了做到这一点，您需要遵循一种不同于前面所示的方法。您可以通过使用名为 [jest-image-snapshot](https://github.com/americanexpress/jest-image-snapshot) 的客户 jest 匹配器和任何端到端框架来实现这一点。在这种情况下，媞[的傀儡师](https://github.com/GoogleChrome/puppeteer)。

```
yarn add -D jest-image-snapshot pupetter 
```

Enter fullscreen mode Exit fullscreen mode

为了启用`jest-image-snapshot`，你需要从`jest`扩展函数`expect`。用`create-react-app`实现这一点的[推荐方式](https://facebook.github.io/create-react-app/docs/running-tests#src-setuptestsjs)是在`src`中创建一个名为`setupTests.js`的文件。这个文件将在所有测试开始之前加载，这将使我们能够使用这个自定义匹配器。

```
// src/setupTests.js
import { toMatchImageSnapshot } from 'jest-image-snapshot';

expect.extend({ toMatchImageSnapshot }); 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们要检查 DuplicationButton 的行为时创建测试文件，让我们检查一下当用户点击它两次时它是什么样子的。

```
// src/DuplicationButton.test.js
import puppeteer from 'puppeteer';

describe('<DuplicationButton />', () => {
  let page;

  beforeAll(async () => {
    // init puppeteer page
    let browser = await puppeteer.launch();
    page = await browser.newPage();
  });

  it('should duplicate content after clicking', async () => {
    // navigate to fullscreen story
    await page.goto(
      '[http://localhost:9009/iframe.html?id=duplicationbutton--good'](http://localhost:9009/iframe.html?id=duplicationbutton--good'),
    );

    // click on the button
    await page.click('button');

    // take screenshot and compare
    const screenshot = await page.screenshot();
    expect(screenshot).toMatchImageSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了运行这个测试，我们需要启动 Storybook，或者您可以直接使用`yarn test:ci`来完成它！屏幕截图如下所示:

<figure>[![Interactive visual testing](img/3c4fcb96963bd651ea02293ab6159835.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H1qjE2Px--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AWc6LOMI9Ggo4dOiPBpFdZg.png) 

<figcaption>互动视觉测试</figcaption>

</figure>

### 我从事视觉测试的经历🙋‍♂️

与所有新技术/框架一样，我首先尝试了可视化测试，并在一个附带项目中发现了可能的改进。这个项目本身是一个用 React 和[样式组件](https://www.styled-components.com/)制作的天气图标的集合，叫做[天气样式图标](https://github.com/EmaSuriano/weather-styled-icon)。

<figure>[![weather-styled-icon showcase](img/06bc14230960e1bfa9d44a654a7212cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oCICXHMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/634/0%2ALhH0pCh9S5QYKROB.gif) 

<figcaption>天气-图标展示区</figcaption>

</figure>

我对这个库做的第一个发布，我用`enzyme`写了所有的测试，遵循一个结构化测试策略。简单地说，我用`mount`渲染一个图标，然后运行检查一个节点是否存在，为了检查它“看起来”如何，我运行了结果样式的`expect.toMatchSnapshot`，正如你可以想象的，这种测试方式非常耗时，但尽管如此，我还是能够完成所有的测试。

当我决定将`styled-components`的版本从 v3 更新到 v4 时，问题出现了，因为我想开始使用一些很酷的新 API，比如`ThemeProvider`或`styled` API 来设计现有的`styled-component`组件。

在我对代码做了所有的修改后，我所有的测试都被破坏了，因为我移动、添加和删除了组件，而且我修改了大部分组件的内部实现。此时，我意识到我正在测试组件的实现，而不是我所期望的输出。

所以我决定尝试视觉测试，因为这似乎是最适合我的情况的测试策略，在这种情况下，我想检查图标和它们的变化看起来是什么样子。我遵循了上面已经解释过的相同步骤，最终我用更少的代码完成了一套更好的测试。这是合并拉取请求的行差！

<figure>[![Visual testing lines diff weather-styled-icon](img/616b1f20aac141c536744154167f7853.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n124NSWT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAglowTdf-p9RC8T3608v9g.png) 

<figcaption>目测线条差异天气-图标</figcaption>

</figure>

### 做与不做的✍️

我认为视觉测试是一种很好的测试方式，我强烈建议你至少在一个兼职项目中尝试一下，你可以玩玩它，看看你是否喜欢它。但是，我想强调一些关于这个话题应该做和不应该做的重要事情:

*   ❌不用代码检查你的组件的风格，取而代之的是，拍下组件的真实图像。
*   ✅:如果你在你的开发工作流程中使用 Storybook，你可以对所有的故事进行一次可视化测试，而不需要任何实际的努力。
*   ❌视觉测试不会取代其他测试策略，它只是在你的应用程序中增加了一个测试层。
*   ✅:你可以很容易地将它与你当前的端到端测试集成起来。

### 有用的资源📓

*   [链接到示例项目](https://github.com/EmaSuriano/visual-testing-storybook)
*   [天气风格图标](https://github.com/EmaSuriano/weather-styled-icon)
*   [自动化视觉测试—故事书文档](https://storybook.js.org/docs/testing/automated-visual-testing/)
*   [讲笑话的木偶师](https://jestjs.io/docs/en/puppeteer)
*   [结构测试—故事书文档](https://storybook.js.org/docs/testing/structural-testing/)
*   [在](https://facebook.github.io/create-react-app/docs/running-tests#src-setuptestsjs) [创建-反应-应用](https://facebook.github.io/create-react-app/docs/running-tests#src-setuptestsjs)中扩展 Jest 配置