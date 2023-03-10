# Cucumber.js with TypeScript

> 原文：<https://dev.to/denolfe/cucumber-js-with-typescript-44cg>

> 最初发布在我的博客上，时间是[https://elliotdenolf.com/posts/cucumberjs-with-typescript](https://elliotdenolf.com/posts/cucumberjs-with-typescript)

Cucumber.js 是 [Cucumber](https://cucumber.io/) 的 JavaScript 实现。为 Cucumber 编写自动化测试的主要好处是它们是用简单的英语编写的，因此任何非技术人员都可以阅读这些场景并知道正在测试什么。这在大型组织中非常强大，因为它允许开发人员、测试人员和业务涉众更好地交流和协作。

这篇文章将介绍如何使用 TypeScript 和 [cucumber-tsflow](https://www.npmjs.com/package/cucumber-tsflow) 建立一个基本的 Cucumber.js 套件。Cucumber-tsflow 是一个包，它允许我们利用 TypeScript 的[装饰器](https://www.typescriptlang.org/docs/handbook/decorators.html)，这使得步骤定义代码更加清晰。

第一步是安装我们的依赖项:

```
npm i -D cucumber cucumber-tsflow cucumber-pretty ts-node typescript chai
npm i -D @types/cucumber @types/chai 
```

Enter fullscreen mode Exit fullscreen mode

为了让装饰器正确编译，在你的`tsconfig.json`中`experimentalDecorators`也必须被设置为`true`。

cucumber 测试的两个主要组件是特征文件和步骤定义。让我们首先创建一个`features`目录，然后在其中创建一个名为`bank-account.feature`的文件。我们的例子将测试一个银行账户的基本功能。

```
# features/bank-account.feature
Feature: Bank Account

  Scenario: Stores money
    Given A bank account with starting balance of $100
    When $100 is deposited
    Then The bank account balance should be $200 
```

Enter fullscreen mode Exit fullscreen mode

这定义了将钱存入银行账户的单一场景。接下来，我们将创建一个名为`step-definitions`的目录，并在其中创建一个名为`bank-account.steps.ts`的文件。

```
import { binding, given, then, when} from 'cucumber-tsflow';
import { assert } from 'chai';

@binding()
export class BankAccountSteps {
  private accountBalance: number = 0;

  @given(/A bank account with starting balance of \$(\d*)/)
  public givenAnAccountWithStartingBalance(amount: number) {
    this.accountBalance = amount;
  }

  @when(/\$(\d*) is deposited/)
  public deposit(amount: number) {
    this.accountBalance = Number(this.accountBalance) + Number(amount);
  }

  @then(/The bank account balance should be \$(\d*)/)
  public accountBalanceShouldEqual(expectedAmount: number) {
    assert.equal(this.accountBalance, expectedAmount);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在利用 [cucumber-tsflow](https://www.npmjs.com/package/cucumber-tsflow) 包，它为我们的`Given`、`When`和`Then`步骤公开了一些非常有用的[装饰器](https://www.typescriptlang.org/docs/handbook/decorators.html)。每个步骤中的代码都相当简单。`Given`步骤初始化`accountBalance`，`When`步骤增加余额，`Then`步骤断言其值。

需要特别注意的是:这个文件导出了一个带有`@binding()` decorator 的类，cucumber-tsflow 需要这个类来选择步骤。每个步骤定义还必须有一个`@given`、`@when`或`@then`装饰器。这些装饰器将一个正则表达式作为参数，这就是特征文件中的行如何映射到代码。另外，请注意，表达式中有捕获组，用于从文本中捕获值，并随后作为参数传递给函数。

Cucumber 使用带有一系列命令行开关的`cucumber-js`命令运行。然而，这可以有选择地放到项目根目录下的一个`cucumber.js`文件中。在项目的根目录下创建一个`cucumber.js`文件，内容如下:

```
// cucumber.js
let common = [
  'features/**/*.feature', // Specify our feature files
  '--require-module ts-node/register', // Load TypeScript module
  '--require step-definitions/**/*.ts', // Load step definitions
  '--format progress-bar', // Load custom formatter
  '--format node_modules/cucumber-pretty' // Load custom formatter
].join('  ');

module.exports = {
  default: common
}; 
```

Enter fullscreen mode Exit fullscreen mode

将配置放在这个文件中允许我们简单地将配置文件名传递给`cucumber-js`(在我们的例子中是`default`)，而不是一长串参数。这个文件构建了所有的命令行参数，连接它们，然后将它们导出到一个命名的属性下。让我们给我们的`package.json`添加一个 npm 脚本，这样我们就可以轻松地运行它。

```
//  package.json  {  //  ...  "scripts":  {  "test":  "./node_modules/.bin/cucumber-js -p default"  },  //  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

您的项目结构现在应该是这样的:

```
.
|-- cucumber.js
|-- features
| `-- bank-account.feature
|-- package.json
|-- step-definitions
| `-- bank-account.steps.ts
`-- tsconfig.json 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行`npm test`时，`node_modules`中的`cucumber-js`将被执行，其中的`-p default`开关表示从我们之前创建的`cucumber.js`文件中导出的默认概要文件。

输出应该类似于这样:

```
Feature: Bank Account

  Scenario: Stores money
    Given A bank account with starting balance of $100
    When $100 is deposited
    Then The bank account balance should be $200

1 scenario (1 passed)
3 steps (3 passed)
0m00.004s 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你起床了，拿着黄瓜和打字稿！

### 链接

*   **[本例源代码为](https://github.com/denolfe/blog-code-samples/tree/master/posts/cucumber-with-typescript)**
*   [黄瓜](https://cucumber.io/)
*   [黄瓜-草莓](https://www.npmjs.com/package/cucumber-tsflow)
*   [特征文件语法参考(小黄瓜)](https://docs.cucumber.io/gherkin/reference/)