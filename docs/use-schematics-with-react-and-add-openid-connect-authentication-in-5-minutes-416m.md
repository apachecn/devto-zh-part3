# 使用 Schematics with React 并在 5 分钟内添加 OpenID 连接身份验证

> 原文：<https://dev.to/oktadev/use-schematics-with-react-and-add-openid-connect-authentication-in-5-minutes-416m>

开发人员喜欢自动化。这是我们谋生的主要手段。我们创造程序来消除任务的乏味。我做了很多演示和现场演示。在过去的一年里，我注意到我的一些演示有太多的步骤需要记忆。我已经把它们写在脚本中了，但是我最近发现用 Schematics 驱动的工具来自动化它们更酷！

Schematics 是 Angular CLI 项目中的一个库，允许您使用代码操作项目。您可以创建/更新文件，并向任何具有`package.json`文件的项目添加依赖项。没错，Schematics 不只是针对有角度的项目！

你有时间吗？我很乐意向您展示如何使用我编写的示意图向 React 应用程序添加身份验证。您需要安装 Node.js 10+;然后运行:

```
npx create-react-app rs --typescript 
```

[![Create React app with TypeScript](img/9ec7633918141f189a18cd6ec88fb0d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aTEVWVJC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-schematics/create-react-app-6fdd7b3e270eedbf9e9030e4cb07f189208142122537e8f4769639126421deed.png)

这个过程完成后，在 Okta 上创建一个 OIDC 应用程序。

为什么是 Okta？因为朋友不让朋友写认证！Okta 有认证和用户管理 API，可以大大减少你的开发时间。我们的 API 和 SDK 使开发人员可以轻松地在任何应用中验证、管理和保护用户。

### 在 Okta 上创建 OIDC App

登录您的 Okta 开发者账户(如果您没有账户，请注册，然后导航至**应用** > **添加应用**。点击**单页 App** ，点击**下一步**。给应用取一个你会记住的名字，把端口从`8080`改成`3000`，点击**完成**。

下一个屏幕应该类似于下图:

[![Okta OIDC App Settings](img/4ebaa7b52997d62c505607b921873d5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qrMNVV9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-schematics/app-settings-e843a338e20305c1db05e38f09ae4ab019fe5d8204ca70e556da12cda158a972.png)

返回到您创建`rs`应用程序的终端窗口。导航到目录并运行应用程序，以确保它在端口 3000 上启动。

```
cd rs
npm start 
```

停止该过程(Ctrl+C ),并使用以下命令将 OIDC 认证添加到您的应用程序中:

```
npm i @oktadev/schematics
schematics @oktadev/schematics:add-auth 
```

出现提示时，输入您的发行商(可以在 Okta 的仪表板中的 **API** > **授权服务器**下找到)和客户 ID。当安装完成后，运行`npm start`，你会惊奇地发现你的 React 应用拥有 OIDC 认证！

[![React + Okta = 💙](img/19379175ee6143d3cc1264b4d1564842.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FPG3316j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-schematics/react-okta-45f521d892f2b75216eed5e791c29d52ce893331eb3463d1df61cc4213ede17d.png)

点击**登录**，输入你用来注册 Okta 的凭证，你将被重定向回你的应用。这一次，将显示一个**注销**按钮。

## 创建 React 示意图

看到运行中的原理图很整洁，写出来也很有趣！现在，我将向您展示如何使用 Schematics 来修改使用 Create React App 创建的项目。

为什么要反应？因为用它写应用程序很受欢迎，也很有趣(以我的经验来看)。此外，Eric Elliot 在他的[顶级 JavaScript 框架和 2019 年要学习的主题](https://medium.com/javascript-scene/top-javascript-frameworks-and-topics-to-learn-in-2019-b4142f38df20)中预测“React 将在 2019 年继续占据主导地位”。

Bootstrap 是一个流行的 CSS 框架，React 通过 [reactstrap](https://reactstrap.github.io/) 支持它。在本教程中，您将学习如何创建集成 reactstrap 的原理图。这是一个简单的例子，我将包括单元测试和集成测试技巧。

## Schematics:用代码操纵文件和依赖关系

Angular DevKit 是 GitHub 上的 [Angular CLI 项目的一部分。DevKit 提供了可用于管理、开发、部署和分析代码的库。DevKit 有一个`schematics-cli`命令行工具，可以用来创建自己的原理图。](https://github.com/angular/angular-cli)

要创建 Schematics 项目，首先安装 Schematics CLI:

```
npm i -g @angular-devkit/schematics-cli@0.13.4 
```

然后运行`schematics`创建一个新的空项目。将其命名为`rsi`，作为 ReactStrap Installer 的缩写。

```
schematics blank --name=rsi 
```

这将创建一个`rsi`目录并安装项目的依赖项。有一个`rsi/package.json`处理你的项目的依赖关系。还有一个`src/collection.json`，它定义了原理图的元数据。

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "rsi":  {  "description":  "A blank schematic.",  "factory":  "./rsi/index#rsi"  }  }  } 
```

你可以看到`rsi`原理图指向了`src/rsi/index.ts`中的一个函数。打开这个文件，你会看到下面的内容:

```
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';

export function rsi(_options: any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    return tree;
  };
} 
```

由`src/rsi/index_spec.ts`测试。

```
import { Tree } from '@angular-devkit/schematics';
import { SchematicTestRunner } from '@angular-devkit/schematics/testing';
import * as path from 'path';

const collectionPath = path.join(__dirname, '../collection.json');

describe('rsi', () => {
  it('works', () => {
    const runner = new SchematicTestRunner('schematics', collectionPath);
    const tree = runner.runSchematic('rsi', {}, Tree.empty());

    expect(tree.files).toEqual([]);
  });
}); 
```

Schematics 的一个优点是它们不会对文件系统执行任何直接操作。相反，您指定针对`Tree`的动作。`Tree`是一个数据结构，包含一组已经存在的文件和一个暂存区(包含新的/更新的代码的文件)。

## 原理图与 React

如果你熟悉原理图，你可能见过它们被用来操纵角度项目。Schematics 对 Angular 有很好的支持，但是如果你编码正确，它们可以在任何项目上运行！你可以只寻找`package.json`和一个通用的文件结构，而不是寻找角度细节。生成项目的 CLI 工具，如 Create React App，使这一点变得容易得多，因为您知道文件将在哪里创建。

## 使用 Schematics 向 React 应用程序添加依赖项

[反应堆文件](https://reactstrap.github.io/)提供安装说明。这些是您将使用`rsi`原理图自动完成的步骤。

1.  `npm i bootstrap reactstrap`
2.  导入引导的 CSS
3.  导入和使用 reactstrap 组件

您可以使用 [Schematics 实用程序](https://nitayneeman.github.io/schematics-utilities/)来自动添加依赖关系，等等。首先打开一个终端窗口，在您创建的`rsi`项目中安装`schematic-utilities`。

```
npm i schematics-utilities@1.1.1 
```

更改`src/rsi/index.ts`以添加`bootstrap`和`reactstrap`作为`addDependencies()`函数的依赖项。从主`rsi()`函数中调用这个方法。

```
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
import { addPackageJsonDependency, NodeDependency, NodeDependencyType } from 'schematics-utilities';

function addDependencies(host: Tree): Tree {
  const dependencies: NodeDependency[] = [
    { type: NodeDependencyType.Default, version: '4.3.1', name: 'bootstrap' },
    { type: NodeDependencyType.Default, version: '7.1.0', name: 'reactstrap' }
  ];
  dependencies.forEach(dependency => addPackageJsonDependency(host, dependency));
  return host;
}

export function rsi(_options: any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    addDependencies(tree);
    return tree;
  };
} 
```

## 创建、复制和更新 React 文件

创建一个`src/rsi/templates/src`目录。您将在这个目录中创建模板，这些模板已经有了必需的 reactstrap 导入和用法。

添加一个`App.js`模板，并将下面的引导代码放入其中。

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import { Alert } from 'reactstrap';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <Alert color="success">reactstrap installed successfully! 
            <span role="img" aria-label="hooray">🎉</span>
            </Alert>
        </header>
      </div>
    );
  }
}

export default App; 
```

在同一个目录中创建一个`index.js`文件来添加引导 CSS 导入。

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import 'bootstrap/dist/css/bootstrap.min.css';

ReactDOM.render(<App />, document.getElementById('root'));

serviceWorker.unregister(); 
```

修改`src/rsi/index.ts`中的`rsi()`函数，复制这些模板并覆盖现有文件。

```
import { Rule, SchematicContext, Tree, apply, url, template, move, forEach, FileEntry, mergeWith, MergeStrategy } from '@angular-devkit/schematics';
import { addPackageJsonDependency, NodeDependency, NodeDependencyType } from 'schematics-utilities';
import { normalize } from 'path';

function addDependencies(host: Tree): Tree {
  const dependencies: NodeDependency[] = [
    { type: NodeDependencyType.Default, version: '4.3.1', name: 'bootstrap' },
    { type: NodeDependencyType.Default, version: '7.1.0', name: 'reactstrap' }
  ];
  dependencies.forEach(dependency => addPackageJsonDependency(host, dependency));
  return host;
}

export function rsi(_options: any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    addDependencies(tree);

    const movePath = normalize('./src');
    const templateSource = apply(url('./templates/src'), [
      template({..._options}),
      move(movePath),
      // fix for https://github.com/angular/angular-cli/issues/11337
      forEach((fileEntry: FileEntry) => {
        if (tree.exists(fileEntry.path)) {
          tree.overwrite(fileEntry.path, fileEntry.content);
        }
        return fileEntry;
      }),
    ]);
    const rule = mergeWith(templateSource, MergeStrategy.Overwrite);
    return rule(tree, _context);
  };
} 
```

## 测试您的 reactstrap 安装程序示意图

为了向`package.json`添加依赖项，您必须在您的测试中提供一个。幸运的是，TypeScript 2.9 添加了 JSON 导入，因此您可以创建一个可测试版本的`package.json`(由 Create React App 生成)并在运行测试之前将其添加到`Tree`。

在`rsi/tsconfig.json`文件中，在编译器选项下，添加这两行:

```
{  "compilerOptions":  {  ...  "resolveJsonModule":  true,  "esModuleInterop":  true  }  } 
```

在与`index_spec.ts`相同的目录下创建`react-pkg.json`。

```
{  "name":  "rsi-test",  "version":  "0.1.0",  "private":  true,  "dependencies":  {  "react":  "^16.8.3",  "react-dom":  "^16.8.3",  "react-scripts":  "2.1.5"  }  } 
```

现在您可以在您的测试中导入这个文件，并将其添加到一个可测试的树中。这允许您验证文件及其内容是否已创建。修改`src/rsi/index_spec.ts`以匹配下面的代码。

```
import { HostTree } from '@angular-devkit/schematics';
import { SchematicTestRunner, UnitTestTree } from '@angular-devkit/schematics/testing';
import * as path from 'path';
import packageJson from './react-pkg.json';

const collectionPath = path.join(__dirname, '../collection.json');

describe('rsi', () => {
  it('works', () => {
    const tree = new UnitTestTree(new HostTree);
    tree.create('/package.json', JSON.stringify(packageJson));

    const runner = new SchematicTestRunner('schematics', collectionPath);
    runner.runSchematic('rsi', {}, tree);

    expect(tree.files.length).toEqual(3);
    expect(tree.files.sort()).toEqual(['/package.json', '/src/App.js', '/src/index.js']);

    const mainContent = tree.readContent('/src/index.js');
    expect(mainContent).toContain(`import 'bootstrap/dist/css/bootstrap.min.css'`);
  });
}); 
```

奔跑吧，当一切都过去的时候，尽情欢乐吧！

## 验证您的 React 原理图作品

您可以通过使用 Create React 应用程序的默认设置创建一个新的 React 项目、安装您的原理图并运行它来验证您的原理图是否有效。

```
npx create-react-app test 
```

运行`npm link /path/to/rsi`来安装您的 reactstrap 安装程序。您可能需要调整`rsi`项目的路径以适应您的系统。

```
cd test npm link ../rsi 
```

运行`schematics rsi:rsi`，你应该会看到文件正在更新。

```
UPDATE /package.json (530 bytes)
UPDATE /src/App.js (620 bytes)
UPDATE /src/index.js (294 bytes) 
```

运行`npm install`，然后运行`npm start`，享受安装了引导程序的 React 应用程序的荣耀！

[![reactstrap installed!](img/34e32ce2a6dd4eff2f642f7164f13f17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iriGN8iK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/react-schematics/reactstrap-installed-a47a04039c653437bff5c7a3a423a4914a38d1d3fb93c2b3fb011fae5fd7a245.png)

## 带棱角的示意图

Angular CLI 基于原理图，其 PWA 和 Angular Material 模块也是如此。我不会在这里深入讨论特定角度的原理图，你可以阅读[使用角度原理图来简化你的生活](https://dev.to/oktadev/use-angular-schematics-to-simplify-your-life-3pel-temp-slug-6091747)来了解。

本教程包括如何添加提示，如何发布您的原理图，它引用了我帮助开发的 [OktaDev Schematics](https://github.com/oktadeveloper/schematics) 项目。这个项目的持续集成使用一个 [`test-app.sh`](https://github.com/oktadeveloper/schematics/blob/master/test-app.sh) 脚本，该脚本使用每个框架各自的 CLI 创建项目。例如，下面的脚本测试创建一个新的 Create React 应用程序的项目，并安装示意图。

```
elif ["$1" == "react"] || ["$1" == "r"]
then npx create-react-app react-app
  cd react-app
  npm install ../../oktadev*.tgz
  schematics @oktadev/schematics:add-auth --issuer=$issuer --clientId=$clientId
  CI=true npm test 
```

这个项目也支持支持类型脚本的 React 项目。

## 了解 React、Schematics 和安全认证的更多信息

我希望你喜欢学习如何为 React 创建原理图。我发现这个 API 很容易使用，并且对它的测试支持也很满意。如果你想了解更多关于 Okta 的 React SDK，请看[它的文档](https://github.com/okta/okta-oidc-js/tree/master/packages/okta-react)。

你可以在 GitHub 上找到本教程[的示例原理图。](https://github.com/oktadeveloper/react-schematics-example)

我们已经写了一些关于 Schematics 的博客文章，并在 Okta 开发者博客上作出了回应。你可能也会喜欢它们。

*   [使用角度示意图简化你的生活](https://dev.to/oktadev/use-angular-schematics-to-simplify-your-life-3pel-temp-slug-6091747)
*   [用 Node 和 React 构建一个基本的 CRUD 应用](https://dev.to/oktadev/build-a-basic-crud-app-with-node-and-react-554c)
*   [使用 React 和 Spring Boot 构建一个简单的 CRUD 应用](https://dev.to/oktadev/use-react-and-spring-boot-to-build-a-simple-crud-app-2pdn)
*   [与 Spring Boot 和 React 的良好发展](https://developer.okta.com/blog/2017/12/06/bootiful-development-with-spring-boot-and-react)
*   [如果不是打字稿，就不性感](https://dev.to/oktadev/if-it-aint-typescript-it-aint-sexy-5dnl-temp-slug-3998661)

在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，了解更多关于 Java 的前沿思想。NET、Angular、React 和 JavaScript。