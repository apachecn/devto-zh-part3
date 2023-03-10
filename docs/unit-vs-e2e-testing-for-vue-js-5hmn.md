# Vue.js 的单元与 E2E 测试

> 原文：<https://dev.to/anthonygore/unit-vs-e2e-testing-for-vue-js-5hmn>

在开发 Vue.js 应用程序时编写测试可以节省大量时间，否则这些时间会被用来修复 bug。你的应用程序越大越复杂，这就变得越真实。

web 应用程序通常有两种类型的测试:**单元测试**和**端到端测试** (E2E)。

有什么区别？你两个都需要吗？

我们来探索一下。

## 单元测试

测试中“单元”的概念是将代码分解成小的、易于测试的部分。通常，单元是单个函数，但也可以是一个类，甚至是一个复杂的算法。

单元测试的一个关键概念是函数的给定输入应该总是产生相同的输出。

例如，如果我们有一个将两个数相加的函数叫做`add`，我们可以编写一个单元测试来确保我们作为参数提供的一对特殊的数总是返回我们期望的输出。

*add . spec . js*T2】

```
// Function we want to test
const add = (x, y) => x + y;

// Unit test
test("should add two numbers", () => {
  const result = add(2, 3);
  expect(result).toBe(5);
}); 
```

任何时候我们运行那个测试，它不等于 5，我们可以断定一个 bug 进入了我们的代码。

### 组件测试

在大多数 Vue.js 应用程序中，函数并不真正代表应用程序的原子构成。当然，我们可以对我们的方法进行单元测试，但是我们真正关心的是生成的 HTML。

由于这个原因，Vue.js 应用测试中的单元是一个*组件*而不是一个函数。

我们如何测试组件？让我们以这个为例:

*display greeting . js*T2】

```
export default {
  template: `<div>Hello, {{ name }}</div>`,
  props: ['name']
}; 
```

如前所述，对于给定的输入(在本例中是一个道具)，单元测试必须返回一致的输出(在本例中是文本内容)。

使用像 Vue Test Utils 这样的库，我们可以在内存中挂载一个 Vue 组件，并创建一个“包装器”对象。然后，我们可以查询包装器，对呈现的 HTML 做出断言。

*display greeting . spec . js*T2】

```
import displayGreeting from "./displayGreeting.js";

test("displays message", () => {
  const name = "Michael";
  const wrapper = mount(displayGreeting, { propsData: { name } });
  expect(wrapper.text()).toBe(`Hello, ${name}`);
}); 
```

### 快照测试

在上面的例子中，我们使用了`wrapper.text()`来查询组件输出中的文本。

但是，在大多数组件中，测试输出的准确性需要不止一段文本。我们通常希望确保各种元素都存在。

也许测试组件的整个 HTML 输出会更容易？

另一种组件单元测试是一种*快照*测试，你就是这样做的。

它的工作原理是，您只需生成一次组件的输出，并将其写入一个文本文件。例如:

*显示问候.规格. js.snap*

```
exports[`renders correctly 1`] = `<div>Hello, Michael</div>`; 
```

现在，在测试运行的任何时候，如果组件的呈现输出与文件中的不同，测试就会失败。

快照是一种笨拙的工具，但是对于测试显示大量 HTML 的组件来说，它们是很好的工具。

## E2E 测试

E2E(端到端)测试是一种功能测试。与单元测试不同，您不是为了测试而将应用程序分解成更小的部分，而是测试整个应用程序。

E2E 测试与你的应用程序互动，就像一个真正的用户会。例如，您可以编写一个 E2E 测试，它:

1.  加载您的站点
2.  点击“注册”链接
3.  为注册表中的输入提供一些有效的详细信息
4.  单击“注册按钮”。

如果身份验证令牌已经存储在 cookies 中，并且应用程序被重定向到个人资料页面，则该测试应该通过。

### 工具

E2E 测试是在像 Selenium 这样的浏览器自动化驱动程序之上进行的，Selenium 提供了驱动浏览器的 API。

像 Cypress 或 Nightwatch 这样的 E2E 测试框架将为您提供一种为浏览器自动化驱动程序编写 E2E 测试脚本的方法。

下面的代码是您可能在 Nightwatch 中使用的代码，用于执行上一节中描述的测试。即使你从未使用过 Nightwatch，你也可能知道它的功能。

*register . spec . js*T2】

```
"register user": browser => {

  // Navigate to register page
  browser.page.register()
    .navigate()
    .waitForElementPresent(form.selector, 5000);

  // Fill out the form
  register.section.form
    .setValue("@nameInput", "Anthony")
    .setValue("@emailInput", "anthony@test.com")
    .setValue("@passwordInput", "test1234")
    .click("@submitButton");

  // Make assertions
  browser
    .assert.urlEquals(profile.url)
    .getCookie(name, (result) => {
      this.assert.equals(result.name, 'auth');
    }
  });

} 
```

## 单位和 E2E 比较

单位优点:

*   测试运行速度很快
*   测试是精确，并允许您确定确切的问题

单元缺点:

*   为应用程序的每个方面编写测试非常耗时
*   尽管通过了单元测试，整个应用程序可能仍然无法工作

E2E 优点:

*   可以同时隐式测试许多东西
*   E2E 测试向你保证你有一个工作系统

E2E 缺点:

*   运行缓慢-一个站点的运行通常需要 5 到 10 分钟
*   脆弱——一个无关紧要的变化，比如换一个班级，可能会毁掉你的整个 E2E 套件
*   测试不能确定失败的原因

### 裁决

在我看来，单元测试和 E2E 测试的结合是最好的方法。一种类型的缺点可以被另一种类型的优点抵消掉。

例如，E2E 测试不会告诉你失败的根本原因，但单元测试会，而单元测试不会告诉你整个应用程序是否正常工作，而 E2E 测试会。

一起使用这些测试类型会给你的应用程序带来更多的信心，允许你添加特性或者重构而不用担心崩溃。

为 Vue.js 应用程序组合单元测试和 E2E 测试的一般策略如下:

*   为所有组件编写单元测试，包括错误状态。在提交 git 之前运行这些。
*   为网站的关键用例编写 E2E 测试，例如注册、添加到购物车等。在合并到 master 之前运行这些。

如果你想知道更多关于正确测试组合的细节，有很多好的博客文章，比如经典的[编写测试。不太多。主要是整合。肯特·c·多兹。](https://kentcdodds.com/blog/write-tests)

## 奖励:测试工具

那么你准备好开始测试了，你能用什么工具呢？

对于前端单元测试，目前最好的工具是 [Jest](https://jestjs.io/) 。它有许多有用的特性，例如，允许您在测试运行之前编译 TypeScript 和 modern JS。

您可以将 Jest 与允许您挂载和查询 Vue 组件的 [Vue 测试工具](https://vue-test-utils.vuejs.org/)结合使用。

对 E2E 来说，现在最先进的工具是柏树。另一个更基本的也很好用的工具是[夜巡](https://nightwatchjs.org)。

好消息是，使用 Vue CLI 3 很容易将所有这些工具添加到 Vue 应用程序中。

最后，使用类似于 [Travis](https://travis-ci.com/) 或 [Circle CI](https://circleci.com/) 的持续集成工具是一个好主意，它将在云中运行您的测试(特别适合耗时的 E2E 测试),并在所有测试通过的情况下部署您的代码。

测试愉快！

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *