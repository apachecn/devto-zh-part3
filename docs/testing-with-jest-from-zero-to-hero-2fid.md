# 用笑话测试:从零到英雄

> 原文：<https://dev.to/bnevilleoneill/testing-with-jest-from-zero-to-hero-2fid>

[![](img/1afcd08234a0d0a0318c634a337142a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YBoAxH_G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/796/1%2AOFDOKm6AnAQOQ-oOiNuQIw.jpeg)

我经常使用 Jest 已经有一段时间了。最初，我像其他测试运行程序一样使用它，但在某些情况下，我使用它只是因为它是 create-react-app 中的默认测试框架。

很长一段时间，我都没有充分发挥 Jest 的潜力。现在，我想告诉你为什么我认为它是最好的测试框架。永远不会。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 快照

什么是快照，为什么它们如此方便？

我第一次看到这个功能时，我以为它仅限于酶和反应单元测试。但其实不是！您可以对任何可序列化的对象使用快照。

让我们来看看。

假设您想测试一个函数是否返回一个非平凡值，就像一个具有一些嵌套数据结构的对象。我发现自己多次这样写代码:

```
const data = someFunctionYouAreTesting()
assert.deepEqual(data, {
  user: {
    firstName: 'Ada',
    lastName: 'Lovelace',
    email: 'ada.lovelace@example.com'
  }
  // etc.
}) 
```

但是，如果一些嵌套的属性不完全是你所期望的…你只是得到一个错误，你需要找到视觉上的差异！

```
assert.js:83
  throw new AssertionError(obj);
  ^

AssertionError [ERR_ASSERTION]: { user:
   { firstName: 'Ada',
     lastName: 'Lovelace!',
     email: 'ada.lovelace@example.com' } } deepEqual { user:
   { firstName: 'Ada',
     lastName: 'Lovelace',
     email: 'ada.lovelace@example.com' } } 
```

如果您正在测试的函数返回随机的东西(例如，当您生成一个随机的 API 键时)，您就不能再使用这种机制了。在这种情况下，您必须手动逐字段检查:

```
const data = someFunctionYouAreTesting()
assert.ok(data.user)
assert.equal(data.user.firstName, 'Ada')
assert.equal(data.user.lastName, 'Lovelace')
assert.equal(data.user.email, 'ada.lovelace@example.com')
// and it goes on... 
```

从测试的角度来看，这更好，但是工作量要大得多。

如果你发现自己在做这些事情，你会爱上快照的！

你会写出这样的东西:

```
const data = someFunctionYouAreTesting()
expect(data).toMatchSnapshot() 
```

…测试第一次运行时，Jest 会将数据结构存储在一个快照文件中，您可以手动打开并验证该文件。每当您再次运行测试时，Jest 将加载快照，并将其与从测试中接收到的数据结构进行比较。如果有任何差异，Jest 将在输出中打印一个彩色的 diff。厉害！

[![](img/6a5dd2c91c62bdbace9d70deaefbe936.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I1qoiobf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/766/0%2AZTerrO_akuDWSV8h.png)

现在，如果我们不想比较整个结构(因为有些字段可以是动态的，或者可以从一个测试到另一个测试发生变化)，该怎么办呢？没问题。

```
const data = someFunctionYouAreTesting()
expect(data).toMatchSnapshot({
  createdAt: expect.any(Date),
  id: expect.any(Number),
}) 
```

这些被称为[属性匹配器](https://jestjs.io/docs/en/snapshot-testing#property-matchers)。

但是还有更多。我发现这种验证数据结构的方法的一个问题是快照文件与测试代码是分离的。因此，有时您需要从一个文件跳到另一个文件，以检查快照是否包含您所期望的内容。没问题！如果快照足够小，您可以使用内联快照。你只需要使用:

```
const data = someFunctionYouAreTesting()
expect(data).toMatchInlineSnapshot() 
```

就是这样！等等…但是快照在哪里？

快照还没有…还没有。第一次运行测试时，Jest 将接受数据结构，而不是将它存储在快照文件中，而是将它放在您的代码中。

是的，它会改变你的测试代码，结果是这样的:

```
const { someFunctionYouAreTesting } = require("../src/app");
test("hello world", () => {
  const data = someFunctionYouAreTesting();
  expect(data).toMatchInlineSnapshot(`
Object {
  "user": Object {
    "email": "ada.lovelace@example.com",
    "firstName": "Ada",
    "lastName": "Lovelace",
  },
}
`);
}); 
```

这让我震惊..

[![](img/089fc7d68ba5b58d49e79122c1d80f11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tJ6xN93p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/64/0%2AYd51W5jRLuS-AZbr)

..我喜欢它。无缝更改代码的开发工具是一个简单而优雅的解决方案，在其他场景中会非常有用。想象一下，有一个 react/angular/vue 开发模式，您可以在浏览器中可视化地编辑组件，代码会为您更新以匹配这些更改！

顺便说一下，如果测试不够小，不能使用内联快照，您仍然可以获得一些帮助。如果您使用 Visual Studio 代码和这个扩展一起使用，您可以在悬停时看到快照(这非常有用，尽管它有一些限制)。

### 互动模式

一开始，我认为交互模式只是许多 CLI 应用程序所具有的典型手表功能的一个花哨术语。但后来我学到了一些东西。

Jest 集成了 Git 和 Mercurial。默认情况下，监视模式将只运行受自上次提交以来所做更改影响的测试。这很酷，也让我写了更多的原子提交。如果您想知道 heck Jest 是如何知道提交更改会影响哪些测试的，您并不孤单。

Jest 做的第一件事是加载测试，从而加载解析 requires()和 imports 的应用程序的源代码，以生成相互依赖关系图。

但是使用 Git 或 Mercurial 并不是限制每次运行测试数量的唯一方法。当我对源代码进行修改时，我会看到许多失败的测试，我会关注最简单的失败测试。你可以通过使用 *test.only* 来做到这一点，但是还有一个更好的方法(我尤其不喜欢 *test.only* 或者 *test.skip* ，因为很容易忘记它，把它留在你的代码中)。

“交互方式”更加优雅便捷。无需编辑测试代码，您就可以限制测试以不同的方式运行。

让我们来看看。

最简单的方法是点击 *t* 并输入测试名称。如果你有测试“你好世界”，点击 *t* ，写*你好世界*，点击*回车。*

[![](img/2fbbea1be51d49749f949d6249ab4879.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WAmqFgok--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/432/0%2Ahzc7dByCxjzgYJXt.gif)

嗯，这在大多数情况下都有效，如果你有一个测试(' hello world 2 '，…它也会运行，因为你输入了一个正则表达式。为了避免这种情况，我通常在模式的末尾添加一个$。

在有许多集成测试触及数据库的项目中，我发现运行测试仍然很慢。为什么？

事情是，通过测试名过滤并不能阻止所有的在()之前的*和()*之后的*回调在所有其他测试中运行。通常，在集成测试中，这些回调是您放置重要内容的地方，比如打开和关闭到数据库的连接。*

所以，为了防止这种情况，我通常也按文件名过滤。只需点击 *p* (对于*路径*，并输入包含测试的文件名。你会发现测试现在运行得快多了(要返回，只需点击 *t* 并通过点击 *enter 键来清理过滤器，*对带有 *p* 和 *enter* 的文件名做同样的事情)。

另一个超级方便的功能是升级。当你看到差异，你看到新的快照是好的，旧的是过时的，只需点击 *u* (用于*升级*)，快照将被覆盖！

[![](img/e9afc4b928152b7b759b6a7e8e0ccdd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d0Rbgjw7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/765/0%2AVrZeHrqiF-DiIQTa.gif)

两个更有用的选项是 *a* 运行所有测试，以及 *f* 再次运行失败的测试。

### 包含电池

我喜欢的另一件事是 Jest 是一个包含电池的框架。这意味着你通常不需要添加插件或库来添加通用功能。它只是附带的！一些例子:

*   当调用 Jest 时，您可以从您的测试中获得覆盖率报告，并且能够在几个内置报告器或自定义报告器之间进行选择。您甚至可以设置一个覆盖率阈值，这样如果没有达到这个阈值，您的测试(以及您的 CI)就会失败。非常适合在代码中保持良好的测试覆盖率。
*   添加 notify ，当测试运行程序完成时，您将获得桌面通知。如果您有数千个测试，它们可能需要一段时间才能完成。只要添加这个标志，你就可以优化你的时间。
*   为了开始编写强大而有用的断言，你不需要在你的项目中添加一个断言库。你有一个广泛的[期望功能](https://jestjs.io/docs/en/expect)已经内置，准备好与有趣的功能一起使用，例如我们在快照功能中也看到的彩色差异。
*   你不需要一个库来模仿功能或服务。你有很多实用程序来[模拟函数](https://jestjs.io/docs/en/mock-functions)和[模块](https://jestjs.io/docs/en/manual-mocks)并检查它们是如何被调用的。

### 用 VSCode 调试

用 VSCode 调试 Jest 测试非常简单。

只需转到调试器选项卡，然后单击带有小红点的齿轮图标。单击它并创建一个 Node.js 启动文件。现在用你在下面找到的食谱替换内容。

这个配方基于一个单独的[配方](https://github.com/Microsoft/vscode-recipes/tree/master/debugging-jest-tests)，它包括两个配置:一个用于运行所有的测试，一个用于只运行当前的测试文件。我添加了一个额外的配置，允许您选择(在文本编辑器中)一个测试的名称，并只运行那个测试！我还添加了 watch 标志，这样您就可以编辑您的代码或测试，保存它，测试将很快重新运行。这是可能的，因为 Jest 自己加载测试，而不是底层系统(Node.js 运行时)。

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Jest All",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "args": ["--runInBand"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "windows": {
        "program": "${workspaceFolder}/node_modules/jest/bin/jest",
      }
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Jest Current File",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "args": ["${relativeFile}"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "windows": {
        "program": "${workspaceFolder}/node_modules/jest/bin/jest",
      }
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Jest Selected Test Name",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "args": ["${relativeFile}", "-t=${selectedText}$", "--watch"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen",
      "windows": {
        "program": "${workspaceFolder}/node_modules/jest/bin/jest",
      }
    }
  ] 
```

### 结论

Jest 不仅仅是一个测试运行程序，它是一个完整的测试框架，将测试带到了另一个层次。它不仅超级强大，而且简单易用。如果你还没有使用它，试一试，你将不会回头。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[用笑话测试:从零到英雄](https://blog.logrocket.com/testing-with-jest-from-zero-to-hero-85ce0e9cc953/)最先出现在[博客](https://blog.logrocket.com)上。