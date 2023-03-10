# 有角度的材料主题

> 原文：<https://dev.to/codingcatdev/angular-material-theming-32km>

> 原帖:[https://ajonp . com/courses/angular material/angular-material-theming/](https://ajonp.com/courses/angularmaterial/angular-material-theming/)

[https://www.youtube.com/embed/V3WiBs-igaY](https://www.youtube.com/embed/V3WiBs-igaY)

> 我对这节课/视频有点赶，所以如果你有任何问题，请跳到[松弛频道](https://bit.ly/ajonp-slack-invite)

# 棱角分明的材质主题化

🌎演示:[https://ajonp-lesson-10.firebaseapp.com/kitchensink](https://ajonp-lesson-10.firebaseapp.com/kitchensink)

Angular Material 是官方支持的组件，用于在 Angular 中实现 Google 的材质设计系统。
棱角分明的材料有 4 个预设主题

*   深紫色-琥珀色
*   靛粉色
*   粉红-蓝灰色
*   紫色-绿色我们将看看如何创建我们自己的主题，并将它包含在我们的默认样式中，然后使用它为我们的每个组件样式提供混音。

> 我听到的关于 Angular Material 中的主题的最大抱怨可能是要求使用 sass 函数构建一套全新的 css，这会使 style.css 文件膨胀。请查看 [Ionic CSS 变量](https://beta.ionicframework.com/docs/theming/color-generator)来比较这是如何实现的。截至发稿时，这仍然是 GitHub 上的一个[未决问题](https://github.com/angular/material2/issues/4352)

## 上课步骤

1.  [厨房水槽组件](#kitchen-sink-component)
2.  [角度柔性布局](#angular-flex-layout)
3.  [棱角分明的素材主题创作](#angular-material-theme-creation)
4.  [主题选择角度服务](#theme-selection-angular-service)
5.  [萨斯 Mixins](#sass-mixins)
6.  [工具](#tools)

# 厨房水槽组件

在英语中(可能只有美国)，我们说“除了厨房的水槽以外的任何东西”，在编程中，我们经常使用“厨房水槽”来指代我们在一个网站上可能使用的任何东西。对于我们的例子，我们将从[Angular Material kitchen sink](https://github.com/angular/material2/blob/b21cfb73261e5c334280a185762ffcc475348b76/src/universal-app/kitchen-sink/kitchen-sink.ts)中提取，这样我们就可以突出显示所有可能的组件以及主题对它们的影响。

这些组件可以在[material.angular.io/components](https://material.angular.io/components/categories)上找到，也有一些来自组件开发工具包(CDK)。

## 克隆回购

通常我会提供一个指南来建立整个事情(如果你想跟着做，我在视频中展示了这一点)，但为了简化事情，我建议你只复制第 10 课的回购(或叉它，然后复制你的)，并在这次演练中检查它。

```
git clone https://github.com/AJONPLLC/lesson-10-angular-material-theming 
```

Enter fullscreen mode Exit fullscreen mode

如果你想继续，你可以从第 9 课开始，和我一起更新。

```
git clone https://github.com/AJONPLLC/lesson-9-angular-material-router-outlet 
```

Enter fullscreen mode Exit fullscreen mode

# 角度伸缩布局

angular FLex-Layout(FX Layout)提供了一个替代手工创建 flexlayout 的 css 的好方法。它与 Bootstrap 的 Flex 非常相似，但我觉得它似乎更强大。

这是 tburleson 的一个很好的例子:

<video controls=""><source src="https://res.cloudinary.com/ajonp/video/upload/v1547729145/ajonp-ajonp-com/10-lesson-angular-material-theming/fxlayoutExample.webm" type="video/webm"> <source src="https://res.cloudinary.com/ajonp/video/upload/v1547729145/ajonp-ajonp-com/10-lesson-angular-material-theming/fxlayoutExample.mov" type="video/mp4"></video> 

## 工具栏

在我们的例子中，我们使用了工具栏中的`nogrow`选项，这样材质图标就可以使用`fxFlex="nogrow"`保持向右移动。

sidenav.component.html

```
<mat-sidenav-container>
  <mat-sidenav opened="false" mode="over" #snav>
    <mat-nav-list>
      <mat-list-item>
        <h4
          matLine
          routerLink="/welcome"
          [routerLinkActiveOptions]="{ exact: true }"
          routerLinkActive="active-link"
        >
          Home
        </h4>
      </mat-list-item>
      <mat-list-item>
        <h4
          matLine
          routerLink="/kitchensink"
          [routerLinkActiveOptions]="{ exact: true }"
          routerLinkActive="active-link"
        >
          Kitchen Sink
        </h4>
      </mat-list-item>
      <mat-list-item>
        <h4 matLine routerLink="/books" routerLinkActive="active-link">
          Books
        </h4>
      </mat-list-item>
    </mat-nav-list>
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
      <span fxFlex>
        <a class="home-link" routerLink=".">{{ title }}</a>
      </span>
      <span fxFlex="nogrow">
        <button mat-icon-button [matMenuTriggerFor]="menu">
          <mat-icon>format_color_fill</mat-icon>
        </button>
        <mat-menu #menu="matMenu">
          <button mat-menu-item (click)="pickColor('')">
            <div class="color-swatch color-swatch-primary" mat-menu-item></div>
          </button>
          <button mat-menu-item (click)="pickColor('green')">
            <div class="color-swatch color-swatch-green" mat-menu-item></div>
          </button>
          <button mat-menu-item (click)="pickColor('pink')">
            <div class="color-swatch color-swatch-pink" mat-menu-item></div>
          </button>
          <button mat-menu-item (click)="pickColor('purple')">
            <div class="color-swatch color-swatch-purple" mat-menu-item></div>
          </button>
          <button mat-menu-item (click)="pickColor('yellow')">
            <div class="color-swatch color-swatch-yellow" mat-menu-item></div>
          </button>
          <button mat-menu-item (click)="pickColor('warn')">
            <div class="color-swatch color-swatch-warn" mat-menu-item></div>
          </button>
        </mat-menu>
      </span>
    </mat-toolbar>
    <router-outlet></router-outlet>
  </mat-sidenav-content>
</mat-sidenav-container> 
```

Enter fullscreen mode Exit fullscreen mode

## Kitchensink

在我们的 kitchensink 示例中，我们用一个`<span fxFlex>`包装每个组件，然后用`<div style="max-width: 80%" fxLayout="column" fxLayoutAlign="center center">`包装整个组件，这将允许所有内容都位于中心，并且每个`fxFlex`项目都位于 flexbox 中。您可以看到，在 html 中呈现时，每个 span 都有样式`flex: 1 1 1e-09px;box-sizing: border-box;`。

# 棱角分明的材质主题创作

您可以使用@angular/material 包附带的 4 个预构建主题，只需将它们导入 style.css 即可

`@import "~@angular/material/prebuilt-themes/indigo-pink.css";`
`@import "~@angular/material/prebuilt-themes/deeppurple-amber.css";`
`@import "~@angular/material/prebuilt-themes/pink-bluegrey.css";`

## 自定义主题

我更喜欢直接添加它们，这样你就可以创建自己的品牌颜色。如果你不允许你的品牌的身份显示出来，你就不能创建一个新的独特的网站(你知道我在说什么营销！).

我找到的最好的网站是[材质设计调色板生成器](http://mcg.mbitson.com)不像[谷歌的材质颜色工具](https://material.io/tools/color/)，DPG 允许你直接导出到 sass 变量，这正是我们在创建一个有角度的材质主题时所需要的。

确保当您选择下载(向下箭头)时，您选择 ANGULAR JS 2(材质 2)。
[![Download Design Palette Generator](img/8e4a0accb5314c36214fd7f01eb8bd9f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--7kHvY0QI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547730945/ajonp-ajonp-com/10-lesson-angular-material-theming/Screen_Shot_2019-01-17_at_8.14.48_AM.jpg)

## 更新主题文件

然后，您可以使用所有这些自定义变量来创建新的主题。sass 变量应该定义调色板中每种颜色的全光谱和对比度。

请确保你包含了所有的素材和主题函数，将它们添加到任何主题文件的顶部。你应该在任何项目中只使用一次，所以如果这在你使用各种`mixins`和`include`特性的样式文件中更有意义，请以对你的项目有意义的方式来做。

```
@import '~@angular/material/theming';
@include mat-core(); 
```

Enter fullscreen mode Exit fullscreen mode

### 萨斯变量

angular-material-router-theme . scss

```
$md-purple: (
  50: #ffffff,
  100: #d3cbe7,
  200: #b2a5d5,
  300: #8974be,
  400: #775fb4,
  500: #674ea7,
  600: #5a4492,
  700: #4d3b7d,
  800: #403168,
  900: #342754,
  A100: #f9f7fd,
  A200: #b7a2ec,
  A400: #7c5dcb,
  A700: #7256b9,
  contrast: (
    50: #000000,
    100: #000000,
    200: #000000,
    300: #000000,
    400: #ffffff,
    500: #ffffff,
    600: #ffffff,
    700: #ffffff,
    800: #ffffff,
    900: #ffffff,
    A100: #000000,
    A200: #000000,
    A400: #ffffff,
    A700: #ffffff
  )
); 
```

Enter fullscreen mode Exit fullscreen mode

为我们的新调色板创建 Sass 变量，这里 mat-palette 函数返回颜色信息和映射，并将其分配给`$angular-material-router-app-purple`。

```
$angular-material-router-app-purple: mat-palette($md-purple); 
```

Enter fullscreen mode Exit fullscreen mode

### 新材料主题

一旦你有了一个新的调色板，是时候创建一个新的 Sass 变量`$angular-material-router-app-theme-purple`来存放你的全部主题信息了。

```
$angular-material-router-app-theme-purple: mat-dark-theme(
  $angular-material-router-app-purple,
  $angular-material-router-app-accent,
  $angular-material-router-app-warn
); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我使用了默认的强调和警告，但你也可以把它们变成你喜欢的任何颜色。然后每当你使用`color="warn"`时，它可能是紫色或灰色而不是红色，请在任何主题中切换变量。

## 样式类更新

此时，我们只创建了一堆 Sass 变量，这些变量没有包含在我们的应用程序中。为了做到这一点，我们将把它们包含在周围的类中，这可以在`style.scss`文件中完成，这应该是由 Angular CLI 生成的，或者如果您克隆了 repo，它已经在`styles`文件夹中。

### 导入 SCSS 主题文件

首先，通过导入 scss `@import 'angular-material-router-theme.scss';`，确保包含了保存所有变量的新主题文件。

您可以在任何组件中这样做，以使用 Sass 变量。

### 将主题包含到类变量中

现在我们将在这个类中包含我们应用程序的整个主题(css)。

```
...
.angular-material-router-app-theme-purple {
  $theme: $angular-material-router-app-theme-purple;
  @include angular-material-theme($theme);
}
... 
```

Enter fullscreen mode Exit fullscreen mode

## 侧导航颜色选择器

现在我们已经定义了所有的主题颜色，我们可以更新侧导航按钮点击样式来包含正确的背景颜色，这将为每个菜单项分配颜色，并使用 50%的`border-radius`使其成为一个圆形。

sidenav.component.scss

```
 .color-swatch {
  position: relative;
  width: 36px;
  height: 36px;
  margin: 6px;
  border-radius: 50%;
  overflow: hidden;
}

.color-swatch-primary {
  background-color: mat-color($angular-material-router-app-primary, 500);
}
.color-swatch-primary:hover {
  background-color: mat-color(
    $angular-material-router-app-primary,
    500
  ) !important;
}
.color-swatch-green {
  background-color: mat-color($angular-material-router-app-green, 500);
}
.color-swatch-green:hover {
  background-color: mat-color(
    $angular-material-router-app-green,
    500
  ) !important;
}
.color-swatch-pink {
  background-color: mat-color($angular-material-router-app-pink, 500);
}
.color-swatch-pink:hover {
  background-color: mat-color(
    $angular-material-router-app-pink,
    500
  ) !important;
}
.color-swatch-purple {
  background-color: mat-color($angular-material-router-app-purple, 500);
}
.color-swatch-purple:hover {
  background-color: mat-color(
    $angular-material-router-app-purple,
    500
  ) !important;
}
.color-swatch-yellow {
  background-color: mat-color($angular-material-router-app-yellow, 500);
}
.color-swatch-yellow:hover {
  background-color: mat-color(
    $angular-material-router-app-yellow,
    500
  ) !important;
}
.color-swatch-warn {
  background-color: mat-color($angular-material-router-app-warn, 500);
}
.color-swatch-warn:hover {
  background-color: mat-color(
    $angular-material-router-app-warn,
    500
  ) !important;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 主题选择角度服务

你可以阅读 [Angular Services](https://angular.io/tutorial/toh-pt4) 以及为什么你应该使用它们。

## 创建拾色器服务

我喜欢在一个名为`core`的文件夹中创建我所有的服务，我也会经常把其他的东西比如模型放在这个文件夹中。

```
ng g s core/services/color-picker.service 
```

Enter fullscreen mode Exit fullscreen mode

在这项服务中，我们需要在变量`initialClass`中为应用程序设置一个初始类，如果您跟随课程视频，它显示了如何在浏览器中使用本地存储。这允许人们设置主题，而不需要在刷新时选择。所以我们先在那里查找，如果找不到，我们就添加初始类，这也是我们设置的`BehaviorSubject`。你也会注意到我们正在更新一个叫做`OverlayContainer`的东西，它来自于[材质 CDK](https://material.angular.io/cdk/overlay/overview) ，允许许多浮动面板拥有风格。

color-picker.service.ts

```
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';
import { OverlayContainer } from '@angular/cdk/overlay';

@Injectable({
  providedIn: 'root'
})
export class ColorPickerService {
  initialClass = 'angular-material-router-app-theme';
  colorClass$: BehaviorSubject<string> = new BehaviorSubject(this.initialClass);
  constructor(private overlayContainer: OverlayContainer) {
    const storageClass = localStorage.getItem('color-picker');
    console.log(storageClass);
    if (storageClass) {
      overlayContainer.getContainerElement().classList.add(storageClass);
      this.colorClass$.next(storageClass);
    } else {
      overlayContainer.getContainerElement().classList.add(this.initialClass);
    }
  }
  getColorClass() {
    return this.colorClass$;
  }
  setColorClass(className: string) {
    this.overlayContainer.getContainerElement().classList.forEach(css => {
      this.overlayContainer.getContainerElement().classList.remove(css);
    });
    this.overlayContainer.getContainerElement().classList.add(className);
    this.colorClass$.next(className);
    localStorage.setItem('color-picker', className);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更新主题类 App

现在我们已经处理好了所有的底层项目，我们需要实时更新应用程序的类。这也是为什么我喜欢在远离应用程序组件的模块中创建所有东西，因为它使这部分看起来非常干净。

你只要添加`[ngClass]="themeClass | async"`就可以将行为主体添加到整个 app 中。这将把最后一个可观察到的字符串作为类名，并在服务广播时赋值。

app.component.html

```
<div [ngClass]="themeClass | async"><app-sidenav></app-sidenav></div> 
```

Enter fullscreen mode Exit fullscreen mode

我们当然需要通过使用依赖注入添加新的`color-service`来更新我们的`app.component.ts`文件，从而告诉我们的 html 这是从哪里来的。

app.component.ts

```
import { ColorPickerService } from './core/services/color-picker.service';
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent implements OnInit {
  title = 'angular-material-router-outlet';
  themeClass;
  constructor(private colorPicker: ColorPickerService) {
    this.themeClass = this.colorPicker.getColorClass();
  }
  ngOnInit(): void {}
} 
```

Enter fullscreen mode Exit fullscreen mode

# 萨斯米辛

在那些不允许使用`color`指令的项目上创建颜色的最简单的方法是基于我们当前主题的颜色创建一个混合。例如，在我们的 kitchensink `mat-grid-list`中没有提供颜色，但是我们可以通过提供一个 mixin 将它添加到背景中。

## 给主题添加 Mixin

angular-material-router-theme . scss

```
@mixin mat-grid-mixin($theme) {
  $primary: map-get(
    $map: $theme,
    $key: primary
  );

  mat-grid-tile {
    background-color: mat-color($primary, 500);
    color: mat-contrast($primary, 500);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当每个主题类被选中时，包含 mixin `@include mat-grid-mixin($theme);`更新。现在任何带有 mat-grid-tile 的东西都将获得当前主要主题颜色的背景色。你可以从 sass 函数`map-get`中得到任何你想要的密钥，并在`mat-color`和`mat-contrast`函数中使用它。

```
.angular-material-router-app-theme-purple {
  $theme: $angular-material-router-app-theme-purple;
  @include angular-material-theme($theme);
  @include mat-grid-mixin($theme);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在主题之外创建 Mixin

比方说，在我们的 kitchensink 示例中，我们想让所有按钮从标准半径切换

[![normal radius button](img/d5199690ab270b9ad6327928fd6248a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k0lmo6Ay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547733899/ajonp-ajonp-com/tools/npukfpxtcqygtpsqe9qq.jpg)

到更圆的半径

[![45px radius](img/4d4ac36034bbdc13d7808dbfde80cb2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xZbLCidi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1547733933/ajonp-ajonp-com/tools/pffvf8nus5pegm0r8qu3.jpg)

我们可以添加一个新的 mixin 文件

角度按钮大半径 scss

```
@mixin angular-button-large-radius {
  .mat-raised-button,
  .mat-stroked-button,
  .mat-flat-button {
    border-radius: 45px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那就把这个写进我们的 kitchensink sass 吧。

```
@import '../../../styles/angular-button-large-radius.scss';

@include angular-button-large-radius; 
```

Enter fullscreen mode Exit fullscreen mode

# 工具

## 上课使用的工具

*   [角度柔性布局](https://github.com/angular/flex-layout)
*   [材质设计调色板生成器](http://mcg.mbitson.com)
*   [萨斯指南](https://sass-lang.com/guide)
*   [角形材料主题化](https://material.angular.io/guide/theming)
*   [角状材料 Kitchensink](https://github.com/angular/material2/blob/b21cfb73261e5c334280a185762ffcc475348b76/src/universal-app/kitchen-sink/kitchen-sink.ts)

## 附加工具

*   [tburleson 布局演示](https://tburleson-layouts-demos.firebaseapp.com/#/docs)
*   [物料 IO](https://material.io/)
*   [SketchApp](https://www.sketchapp.com/)
*   [材质草图插件](https://material.io/tools/theme-editor/)
*   [离子 CSS 变量](https://beta.ionicframework.com/docs/theming/color-generator)