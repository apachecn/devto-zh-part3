# 角形材料路由器出口

> 原文：<https://dev.to/codingcatdev/angular-material-router-outlet-3292>

原帖:[https://ajonp . com/courses/angular material/angular-material-router-outlet/](https://ajonp.com/courses/angularmaterial/angular-material-router-outlet/)

[https://www.youtube.com/embed/niJrSNQ1KwI](https://www.youtube.com/embed/niJrSNQ1KwI)

# 角状材料路由器出口

这节课将从一个新的 Angular 项目开始，通过使用 Angular [路由器](https://angular.io/guide/router)和[命名的 Outlets](https://angular.io/guide/router#displaying-multiple-routes-in-named-outlets) 来学习如何使用 Angular 材料 [Sidenav](https://material.angular.io/components/sidenav/overview) 。这将是建立一个发布书评的应用程序的开始。

🌎演示:[https://ajonp-lesson-9.firebaseapp.com/](https://ajonp-lesson-9.firebaseapp.com/)

## 上课步骤

1.  [角状材料路由器出口](https://ajonp.com/lessons/9-angular-material-router-outlet/#angular-material-router-outlet)
2.  [创建角度项目](https://ajonp.com/lessons/9-angular-material-router-outlet/#create-angular-project)
3.  [发球角度项目](https://ajonp.com/lessons/9-angular-material-router-outlet/#serve-angular-project)
4.  [角度模块](https://ajonp.com/lessons/9-angular-material-router-outlet/#angular-modules)
5.  [角形材料侧导航](https://ajonp.com/lessons/9-angular-material-router-outlet/#angular-material-sidenav)
6.  [懒惰加载书籍功能模块](https://ajonp.com/lessons/9-angular-material-router-outlet/#lazy-loading-books-feature-module)
7.  [延迟加载欢迎功能模块](https://ajonp.com/lessons/9-angular-material-router-outlet/#lazy-loading-welcome-feature-module)
8.  [使用路由器链接导航](https://ajonp.com/lessons/9-angular-material-router-outlet/#using-router-link-for-navigation)
9.  [工具栏更新](https://ajonp.com/lessons/9-angular-material-router-outlet/#toolbar-updates)
10.  [作为指定出口的书抽屉](https://ajonp.com/lessons/9-angular-material-router-outlet/#book-drawer-as-named-outlet)
11.  [最终想法](https://ajonp.com/lessons/9-angular-material-router-outlet/#final-thoughts)

# 创建角度项目

如果您从未使用过 [Angular CLI](https://cli.angular.io/) ,您将需要查看主页以开始使用。

```
ng new angular-material-router-outlet 
```

Enter fullscreen mode Exit fullscreen mode

请为路线和 SCSS 选择是。
[![NG Choices](img/e11caff6d9edd53f422df603c170a3c9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YLbWPUtC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547496414/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/ukc1dpxppxkumbid4aql.jpg)

## 添加材料到角度项目

> 确保您已经切换到正确的目录`cd angular-material-router-outlet`

我们现在将运行一个角度原理图命令，您可以将此视为一个工作流，以帮助您更快地启动和运行项目。有几个可用的原理图，我建议阅读[角度博客](https://blog.angular.io/schematics-an-introduction-dc1dfbc2a2b2)关于原理图和[角度控制台](https://angularconsole.com/)。

```
ng add @angular/material 
```

Enter fullscreen mode Exit fullscreen mode

对于选项，请选择自定义，因为我们将在下一课中添加这些选项。
[![Angular Material Selections](img/806e40ad8d35128aff481132da5b4610.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--X2j6rsyF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547499455/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/rcbcfajzbwkjlptbhk08.jpg)

## 打开项目

现在你可以打开你的新 Angular 项目，如果使用 VSCode

```
cd angular-material-router-outlet && code . 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到基地棱角分明的结构，包括一个路由模块`app-routing.module.ts`
[![Structure for Angular Project](img/59d0ca378d7debf0e0a17a7ba298863a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U70V3HKo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547499630/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/wds1pzbwihb4p6efpnks.jpg)

package.json

```
{  "name":  "angular-material-router-outlet",  "version":  "0.0.0",  "scripts":  {  "ng":  "ng",  "start":  "ng serve",  "build":  "ng build",  "test":  "ng test",  "lint":  "ng lint",  "e2e":  "ng e2e"  },  "private":  true,  "dependencies":  {  "@angular/animations":  "~7.1.0",  "@angular/cdk":  "~7.2.1",  "@angular/common":  "~7.1.0",  "@angular/compiler":  "~7.1.0",  "@angular/core":  "~7.1.0",  "@angular/forms":  "~7.1.0",  "@angular/material":  "^7.2.1",  "@angular/platform-browser":  "~7.1.0",  "@angular/platform-browser-dynamic":  "~7.1.0",  "@angular/router":  "~7.1.0",  "core-js":  "^2.5.4",  "hammerjs":  "^2.0.8",  "rxjs":  "~6.3.3",  "tslib":  "^1.9.0",  "zone.js":  "~0.8.26"  },  "devDependencies":  {  "@angular-devkit/build-angular":  "~0.11.0",  "@angular/cli":  "~7.1.3",  "@angular/compiler-cli":  "~7.1.0",  "@angular/language-service":  "~7.1.0",  "@types/node":  "~8.9.4",  "@types/jasmine":  "~2.8.8",  "@types/jasminewd2":  "~2.0.3",  "codelyzer":  "~4.5.0",  "jasmine-core":  "~2.99.1",  "jasmine-spec-reporter":  "~4.2.1",  "karma":  "~3.1.1",  "karma-chrome-launcher":  "~2.2.0",  "karma-coverage-istanbul-reporter":  "~2.0.1",  "karma-jasmine":  "~1.1.2",  "karma-jasmine-html-reporter":  "^0.2.2",  "protractor":  "~5.4.0",  "ts-node":  "~7.0.0",  "tslint":  "~5.11.0",  "typescript":  "~3.1.6"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

index.html

```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  AngularMaterialRouterOutlet
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link href="https://fonts.googleapis.com/css?family=Roboto:300,400,500" rel="stylesheet">
  <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
</head>
<body>
  <app-root></app-root>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

# 奉角项目

为了预览此基础设置，您需要运行角度伺服命令。

```
ng serve 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 [http://localhost:4200](http://localhost:4200) 上，您将看到显示的默认角页。

[![Angular Base page](img/49d818124a8aaae7282d8493657a003d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JONrjRKX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547499873/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/cq7rdkftk6km9kmtism0.jpg)

# 角度模块

一般来说，模块是一种打包逻辑上属于一起的几个基于角度的文件的方式。直接来自 Angular 的[文档](https://angular.io/guide/architecture-modules)，“NgModules 是一个内聚代码块的容器，专用于一个应用程序域、一个工作流或一组密切相关的功能。”

我们将在本课(以及任何角度项目)中广泛使用 [NgModule](https://angular.io/api/core/NgModule) 和[组件](https://angular.io/api/core/Component)。

很多教程会让你开始把所有东西都放进 app.component*里，我喜欢保持主 app 干净，尽可能在懒加载后加载。创建一个模块文件夹使事情更简洁，但是做你最喜欢的。

# 角材侧导航

侧导航由三个主要的 html 元素组成`<mat-sidenav-container>`、`<mat-sidenav>`和`<mat-sidenav-content>`。视觉上，这些可以表示为

[![Material Sidenav Layout](img/13296c4c9f1cdcdbd674430f8585c7e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtVcf4V5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547500528/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/mat-sidenav-content.jpg)

## 创建侧导航模块

要创建一个模块，我们可以利用 Angular CLI 并运行

```
ng g m modules/sidenav 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将需要一个组件来显示角材料侧导航。

```
ng g c modules/sidenav 
```

Enter fullscreen mode Exit fullscreen mode

这些命令的输出应该给出这样的结构。

[![Sidenav Module Structure](img/eae21f81ce2080de0697c3b4756c77e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mVhx3XR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547500863/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/dbap7swjuk06qig0jtg2.jpg)

然后，您可以用
替换`app.component.html`中的任何内容

```
<app-sidenav></app-sidenav> 
```

Enter fullscreen mode Exit fullscreen mode

Sidenav 将是整个应用程序的主要入口点，所以它需要直接驻留在 app.component 中。这是在属性`selector: app-sidenav`中的`@Component`装饰器的`sidenav.component.ts`中定义的。现在在这一点上`app.component.ts`仍然不知道如何找到`sidenav.component.ts`，所以我们必须从`sidenav.module.ts`导出它，并将其导入`app.module.ts`。

sidenav.module.ts

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { SidenavComponent } from './sidenav.component';
import { MatSidenavModule, MatToolbarModule, MatIconModule, MatButtonModule, MatListModule} from '@angular/material';
import { RouterModule } from '@angular/router';

@NgModule({
  declarations: [SidenavComponent],
  imports: [
    CommonModule,
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    RouterModule,
    MatListModule
  ],
  exports: [SidenavComponent]
})
export class SidenavModule { } 
```

Enter fullscreen mode Exit fullscreen mode

app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { SidenavModule } from './modules/sidenav/sidenav.module';
import { OverlayContainer } from '@angular/cdk/overlay';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    SidenavModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {
  constructor(overlayContainer: OverlayContainer){
    overlayContainer.getContainerElement().classList.add('angular-material-router-app-theme');
  }
 } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的应用程序可以找到 Sidenav 模块，并使用它来显示任何导出的组件。
如果你再次打开预览 [http://localhost:4200](http://localhost:4200) ，你现在应该看到“sidenav 工作！”

我建议在这一点上做出承诺。

```
git add . && git commit -m "Initial sidenav" 
```

Enter fullscreen mode Exit fullscreen mode

## 更新 sidenav.component*

现在我们知道我们的组件可以被看作是纯文本，让我们开始使用有角度的材料 Sidenav 组件来设计我们的应用程序。首先，我们需要告诉`sidenav.module.ts`我们需要包含这个新组件，通过将它添加到我们从`@angular/material`的导入中。

```
import { MatSidenavModule} from '@angular/material';
...
  imports: [
    CommonModule,
    MatSidenavModule,
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以将更新 sidenav.component.html 包括侧导航元素。

```
<mat-sidenav-container>
  <mat-sidenav>drawer</mat-sidenav>
  <mat-sidenav-content>content</mat-sidenav-content>
</mat-sidenav-container> 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您现在预览页面，您将只能看到“内容”,因为抽屉是自动隐藏的。

更新`mat-sidenav`元素以打开抽屉并放在内容旁边。

```
<mat-sidenav opened=false mode="over">
... 
```

Enter fullscreen mode Exit fullscreen mode

现在可以再次预览 [http://localhost:4200](http://localhost:4200) 。

## 添加 MatToolbar

我们可以通过在顶部添加一个工具栏来让我们的网站看起来更像

```
<mat-sidenav-container>
  <mat-sidenav opened=false mode="over" #snav>
  drawer
  </mat-sidenav>
  <mat-sidenav-content>
    <mat-toolbar color="primary">
      <button
      type="button"
      aria-label="Toggle sidenav"
      mat-icon-button
      (click)="snavToggle(snav)"
    >
      <mat-icon>menu</mat-icon>
    </button>
   content
  </mat-sidenav-content>
</mat-sidenav-container> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在组件中添加了三个新的角度材料元素`mat-toolbar`、`mat-icon-button`和`mat-icon`，我们需要让`sidenav.component.ts`知道它们是在哪里定义的，所以你需要在`sidenav.module.ts`中导入它们。

```
@NgModule({
  declarations: [SidenavComponent],
  imports: [
    CommonModule,
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    ... 
```

Enter fullscreen mode Exit fullscreen mode

## 添加角度路由器出口

我们的应用程序的主要内容需要一个地方来结束，这就是 Angular 的`router-outlet`所完成的。它是一个占位符，从另一个组件获取标记并将其放在页面上。对于我们的应用程序，这将是其他子出口嵌套下的主出口。

```
...
    <router-outlet></router-outlet>
  </mat-sidenav-content>
</mat-sidenav-container> 
```

Enter fullscreen mode Exit fullscreen mode

还要记住将 RouterModule 添加到`sidenav.module`中，以便 Angular 理解元素`<router-outlet>`。

```
@NgModule({
  declarations: [SidenavComponent],
  imports: [
    CommonModule,
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    RouterModule,
    MatListModule
  ],
  exports: [SidenavComponent]
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是到目前为止我们代码中发生的事情的可视化表示，mat-side nav-content-> router-outlet 是我们应用程序的 reaminder 所在的地方。

[![Sidenav with Toolbar](img/7729361fa4ceb2f9a162a171d1503b80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7t6MQLOS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547502835/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/main-router-outlet.jpg)

# 懒人加载书籍功能模块

我们将设置的第一个子路由是预订路由。这将需要我们创建一个新的模块和组件。这次我们将使用一个可选参数`--routing`，它也将创建一个路由模块。

创建图书模块

```
ng g m modules/books --routing 
```

Enter fullscreen mode Exit fullscreen mode

创建图书组件

```
ng g c modules/books 
```

Enter fullscreen mode Exit fullscreen mode

## 更新 App 路由

我们现在需要配置路由器，以便可以访问图书功能模块。因此，我们将返回到`app-routing.module.ts`并添加一条路径为`books`的新路由。有一种以惰性方式加载模块的特殊方法，这意味着它们不是在第一次访问应用程序时下载，而是在第一次访问路线时下载。你可以在[角度指南](https://angular.io/guide/lazy-loading-ngmodules)中阅读更多关于惰性加载模块的内容。

```
const routes: Routes = [
  {
    path: 'books',
    loadChildren: './modules/books/books.module#BooksModule'
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

## App 路由默认路由

如果有人在没有指定路径的情况下进入应用程序，我们需要将该请求重定向到图书，以便内容能够正确显示。

添加到固定路线。

```
 {
    path: '',
    redirectTo: '/books',
    pathMatch: 'full'
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 更新图书功能模块路线

现在我们已经告诉了 app router 一个特性模块，我们需要确保这个特性模块知道它应该加载哪个组件，所以我们将添加一个空路径。

```
const routes: Routes = [
  {
    path: '',
    component: BooksComponent,
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该在实时预览中看到一条消息“books works！”。

# 懒加载欢迎功能模块

许多网站通常会有一个欢迎或主页模块，如果有通知、登录或基本信息要求，您可以将您的流量路由到该模块。因此，我们将把我们的基本路径切换到这个新的功能模块，并将书籍放在一个单独的路径上。这将是与我们的图书模块相同的设置。

创建欢迎模块

```
ng g m modules/welcome --routing 
```

Enter fullscreen mode Exit fullscreen mode

创建欢迎组件

```
ng g c modules/welcome 
```

Enter fullscreen mode Exit fullscreen mode

## 更新 App 路由

```
const routes: Routes = [
  {
    path: 'welcome',
    loadChildren: './modules/books/books.module#BooksModule'
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

## App 路由默认路由

将此重定向从 books 更改为 Welcome。

app-routing.module.ts

```
const routes: Routes = [
  {
    path: 'welcome',
    loadChildren: './modules/welcome/welcome.module#WelcomeModule'
  },
  {
    path: 'books',
    loadChildren: './modules/books/books.module#BooksModule'
  },
  {
    path: '',
    redirectTo: '/welcome',
    pathMatch: 'full'
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

## 更新欢迎功能模块路线

welcome-routing.module.ts

```
const routes: Routes = [
  {
    path: '',
    component: WelcomeComponent,
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

# 利用路由器链接进行导航

为了让我们浏览网站，我们需要添加一些导航元素。使用带有特定`mat-nav-list`元素类型的[角度材料列表](https://material.angular.io/components/list/overview#navigation-lists)正是我们的 sidenav 抽屉所需要的。

```
...
  <mat-sidenav opened=false mode="over">
    <mat-nav-list>
      <mat-list-item>
        <h4 matLine routerLink="/welcome"
        [routerLinkActiveOptions]="{exact:true}"
        routerLinkActive="active-link">Home</h4>
      </mat-list-item>
      <mat-list-item>
        <h4 matLine routerLink="/books" routerLinkActive="active-link">Books</h4>
      </mat-list-item>
    </mat-nav-list>
  </mat-sidenav>
  ... 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记，您现在需要在您的`sidenav.module.ts`导入中添加`RouterModule`和`MatListModule`。

sidenav.module.ts

```
@NgModule({
  declarations: [SidenavComponent],
  imports: [
    CommonModule,
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    RouterModule,
    MatListModule
  ],
  ... 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在预览 http://localhost:4200 ，你会在侧边导航中看到，你可以点击主页或书籍，内容会变成“欢迎作品！”和书籍作品！“分别了。

## 主动路由器链接

您可以通过添加属性`routerLinkActive`并传递一个类来设计您的链接，以了解您当前正在使用哪个链接。我们已经把我们的分配给了`active-link`。

然后，我们可以将样式添加到`sidenav.component.scss`中，使活动链接变成粗体蓝色。

```
.active-link {
  color: blue;
  font-weight: bold !important;
  border: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们已经将我们的 home (welcome) route 路径分配给了“/”,如果您现在预览，Books 和 home 都会被高亮显示。通过将我们的 routerlink 改为`/welcome`，这个问题将会得到解决。在后面的课程中，我们还将讨论 router link 选项，如`[routerLinkActiveOptions]="{exact:true}"`。

# 工具栏更新

在我们的`mat-toolbar`中，我们放置了一个按钮，该按钮当前调用一个尚未定义的函数。我们需要通过在元素`mat-sidenav`中使用`#snav`来分配一个名为 snav 的变量。

## Sidenav Toggle

sidenav.component.html

```
<mat-sidenav opened=false mode="over" #snav> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用这个新变量，并将其传递给按钮 click output `(click)="snavToggle(snav)"`

sidenav.component.html

```
<button
  type="button"
  aria-label="Toggle sidenav"
  mat-icon-button
  (click)="snavToggle(snav)"
> 
```

Enter fullscreen mode Exit fullscreen mode

## 功能为切换开关

使用我们新的 snav 引用，我们可以调用这个对象上存在的方法，它将打开或关闭 sidenav 抽屉。

sidenav.component.ts

```
snavToggle(snav) {
  snav.toggle();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在预览 http://localhost:4200 ，你会看到工具栏上的汉堡(三条水平线)按钮会打开和关闭侧导航抽屉。

## 工具栏标题

我们还可以指定一个标题来允许我们的 home routerlink 返回 home。

```
<a class="home-link" routerLink=".">{{ title }}</a> 
```

Enter fullscreen mode Exit fullscreen mode

sidenav.component.ts

```
 title = 'Lesson 9 - Angular Material Router Outlet'; 
```

Enter fullscreen mode Exit fullscreen mode

# 图书抽屉作为命名网点

现在，我们已经为图书功能模块设置了工作导航和工具栏，我们将在此页面上为抽屉添加一个命名的插座。

视觉上看起来是这样的
[![Drawer Layout](img/15a2e42ac31fca7da3709c1d3df1ff45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QrtHVAQU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547511788/ajonp-ajonp-com/9-lesson-angular-material-router-outlet/book-drawer.jpg) 
我们将把`books.component.html`从有文本改为包括一个有角度的材料抽屉(mat-drawer)。记住，现在我们在`sidenav.component`有`one`路由器出口，在`books.component`有`two`路由器出口，一个以抽屉命名，一个以内容命名。

## 创建抽屉组件

此模块不需要路由，因为它将只在我们的图书模块内部使用，而不是作为一个功能模块。

模块

```
ng g m modules/books/book-drawer 
```

Enter fullscreen mode Exit fullscreen mode

组件

```
ng g c modules/books/book-drawer 
```

Enter fullscreen mode Exit fullscreen mode

> 不要忘记导出该组件，因为它将在 book-detail 中使用。

book-drawer.module.ts

```
...
@NgModule({
  declarations: [BookDrawerComponent],
  imports: [
    CommonModule
  ],
  exports: [
    BookDrawerComponent
  ]
... 
```

Enter fullscreen mode Exit fullscreen mode

## 给书籍添加垫屉

抽屉有三个部分，就像 sidenav 一样，这是因为它们和 sidenav 一样，有一些额外的结构特征。

打开属性=“真”会在屏幕上显示抽屉，打开模式=“侧”会将内容推到抽屉旁边。

modules/books/books . component . html

```
<mat-drawer-container>
  <mat-drawer  opened="true" mode="side">
    <router-outlet name="book-drawer"></router-outlet>
  </mat-drawer>
  <mat-drawer-content>
    <router-outlet></router-outlet>
  </mat-drawer-content>
</mat-drawer-container> 
```

Enter fullscreen mode Exit fullscreen mode

记得给`books.module.ts`加上 MatSidenavModule，否则`mat-drawer`元素不会被识别。

```
@NgModule({
  declarations: [BooksComponent],
  imports: [
    CommonModule,
    BooksRoutingModule,
    MatSidenavModule
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 创建账簿明细组件

我们将使用它作为书籍的附加子功能路线，因此我们需要路由器模块。

模块

```
ng g m modules/books/book-detail --routing 
```

Enter fullscreen mode Exit fullscreen mode

组件

```
ng g c modules/books/book-detail 
```

Enter fullscreen mode Exit fullscreen mode

## 更新图书路由

我们不再希望在点击`/books`路径时只加载 BookComponent，我们希望它也加载其子组件。我们这样做和我们用`app-routing.module.ts`做的一样，我们将用`loadChildren`来延迟加载它。

```
const routes: Routes = [
  {
    path: '',
    component: BooksComponent,
    children: [
      {
        path: '',
        loadChildren: './book-detail/book-detail.module#BookDetailModule'
      }
    ]
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

既然 books 模块知道如何在其基本路径上延迟加载 book-detail 模块，我们需要更新 book-detail 中的 route 来加载它自己的组件。然而，这将有一个特殊的路由，抽屉也有一个`outlet`，这告诉路由器，它必须只使用这个命名的路由的组成部分。

所以路由器会加载:

【账本-明细- > `<router-outlet>`

【账本-抽屉- > `<router-outlet name="book-drawer">`

```
const routes: Routes = [
  {
    path: '',
    component: BookDetailComponent
  },
  {
    path: '',
    component: BookDrawerComponent,
    outlet: 'book-drawer'
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在预览 http://localhost:4200/books ，你会在一个抽屉里看到“book-drawer works！”而在内容区“书-细节作品！”。

# 最后的想法

角路由器是惊人的强大，你可以创建多个嵌套路由，命名路由，保护路由...

如果你克隆了最终的 GitHub repo，你会看到一些额外的样式更新，我会在下一节 Angular Material 主题化课程中介绍。