# 用 Jasmine 对 JavaScript 进行单元测试

> 原文：<https://dev.to/daniel_werner/unit-testing-javascript-with-jasmine-14p4>

嗯，JavaScript……就是那个在浏览器里运行的，包含很多 **$()** 函数调用的乱七八糟的代码，对吧？**没有**。至少不应该。JavaScript 是一种功能全面的语言，所有良好的开发实践都可以并且应该用于 JavaScript 开发，包括但不限于面向对象的架构、设计模式和自动化测试(单元测试、集成测试)。

在本文中，我将展示如何在 JavaScript 中设置 Jasmine 进行单元测试。更准确地说，Jasmine 是一个行为驱动的开发框架，而不是测试驱动的开发，但是本质和目标是相同的:对代码的测试越多越好。

## 安装

让我们看看如何安装和配置 Jasmine:

用 npm 安装:

```
npm install --save-dev jasmine 
```

初始化:

```
node node\_modules/jasmine/bin/jasmine init 
```

将 jasmine 设置为您的包中的测试脚本

```
"scripts": { "test": "jasmine" } 
```

使用 npm 运行测试:

```
npm test 
```

如果你想在浏览器中看到测试结果，你可以安装 Jasmine 的独立版本，在这里找到发布页面:[https://github.com/jasmine/jasmine/releases](https://github.com/jasmine/jasmine/releases)。

下载这个包，解压，并在 specs.html 中添加以下内容:

```
\<link rel="shortcut icon" type="image/png" href="jasmine/lib/jasmine-{#.#.#}/jasmine\_favicon.png"\>\<link rel="stylesheet" type="text/css" href="jasmine/lib/jasmine-{#.#.#}/jasmine.css"\>\<script type="text/javascript" src="jasmine/lib/jasmine-{#.#.#}/jasmine.js"\>\</script\>\<script type="text/javascript" src="jasmine/lib/jasmine-{#.#.#}/jasmine-html.js"\>\</script\>\<script type="text/javascript" src="jasmine/lib/jasmine-{#.#.#}/boot.js"\>\</script\> 
```

## 写作第一关考试

我们的第一个示例测试如下所示:

```
describe("Basic suite", function() {   it("ensures jasmine is working", function() {       expect(true).toBe(true);   });}); 
```

看起来它没有测试任何东西，但是如果它运行成功，就意味着您已经正确地配置了 Jasmine。我们可以看到上述测试的三个主要部分:

*   **描述**功能将相关的测试用例分组到测试套件中。
*   **it** 函数代表一个测试用例。描述应该描述要测试的功能的期望行为。
*   **expect** 函数包含测试的断言。

你可以在 Jasmine 教程页面找到更多的例子和详细的解释:[https://jasmine.github.io/tutorials/your_first_suite](https://jasmine.github.io/tutorials/your_first_suite)

很容易，不是吗？你可能认为编写测试很无聊，很耗时，甚至没有必要。截止日期总是很紧，谁有时间写测试？是的，这是真的，但是进行测试可以让你在不破坏现有功能的情况下自信地修改/重构你的代码，这可以为你以后节省很多调试时间。

在接下来的博文中，我将展示一些关于模拟单元测试输入数据的技巧和诀窍(特别是对于像 datetime 或 geolocation 这样的内置对象)。

同意吗？不同意？请在下面的评论区告诉我。

用 Jasmine 测试 JavaScript 的帖子[最早出现在](https://42coders.com/unit-testing-javascript-with-jasmine/) [42 编码员](https://42coders.com)上。