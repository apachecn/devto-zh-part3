# 角度材质入门-创建自定义对话框组件

> 原文：<https://dev.to/pavlosisaris/getting-started-with-angular-material-creating-a-custom-dialog-component-3679>

# 简介

本文最初发表于[paulisaris.com](https://paulisaris.com/getting-started-with-angular-material-creating-a-custom-dialog-component/)

Angular 是构建单页交互式应用程序的流行框架。

随着 Angular 越来越受欢迎，我们有机会使用为我们提供内置 UI 组件的库，这些组件允许我们专注于使我们的应用程序脱颖而出的代码。；)

在本教程中，我们将研究如何使用[有角度的材料](https://material.angular.io/)来构建一个干净的可重用的对话框组件。

我们将关注可重用性，这意味着我们将构建一个单独的自定义对话框组件，我们将在需要时通过服务类调用该组件。这将有助于我们避免 HTML 模板和 CSS 类的重复，也有助于改善整个应用程序中的关注点分离(T2)。

您可以在我的 [Github 资源库](https://github.com/PavlosIsaris/Angular-playground)中找到示例项目

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[角斗场](https://github.com/PavlosIsaris/Angular-playground)

### 一个简单的角度应用程序，演示角度材料的组成部分

<article class="markdown-body entry-content container-lg" itemprop="text">

# 角状操场

此项目是使用版本 7.3.8 的 [Angular CLI](https://github.com/angular/angular-cli) 生成的。

## 开发服务器

为开发服务器运行`ng serve`。导航至`http://localhost:4200/`。如果您更改任何源文件，应用程序将自动重新加载。

## 代码脚手架

运行`ng generate component component-name`生成一个新组件。也可以用`ng generate directive|pipe|service|class|guard|interface|enum|module`。

## 建设

运行`ng build`来构建项目。构建工件将存储在`dist/`目录中。使用`--prod`标志进行生产构建。

## 运行单元测试

运行`ng test`通过 [Karma](https://karma-runner.github.io) 执行单元测试。

## 运行端到端测试

运行`ng e2e`通过[量角器](http://www.protractortest.org/)执行端到端测试。

## 进一步帮助

要获得更多关于 Angular CLI 的帮助，请使用`ng help`或查看 [Angular CLI 自述文件](https://github.com/angular/angular-cli/blob/master/README.md)。

</article>

[View on GitHub](https://github.com/PavlosIsaris/Angular-playground)

# 入门

在编写任何代码之前，我们需要设置我们的项目。Angular 使用命令行界面(简称 CLI)来使用它的各种命令。该工具可以作为 npm 依赖项安装。

我们假设您已经在系统上安装了 npm 作为全局依赖项。
您可以通过运行以下命令来验证 npm 安装是否正确:

```
paul@spiderman:~$ npm -v
6.4.1 
```

(是的，我用超级英雄的名字给我所有的电脑命名，这不是很棒吗？):D

很好，因为我们已经安装了 npm，所以我们可以继续安装 Angular CLI 工具:

```
npm -g i @angular/cli 
```

(-g 表示将全局安装依赖项)

如果安装过程成功完成，我们可以使用 npm 打印所有的全局依赖项:

```
paul@spiderman:~$ npm -g ls --depth=0

/home/paul/.nvm/versions/node/v10.15.3/lib
├── @angular/cli@7.3.8
└── npm@6.4.1 
```

太好了！让我们创建一个游乐场项目开始编码！

```
ng new angular-playground 
```

在我们选择了所需的设置配置选项后，我们可以转到项目的位置并启动项目:

```
cd angular-playground

ng serve 
```

如果一切按计划进行，我们应该能够导航到 [http://localhost:4200/](http://localhost:4200/) 并看到默认的 Angular 起始页。

# 给我们的项目添加有角度的素材

现在，是时候安装角状材料并利用它的内置组件了。

如[安装指南](https://material.angular.io/guide/getting-started)所述，我们使用以下 npm 命令安装角形材料:

```
npm install --save @angular/material @angular/cdk @angular/animations 
```

接下来，我们需要启用动画。转到`src/app/app.module.ts`并添加以下内容:

```
import {BrowserAnimationsModule} from '@angular/platform-browser/animations';

@NgModule({
  ...
  imports: [BrowserAnimationsModule],
  ...
}) 
```

此外，我们需要指定一个 CSS 主题，我们的应用程序将在显示有角度的材料组件时使用该主题。

我们将使用默认的 indigo-pink 主题，将下面的 import 语句添加到`src/styles.scss` :

```
@import "~@angular/material/prebuilt-themes/indigo-pink.css"; 
```

太好了！棱角分明的材料已经准备好，随时可以使用。

# 创建自定义对话框组件

接下来，我们需要创建一个新的 Angular 组件，它将作为`MatDialog`组件的包装器，而这个组件又是 Angular Material 中的本地对话框组件。
这里的要点是为我们的应用程序创建一个通用的 Angular 组件，以便按照我们的意愿设计它，并在其中包含任何所需的业务逻辑代码。

我们可以使用 Angular CLI 并执行以下命令:

```
ng g c dialog 
```

在`src/app/dialog/`中创建了一个新组件。
导航到`src/app/dialog/dialog.component.html`并删除样本 HTML。

我们还需要将`DialogComponent`添加到`src/app/app.module.ts`类中的`entryComponents`数组中: