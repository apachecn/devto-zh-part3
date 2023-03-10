# 使用 TestCafe 进行更好的 E2E 测试

> 原文：<https://dev.to/pluralsight/better-e2e-testing-with-testcafe-2h6d>

> 如果你想了解更多关于 TestCafe 的信息，请查看我的课程[使用 TestCafe 进行端到端 Web 测试:在 Pluralsight](https://pluralsight.pxf.io/Od2GW) 上开始。

TestCafe 是一个节点。用于在浏览器中运行端到端测试的 JS 工具。

## 我为什么喜欢 TestCafe

使用 TestCafe，我可以用 Typescript 或最新的(ES2017) JS 编写我的所有代码。当我能够以编写应用程序代码的同样方式编写 E2E 测试时，我认为这是一个巨大的胜利。我在我的应用程序代码中使用 Typescript 来减少对我的代码实际上在做什么的混淆，以便我的团队的其他成员能够更好地理解我的代码。在 E2E 测试中使用这些相同的原理似乎是显而易见的。另外，智能感知:)

能够在测试中使用更现代的 Javascript，我最喜欢的一点是使用 async/await。因为在测试期间运行的大多数动作都会返回承诺，所以我的代码看起来像这样:

```
// good
test('Should be able to login', async t => {
  await t.typeText(Selector('#username'));
  await t.click(Selector('#login-button'));
  //Or chain the calls
  await t.typeText(Selector('#username')).click(Selector('#login-button'));
}); 
```

不使用回调:

```
// booo!
test('Should be able to login', t => {
  t.typeText(Selector('#username')).then(() => {
    t.click(Selector('#login-button'));
  });
}); 
```

Testcafe 能够自动等待页面加载、XHR 请求完成和元素出现，而无需我设置超时，这让我的生活变得轻松多了。我还喜欢减缓测试执行速度的特性，这样你就可以很容易地看到浏览器中发生了什么。

## 速度设定

要开始，你所要做的就是用 npm 安装 test cafe:

```
$ npm install -g testcafe 
```

Node.js 是您唯一的依赖项。我最喜欢的一点是，你不需要安装 webdriver！您只需 npm 安装 TestCafe，就可以开始了。

每个测试都从一个夹具开始。夹具基本上是一个测试类别，每个测试文件都需要一个或多个夹具:

```
fixture(fixtureName);
// or
fixture`fixtureName`; 
```

现在您可以开始添加测试:

```
fixture`Login Functionality`;
test('Should be able to login', async t => {
  await t
    .typeText(Selector('#username'))
    .click(Selector('#login-button'))
    .expect(true)
    .ok(); //This is just an example assertion. Don't do   this in real tests.
});
fixture`Logout functionality`;
test('Should be able to logout', async t => {
  await t
    .click(Selector('#logout-button'))
    .expect(true)
    .ok();
}); 
```

这里有一个测试 API 的链接([https://devexpress . github . io/test cafe/documentation/test-API/](https://devexpress.github.io/testcafe/documentation/test-api/))显示了所有可用的测试功能。

## 页面型号

设置页面模型仍然是浏览器内测试的推荐做法。页面模型基本上是一个保存浏览器元素引用的文件(按钮，链接，任何你测试时会与之交互的东西)。我也喜欢将页面交互方法添加到页面模型中。这样做将会使你的测试更加简洁。

```
import { Selector } from 'testcafe';
export default class HomeModel {
  constructor() {
    this.signInLink = Selector('#sign-in');
    this.username = Selector('input[type=“text”]');
    this.password = Selector('.password');
    this.submitButton = Selector('#submit');
    this.forgotPasswordLink = Selector('.forgot-password');
  }
  async loginUser(t: TestController) {
    await t
    .typeText(this.username, 'someUser')
    .typeText(this.password, 'password123')
    .click(this.submitButton);
  }
}
//Or if you prefer Typescript
export default class HomeModel {
  signInLink: Selector;
  forgotPasswordLink: Selector;

  constructor() {
    this.signInLink = Selector('#sign-in');
    this.forgotPasswordLink = Selector('.forgot-password');
  }
} 
```

在上面的页面模型中，我们使用 TestCafe 的选择器 API 在构造函数中定义页面元素。然后，我们有一个与这些页面元素交互的 loginUser 方法。这意味着在我们的测试中，我们可以只写:

```
import HomeModel from './home-model';
const Home = new HomeModel();
… //Test fixture code
test('Loggin in', async t => {
  await Home.loginUser(t);
  await t.expect(…).ok();
}); 
```

我们这样做的原因是，如果应用程序代码发生变化，您应该只需要更新您的页面模型，而不是所有的测试。选择器 API 本身相当健壮，但是如果您使用 React、AngularJS、Angular、Vue 或 Aurelia 之类的框架，TestCafe 可以满足您的需求([https://devexpress . github . io/test cafe/documentation/test-API/selecting-page-elements/framework-specific-selectors . html](https://devexpress.github.io/testcafe/documentation/test-api/selecting-page-elements/framework-specific-selectors.html))。社区开发的每个框架都有特定的选择器库。

如果需要，您仍然可以远程运行您的测试。我们使用 SauceLabs，但是我确信在那个领域的其他玩家也一样好。

如果你正在运行浏览器/e2e 测试(你真的应该这样做)，那么我推荐你试试 Testcafe。它可以快速设置并与最流行的 JS 框架一起工作。如果您想要强类型测试，您可以使用 Typescript 而不需要任何额外的 Typescript 配置(它捆绑在 TestCafe 包中)。

我希望这篇文章能帮助那些寻找 E2E 测试他们的应用程序的人。如果我做错了什么或者你有任何问题，请随时发表评论。佛感谢阅读:)

在推特上关注我😀