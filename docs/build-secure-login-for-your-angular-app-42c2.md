# 为您的 Angular 应用程序构建安全登录

> 原文：<https://dev.to/oktadev/build-secure-login-for-your-angular-app-42c2>

单页应用程序变得越来越流行。他们的吸引力显而易见。即使在慢速网络上，快速的加载时间也能给用户带来快速响应的感觉。在某些时候，SPA 的开发人员必须考虑身份验证和授权。但是这两个术语实际上是什么意思呢？认证处理确保用户确实是他们所声称的那个人。这通常涉及一个登录页面，用户在其中提供他们的凭证。登录后，授权处理对特定资源的限制和授权访问。在最简单的情况下，对页面的访问仅限于已经验证了自己身份的用户。

在本教程中，我将向您展示如何使用 Angular 在客户端应用程序中实现安全登录。Okta 提供了[特定角度库])([https://developer.okta.com/code/angular/](https://developer.okta.com/code/angular/))，使得包含认证和访问控制变得非常容易。

## 用登录建立一个有角度的 SPA

在本教程中，我将只关注客户端安全性。我不会深入探讨服务器端认证或授权的话题。您将实现的应用程序是一个简单的无服务器在线计算器。只有已经登录的用户才能访问计算器。自然，现实生活中的应用程序将与服务器通信，并向服务器验证自己，以获得对受限资源的访问权。

我将假设您已经在您的系统上安装了 Node，并且您对 node packet manager `npm`有些熟悉。本教程将使用角度 7。要安装 Angular 命令行工具，请打开终端并输入命令:

```
npm install -g @angular/cli@7.1.4 
```

这将安装全局`ng`命令。在某些系统上，Node 将全局命令安装在普通用户不可写的目录中。在这种情况下，您必须使用`sudo`运行上面的命令。接下来，创建一个新的角度应用程序。导航到您选择的目录，并发出以下命令。

```
ng new AngularCalculator 
```

这将启动一个向导，引导您创建新的应用程序。向导将提示您两个问题。当被问及是否在您的应用中添加角度路由时，回答*是*。接下来，您可以选择 CSS 技术。在这里选择 plain *CSS* ，因为你将要开发的应用程序只需要很少的样式。下一步是安装一些库，这将使创建一个令人愉快的响应设计变得更加容易。进入刚刚创建的`AngularCalculator`目录，运行命令:

```
ng add @angular/material 
```

这将提示您几个选项。在第一个问题中，你可以选择颜色主题。我选择了*深紫色/琥珀色*。对于接下来的两个问题，使用手势识别和浏览器动画回答*是*。在材料设计之上，我将使用 Flex 布局组件。运行命令:

```
npm install @angular/flex-layout@7.0.0-beta.22 
```

接下来，向应用程序添加一些常规样式。打开`src/style.css`并用以下内容替换内容。

```
body {
  margin: 0;
  font-family: sans-serif;
}

h1, h2 {
  text-align: center;
} 
```

下一步是使材料设计组件在 Angular 应用程序中可用。用下面的代码替换文件`src/app/app.module.ts`的内容。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';

import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { FlexLayoutModule } from "@angular/flex-layout";

import { MatToolbarModule,
         MatMenuModule,
         MatIconModule,
         MatCardModule,
         MatButtonModule,
         MatTableModule,
         MatDividerModule } from '@angular/material';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    BrowserAnimationsModule,
    FlexLayoutModule,
    MatToolbarModule,
    MatMenuModule,
    MatIconModule,
    MatCardModule,
    MatButtonModule,
    MatTableModule,
    MatDividerModule,
    AppRoutingModule,
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

文件`src/app/app.component.html`包含主应用程序组件的模板。该组件充当完整应用程序及其所有组件的容器。我喜欢在这个组件中创建一个基本的工具栏布局。打开文件，用以下内容替换内容。

```
<mat-toolbar color="primary" class="expanded-toolbar">
  <span>
    <button mat-button routerLink="/">{{title}}</button>
    <button mat-button routerLink="/"><mat-icon>home</mat-icon></button>
  </span>
  <button mat-button routerLink="/calculator"><mat-icon>dialpad</mat-icon></button>
</mat-toolbar>
<router-outlet></router-outlet> 
```

要添加一些样式，打开`src/app/app.component.css`并添加以下几行。

```
.expanded-toolbar {
  justify-content: space-between;
  align-items: center;
} 
```

HTML 模板中的`<router-outlet>`标签充当路由器管理的组件的占位符。接下来创建这些组件。该应用程序将由两个视图组成。主视图显示了一个简单的闪屏，包含关于应用程序的信息。计算器组件包含实际的计算器。要为这些视图创建组件，请在应用程序的主目录中再次打开终端，并为每个视图运行“ng generate”命令。

```
ng generate component home
ng generate component calculator 
```

这将在`src/app`下创建两个目录，每个组件一个。您将只向闪屏添加两个简单的标题。打开`src/app/home/home.component.html`并用以下内容替换内容。

```
<h1>Angular Calculator</h1>
<h2>A simple calculator with login</h2> 
```

计算器组件包含了应用程序的主要部分。首先创建计算器按钮的布局模板，并显示在`src/app/calculator/calculator.component.html`中。

```
<h1 class="h1">Calculator</h1>
<div fxLayout="row" fxLayout.xs="column" fxLayoutAlign="center" class="products">
  <mat-card class="mat-elevation-z1 calculator">
    <p class="display">{{display}}</p>
    <div>
      <button mat-raised-button color="warn" (click)="acPressed()">AC</button>
      <button mat-raised-button color="warn" (click)="cePressed()">CE</button>
      <button mat-raised-button color="primary" (click)="percentPressed()">%</button>
      <button mat-raised-button color="primary" (click)="operatorPressed('/')">÷</button>
    </div>
    <div>
      <button mat-raised-button (click)="numberPressed('7')">7</button>
      <button mat-raised-button (click)="numberPressed('8')">8</button>
      <button mat-raised-button (click)="numberPressed('9')">9</button>
      <button mat-raised-button color="primary" (click)="operatorPressed('*')">x</button>
    </div>
    <div>
      <button mat-raised-button (click)="numberPressed('4')">4</button>
      <button mat-raised-button (click)="numberPressed('5')">5</button>
      <button mat-raised-button (click)="numberPressed('6')">6</button>
      <button mat-raised-button color="primary" (click)="operatorPressed('-')">-</button>
    </div>
    <div>
      <button mat-raised-button (click)="numberPressed('1')">1</button>
      <button mat-raised-button (click)="numberPressed('2')">2</button>
      <button mat-raised-button (click)="numberPressed('3')">3</button>
      <button mat-raised-button color="primary" class="tall" (click)="operatorPressed('+')">+</button>
    </div>
    <div>
      <button mat-raised-button (click)="numberPressed('0')">0</button>
      <button mat-raised-button (click)="numberPressed('.')">.</button>
      <button mat-raised-button color="primary" (click)="equalPressed()">=</button>
    </div>
  </mat-card>
</div> 
```

您会注意到按钮上的`(click)`属性。此属性允许您指定单击按钮时将调用的组件类的成员函数。在开始实现这个类之前，在`src/app/calculator/calculator.component.css`中添加一点样式。

```
.display {
  background-color: #f8f8f8;
  line-height: 24px;
  padding: 5px 8px;
}

.calculator button {
  margin: 5px;
  width: 64px;
}

.calculator button.tall {
  float: right;
  height: 82px;
} 
```

组件的类存在于文件`src/app/calculator/calculator.component.ts`中。打开该文件，并用以下代码替换其内容。

```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-calculator',
  templateUrl: './calculator.component.html',
  styleUrls: ['./calculator.component.css']
})
export class CalculatorComponent implements OnInit {
  private stack: (number|string)[];
  display: string;

  constructor() { }

  ngOnInit() {
    this.display = '0';
    this.stack = ['='];
  }

  numberPressed(val: string) {
    if (typeof this.stack[this.stack.length - 1] !== 'number') {
      this.display = val;
      this.stack.push(parseInt(this.display, 10));
    } else {
      this.display += val;
      this.stack[this.stack.length - 1] = parseInt(this.display, 10);
    }
  }

  operatorPressed(val: string) {
    const precedenceMap = {'+': 0, '-': 0, '*': 1, '/': 1};
    this.ensureNumber();
    const precedence = precedenceMap[val];
    let reduce = true;
    while (reduce) {
      let i = this.stack.length - 1;
      let lastPrecedence = 100;

      while (i >= 0) {
        if (typeof this.stack[i] === 'string') {
          lastPrecedence = precedenceMap[this.stack[i]];
          break;
        }
        i--;
      }
      if (precedence <= lastPrecedence) {
        reduce = this.reduceLast();
      } else {
        reduce = false;
      }
    }

    this.stack.push(val);
  }

  equalPressed() {
    this.ensureNumber();
    while (this.reduceLast()) {}
    this.stack.pop();
  }

  percentPressed() {
    this.ensureNumber();
    while (this.reduceLast()) {}
    const result = this.stack.pop() as number / 100;
    this.display = result.toString(10);
  }

  acPressed() {
    this.stack = ['='];
    this.display = '0';
  }

  cePressed() {
    if (typeof this.stack[this.stack.length - 1] === 'number') { this.stack.pop(); }
    this.display = '0';
  }

  private ensureNumber() {
    if (typeof this.stack[this.stack.length - 1] === 'string') {
      this.stack.push(parseInt(this.display, 10));
    }
  }

  private reduceLast() {
    if (this.stack.length < 4) { return false; }
    const num2 = this.stack.pop() as number;
    const op = this.stack.pop() as string;
    const num1 = this.stack.pop() as number;
    let result = num1;
    switch (op) {
      case '+': result = num1 + num2;
        break;
      case '-': result = num1 - num2;
        break;
      case '*': result = num1 * num2;
        break;
      case '/': result = num1 / num2;
        break;
    }
    this.stack.push(result);
    this.display = result.toString(10);
    return true;
  }
} 
```

这个代码包含一个完整的计算器。看看 HTML 模板中按钮的回调函数是如何作为成员函数实现的。计算器知道基本运算`+`、`-`、`*`和`/`，并且知道运算符优先级。我不会详细说明这是如何实现的。我把这个留给你作为练习来解决。

在开始测试应用程序之前，您需要向路由器注册新组件。打开`src/app/app-routing.module.ts`并编辑其内容以匹配以下内容。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { HomeComponent } from './home/home.component';
import { CalculatorComponent } from './calculator/calculator.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'calculator', component: CalculatorComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

演示应用程序现在已经完成，您可以启动服务器了。Angular 命令行工具附带了一个开发服务器，非常适合测试应用程序。每当您对代码进行任何更改时，它都会自动导致浏览器重新加载应用程序。要启动开发服务器，只需运行以下命令。

```
ng serve 
```

打开浏览器，导航至`http://localhost:4200`，点击右上角的计算器图标。您应该会看到类似下图的内容。

[![Calculator screenshot](img/653b44ff39cc879a1116698d3e5ae773.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yM7YDAj3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-login/angular-calculator-9dd1a0e257c04f21367d71e173f8b6e44aa03455b5b29fe6fbe5d708939403b8.png)

## 为您的 Angular 应用程序添加认证

在这一节中，我将向您展示如何向您的应用程序添加身份验证。 [Okta](https://developer.okta.com/) 提供简单的安全认证解决方案，可轻松集成到 Angular 应用中。现成的 route guard 让您只需将它放入路由规范中，就可以限制对所选路由的访问。申请流程如下。每当用户请求受保护的资源时，路由守卫将检查用户是否登录。如果用户没有登录，guard 会将用户重定向到 Okta 服务器上的托管登录页面。或者，用户可以选择直接点击登录链接。在这种情况下，身份验证服务会将用户重定向到登录页面。用户登录后，登录页面会将用户重定向回一个特殊的路径，在应用程序中通常称为`implicit/callback`。该路由由 Okta 回调组件管理。回调组件将根据原始请求和用户的身份验证状态决定将用户重定向到哪里。

在你开始之前，你需要在 Okta 注册一个免费的开发者账户。打开浏览器，导航到[developer.okta.com](https://developer.okta.com)，点击**创建免费账户**按钮。在接下来出现的表单中，输入您的详细信息。点击**开始**按钮完成注册。

[![Create Okta Developer Account](img/3bd04c686df9cec4e3bfa27f7ba4a393.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q89_gsvA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-login/create-okta-account-6f4d161cb1219552c1c41257326f8fbeb89fbcf6ae116e9b29801ea6a20ae762.png)

一旦您完成注册过程，您将被带到您的 Okta 仪表板。选择顶部菜单中的**应用**链接。在这里，您可以看到您链接到 Okta 帐户的所有应用程序的概述。如果您是新成员，此列表将为空。要创建你的第一个应用程序，点击绿色按钮**添加应用程序**。在接下来出现的屏幕上，选择**单页应用**，点击**下一步**。在下一个屏幕上，您可以编辑设置。基本 URI 应该指向应用程序的位置。由于您使用的是 Angular development 服务器的标准设置，因此应将其设置为`http://localhost:4200/`。登录重定向 URI 是用户成功登录后将被重定向回的位置。这应该与您的应用程序中的回调路由相匹配，因此您需要将其设置为`http://localhost:4200/implicit/callback`。完成后，点击**完成**按钮。出现的屏幕将为您提供一个客户端 ID，您需要将它复制并粘贴到您的应用程序中。

[![Angular app on Okta](img/182ef6435f799c180e02684b01baffdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aRyTbeGX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-login/okta-settings-f33e237b12c43549f51b8c2cfa841fd48180d5c02647fcc6625597b56fe8f61f.png)

要开始在应用程序中实现身份验证，您需要安装 Okta Angular 库。在应用目录下打开终端，运行命令:

```
npm install @okta/okta-angular@1.0.7 
```

打开`src/app/app.module.ts`并将以下导入添加到文件的顶部。

```
import { OktaAuthModule } from '@okta/okta-angular'; 
```

在同一个文件中，在`@NgModule`注释的`imports`部分，添加以下代码。

```
OktaAuthModule.initAuth({
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  redirectUri: 'http://localhost:4200/implicit/callback',
  clientId: '{yourClientId}'
}) 
```

占位符`{yourClientId}`应该替换为您在 Okta 仪表板中获得的客户 ID。打开`src/app/app.component.ts`并用以下内容替换文件内容。

```
import { Component, OnInit } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  title = 'AngularCalculator';
  isAuthenticated: boolean;

  constructor(public oktaAuth: OktaAuthService) {
    this.oktaAuth.$authenticationState.subscribe(
      (isAuthenticated: boolean) => this.isAuthenticated = isAuthenticated
    );
  }

  ngOnInit() {
    this.oktaAuth.isAuthenticated().then((auth) => {
      this.isAuthenticated = auth;
    });
  }

  login() {
    this.oktaAuth.loginRedirect();
  }

  logout() {
    this.oktaAuth.logout('/');
  }
} 
```

`OktaAuthService`被注入到主应用程序组件中。主要组件包含一个标记`isAuthenticated`,用于跟踪用户的身份验证状态。通过订阅`$authenticationState`可观察值，无论状态何时改变，该标志都保持最新。该标志在`ngOnInit`功能中初始化。`login`成员函数简单地调用`OktaAuthService.loginRedirect`，它将用户重定向到托管登录页面。类似地,`logout`成员函数调用`OktaAuthService.logout`,它删除所有用户令牌并将用户重定向到主路由。

接下来，打开`src/app/app.component.html`并将下面的代码添加到第一个`<span>`元素的结束标签之后的`<mat-toolbar>`中。

```
<span>
  <button mat-button *ngIf="!isAuthenticated" (click)="login()"> Login </button>
  <button mat-button *ngIf="isAuthenticated" (click)="logout()"> Logout </button>
</span> 
```

通过使用`isAuthenticated`标志，可以显示`Login`或`Logout`按钮。每个按钮都调用应用程序组件的相应方法来让用户登录或注销。在最后一步，您需要修改路由器设置。打开`src/app/app-routing.module.ts`并将以下导入添加到文件的顶部。

```
import { OktaCallbackComponent, OktaAuthGuard } from '@okta/okta-angular'; 
```

在上面的代码和 Okta 仪表板设置中，您已经指定了`implicit/callback` route 应该处理登录回调。要将`OktaCallbackComponent`注册到该路线，将以下条目添加到`routes`设置中。

```
{ path: 'implicit/callback', component: OktaCallbackComponent } 
```

`OktaAuthGuard`可用于限制对任何受保护路线的访问。为了保护`calculator`路由，通过添加一个`canActivate`属性来修改它的条目，如下所示。

```
{ path: 'calculator', component: CalculatorComponent, canActivate: [OktaAuthGuard] } 
```

现在，如果您尝试访问应用程序中的计算器，您将被重定向到 Okta 登录页面。只有成功登录后，您才会被重定向回计算器。另一方面，闪屏无需任何认证即可访问。

## 用登录小工具进行角度认证

对于某些用例，将用户重定向到外部登录页面是可以的。在其他情况下，你不希望用户离开你的网站。这是登录小部件的一个用例。它允许您将登录表单直接嵌入到应用程序中。要使用这个小工具，你首先要安装它。在应用程序目录中打开终端并安装以下软件包。

```
npm install @okta/okta-signin-widget@2.14.0 rxjs-compat@6.3.3 
```

接下来，生成一个承载登录表单的组件。这个组件不需要任何额外的样式，HTML 模板只包含一个可以在组件定义中内联的标签。

```
ng generate component login --inline-style=true --inline-template=true 
```

打开新创建的`src/app/login/login.component.ts`，粘贴以下内容。

```
import { Component, OnInit } from '@angular/core';
import { Router, NavigationStart} from '@angular/router';

import { OktaAuthService } from '@okta/okta-angular';
import * as OktaSignIn from '@okta/okta-signin-widget';

@Component({
  selector: 'app-login',
  template: `<div id="okta-signin-container"></div>`,
  styles: []
})
export class LoginComponent implements OnInit {
  widget = new OktaSignIn({
    baseUrl: 'https://{yourOktaDomain}'
  });

  constructor(private oktaAuth: OktaAuthService, router: Router) {
    // Show the widget when prompted, otherwise remove it from the DOM.
    router.events.forEach(event => {
      if (event instanceof NavigationStart) {
        switch(event.url) {
          case '/login':
          case '/calculator':
            break;
          default:
            this.widget.remove();
            break;
        }
      }
    });
  }

  ngOnInit() {
    this.widget.renderEl({
      el: '#okta-signin-container'},
      (res) => {
        if (res.status === 'SUCCESS') {
          this.oktaAuth.loginRedirect('/', { sessionToken: res.session.token });
          // Hide the widget
          this.widget.hide();
        }
      },
      (err) => {
        throw err;
      }
    );
  }
} 
```

在`src/index.html`中，在`<head>`标签中添加下面两行，以包含默认的小部件样式。

```
<link href="https://ok1static.oktacdn.com/assets/js/sdk/okta-signin-widget/2.14.0/css/okta-sign-in.min.css" type="text/css" rel="stylesheet"/>
<link href="https://ok1static.oktacdn.com/assets/js/sdk/okta-signin-widget/2.14.0/css/okta-theme.css" type="text/css" rel="stylesheet"/> 
```

现在将登录路由添加到路由配置中。打开`src/app/app-routing.module.ts`。在文件顶部为`LoginComponent`添加一个导入。

```
import { LoginComponent } from './login/login.component'; 
```

接下来，添加一个功能，告诉路由器在要求用户登录时应该做什么。

```
export function onAuthRequired({ oktaAuth, router }) {
  router.navigate(['/login']);
} 
```

确保该函数已导出。在`routes`规范中，为登录组件添加路由。

```
{ path: 'login', component: LoginComponent } 
```

最后，修改`calculator`路由的规范，以包含对`onAuthRequired`函数的引用。

```
{
  path: 'calculator',
  component: CalculatorComponent,
  canActivate: [OktaAuthGuard],
  data: { onAuthRequired }
} 
```

下一步是确保当顶栏中的登录按钮被按下时，用户被引导到`login`。打开`src/app/app.component.html`,将包含登录按钮的行更改为以下内容。

```
<button mat-button *ngIf="!isAuthenticated" routerLink="/login"> Login </button> 
```

您也可以删除`src/app/app.component.ts`中的`login`功能，因为不再需要它。

就是这样！您的应用程序现在拥有自己的由 Okta 支持的登录表单。下面是登录小部件的截图。

[![Angular Calculator with Sign-In Widget](img/0dfebe1a78806b45a4adc23747409c78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UfX9kddz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/angular-login/angular-login-5ef9af576dab6db6d4f4172607d98bdcdc7f1e70bf91a54d580cabc5b4cdfc97.png)

## 了解有关在 Angular 中构建安全登录和注册的更多信息

在本教程中，我向您展示了如何在基于 Angular 的单个页面应用程序中实现身份验证和基本授权。您可以在托管登录页面和应用程序中嵌入的登录小部件之间进行选择。当您知道有多个应用程序链接到一个用户帐户时，托管登录是理想的选择。在这种情况下，托管解决方案传达了用户在一个中心位置登录所有应用程序的想法。当您希望在单一品牌的应用程序中提供无缝体验时，登录小部件是理想的解决方案。

下面是一些链接，您可以在其中找到更多关于单页应用程序、Angular 和认证的信息。

*   [了解如何为任何网页添加认证](https://developer.okta.com/blog/2018/06/08/add-authentication-to-any-web-page-in-10-minutes)
*   [了解 Angular 7 的更多新功能](https://developer.okta.com/blog/2018/12/04/angular-7-oidc-oauth2-pkce)
*   [了解如何将 CRUD 服务器添加到您的 Angular 应用程序中](https://developer.okta.com/blog/2018/10/30/basic-crud-angular-and-node)
*   [了解如何将您的 Angular 应用程序转变为渐进式网络应用程序](https://developer.okta.com/blog/2019/01/30/first-angular-pwa)

本教程的代码可以在 GitHub 的[okta developer/okta-angular-calculator-example](https://github.com/oktadeveloper/okta-angular-calculator-example)找到。

你喜欢这个教程吗？更多精彩内容，请在 Twitter [@oktadev](https://twitter.com/OktaDev) 、[脸书](https://www.facebook.com/oktadevelopers/)和 [LinkedIn](https://www.linkedin.com/company/oktadev/) 上关注我们！