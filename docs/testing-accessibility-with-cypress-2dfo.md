# 用 Cypress 测试可访问性

> 原文：<https://dev.to/stereobooster/testing-accessibility-with-cypress-2dfo>

在[之前的帖子](https://dev.to/stereobooster/accessible-react-accordion-component-4p99)中，我们创建了一个可访问的 React accordion 组件。我们来测试一下。我看不出为这种组件编写单元测试有什么意义。快照测试也没有提供多少价值。我相信端到端(e2e)测试是这里最好的选择(但是对于测试钩子，我更喜欢单元测试)。

会用[柏树](https://www.cypress.io/)试一下。Cypress 使用 headless Chrome，它有一个 devtools 协议，该协议被认为比以前的类似解决方案具有更好的集成性。

## 装柏

一旦你明白如何开始，柏树很容易。它花了我...比我预期的更了解如何开始。他们有大量的文档，很难浏览(至少对我来说)。

> 有时候文档太多和太少一样糟糕

但是经过一些实验后，我明白了。安柏

```
yarn add cypress --dev 
```

第一次运行

```
yarn cypress open 
```

它会创建很多文件。关闭柏树窗户。删除从`cypress/integration`开始的所有内容。

将`cypress.json`添加到项目的根目录。

```
{  "baseUrl":  "http://localhost:3000/"  } 
```

现在，在一个终端中，您可以启动 dev server `yarn start`并在第二个终端中启动 Cypress `yarn cypress open`并开始编写测试。

## 配置柏树

但是如何在 CI 中运行测试呢？为此你需要另一个 npm 包:

```
yarn add --dev start-server-and-test 
```

改变`package.json`

```
"scripts":  {  "test":  "yarn test:e2e && yarn test:unit",  "test:unit":  "react-scripts test",  "cypress-run":  "cypress run",  "test:e2e":  "start-server-and-test start http://localhost:3000 cypress-run"  } 
```

快到了。再加一个包

```
yarn add cypress-plugin-tab --dev 
```

在`cypress/support/index.js`中

```
import "./commands";
import "cypress-plugin-tab"; 
```

添加到`.gitignore`和

```
cypress/screenshots
cypress/videos 
```

现在我们完成了。

## 计划测试

这部分我喜欢。

让我们创建测试文件`cypress/integration/Accordion.js` :

```
describe("Accordion", () => {
  before(() => {
    cy.visit("/");
  });
  // your tests here
}); 
```

它将在测试前打开服务器的根页面(我们将使用开发服务器)。

我们看到了 [WAI-ARIA 创作实践 1.1。](https://www.w3.org/TR/wai-aria-practices/examples/accordion/accordion.html)在之前的帖子中:

*   空格或回车
    *   当焦点在折叠部分的折叠标题上时，展开该部分。
*   标签
    *   将焦点移到下一个可聚焦的元素。
    *   折叠面板中所有可聚焦的元素都包含在页面选项卡序列中。

我们可以简单地复制粘贴它到测试文件 :

```
 describe("Space or Enter", () => {
    xit("When focus is on the accordion header of a collapsed section, expands the section", () => {});
  });

  describe("Tab", () => {
    xit("Moves focus to the next focusable element.", () => {});
    xit("All focusable elements in the accordion are included in the page Tab sequence.", () => {});
  }); 
```

*   `describe` -增加一个层次(可选)。
*   `xit` -将被跳过的测试，一旦我们实施实际测试，我们会将其更改为`it`
*   `it` -一次测试，`it("name of the test", <body of the test>)`

是不是很美？我们可以直接从 WAI-ARIA 规范中复制粘贴测试定义。

## 写作测试

让我们[编写实际的测试](https://github.com/stereobooster/react-accessible-accordion/pull/10/commits/bbe3e2a81ba158afb401a60300807c5c85616394)。

首先，我们需要就测试页面的假设达成一致:

*   只有一个手风琴组件
*   它有三个部分:“第一部分”、“第二部分”、“第三部分”
*   第 2 部分展开，其他部分折叠
*   第 2 部分有一个链接
*   手风琴后面有一个按钮

第一个测试:“当焦点在折叠部分的折叠标题上时，按空格键或回车键，展开该部分”。

让我们在 accordion 中找到第一个面板，并检查它是否已折叠。从规范中我们知道，面板应该有`role=region`参数，如果它是折叠的，它应该有`hidden`参数:

```
cy.get("body")
  .find("[role=region]")
  .first()
  .should("have.attr", "hidden"); 
```

让我们先找到相应的标题。从规范中我们知道它应该有`role=button` param。让我们模仿一下`focus`事件，因为用户将使用`Tab`来访问它。

```
cy.get("body")
  .find("[role=button]")
  .first()
  .focus(); 
```

现在让我们在聚焦元素
中输入空格

```
cy.focused().type("  "); 
```

让我们检查展开的部分(与第一个动作相反):

```
cy.get("body")
  .find("[role=region]")
  .first()
  .should("not.have.attr", "hidden"); 
```

我想这非常简单(如果你熟悉任何 e2e 测试工具，它们都有相似的 API)。

很容易根据鼠标的规格编写所有测试。

### 片石试验

唯一易变的部分是当我们使用 React 来切换焦点时，例如向上箭头、向下箭头、结束、home。在这种情况下，焦点的改变不是即时的(与浏览器相比)。所以我被迫添加了一个小延迟来修复这个问题:

```
describe("Home", () => {
  it("When focus is on an accordion header, moves focus to the first accordion header.", () => {
    cy.contains("section 2").focus();
    cy.focused().type("{home}");
    cy.wait(100); // we need to wait to make sure React has enough time to switch focus
    cy.focused().contains("section 1");
  });
}); 
```

## 结论

我喜欢如何将规范直接转化为 e2e 测试。这是编写 a11y 组件的好处之一——所有的行为都被描述，测试都被计划。我想尝试编写下一个组件 BDD 风格(首先测试)。