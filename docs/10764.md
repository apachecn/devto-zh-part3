# 角度布局构图完全指南

> 原文：<https://dev.to/islamuad/angular-content-projection-the-complete-guide--3dcb>

# 布局构图

> 在这篇文章中，我们将探索布局构图的许多方法。

[![Photo by Glen Carrie on Unsplash](img/d250df2882a68e5b8e9aa40ca12ddf9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3gD2lOF_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1575470522418-b88b692b8084%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1366%26q%3D80)

格伦·卡丽在 [Unsplash](https://unsplash.com/) 拍摄的照片

**什么是布局构图？**

在很多方面，布局和构图是设计的基石。它们给你的作品提供了一个结构，让你更容易浏览，从边上的空白到中间的内容。布局构图有五个基本原则

*   接近度
*   空格
*   对齐
*   对比
*   等级制度

**注意:**我不打算在这里解释每个原则，因为这超出了我们的范围，但是你可以阅读这篇[的精彩文章](https://edu.gcfglobal.org/en/beginning-graphic-design/layout-and-composition/1/)了解更多信息。

**我怎样才能做到有棱角？**

1.  组件应该在`{x:0 , y:0}`渲染
2.  组件`:host`样式应该是`display: block`为什么这个重要？如果我们在其他组件内部使用该组件，使外部组件易于设置边距和宽度。
3.  尽可能多地创建小且可重用的组件。我怎么知道这部分是否应该作为一个组件分开？。组件应该做一件事，而且只做一件事。

**什么是布局构图技巧角度提供？**

这篇文章探讨了多种角度组件的组合、混搭和混淆方式，包括:

*   自定义元素中的内容投影组件(布局和样式组件)。

* * *

## 内容投影

**什么是内容投影？**

在 Angular 中，内容投影用于投影组件(angular.io)中的内容。

我们为什么使用它？

1.  你的应用中的许多组件使用相同的结构和风格，但内容不同，换句话说就是**可重用性**。
2.  您构建一个仅用于显示的组件，另一个用于处理用户动作，换句话说就是**关注点分离**。

如何使用它？

角度杠杆使用 css 选择器，html 属性和 html 元素来实现布局组成。

### 单槽

基本上，你只需在你的 html 中添加`<ng-content></ng-content>`，这将被替换为组件外的内容

```
<!-- inside container component -->
<ng-content></ng-content> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- inside another component -->
<container-component>
  <p>Content Here</p>
</container-component> 
```

Enter fullscreen mode Exit fullscreen mode

### 多槽(定向投影)

> `ng-content`接受一个`select`属性，它允许我们为插槽设置特定 css 选择器名称。

*   使用元素`name`

```
<!-- inside container component -->
<ng-content select="slot-one"></ng-content> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- inside another component using container component -->
<container-component>
  <slot-one>Content For Slot one</slot-one>
</container-component> 
```

Enter fullscreen mode Exit fullscreen mode

如果你在一个普通的 angular cli 设置中使用它，如果你现在使用`<slot-one>`标签，你会遇到一个错误。

> 未处理的承诺拒绝:模板分析错误:“slot-one”不是已知元素，Angular 无法识别`slot-one`标记。`slot-one`既不是指令，也不是组件

解决此错误的快速方法是在模块中添加 schema 元数据属性，在模块文件中将值设置为 NO_ERRORS_SCHEMA。

```
// app.module.ts

import { NgModule, NO_ERRORS_SCHEMA } from '@angular/core'; //
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { ContainerComponent } from './container-component';

@NgModule({
  imports: [BrowserModule],
  declarations: [AppComponent, ContainerComponent],
  bootstrap: [AppComponent],
  schemas: [NO_ERRORS_SCHEMA], // add this line
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

*   使用属性`[name]` | `[name][another-name]`

```
<!-- inside container component -->
<!-- Using Single Attribute -->
<ng-content select="[slot-one]"></ng-content>
<!-- Using Multiple Attributes -->
<ng-content select="[slot][two]"></ng-content> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- inside another component using container component -->
<container-component>
  <p slot-one>Content For Slot one</p>
  <p slot two>Content For Slot two</p>
</container-component> 
```

Enter fullscreen mode Exit fullscreen mode

*   使用带有值`[name="value"]`的属性

```
<!-- inside container component -->
<ng-content select="[slot='one']"></ng-content>
<ng-content select="[slot='two']"></ng-content> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- inside another component using container component -->
<container-component>
  <p slot="one">Content For Slot one</p>
  <p slot="two">Content For Slot two</p>
</container-component> 
```

Enter fullscreen mode Exit fullscreen mode

*   使用类别`.name` | `.name.another-name`

```
<!-- inside container component -->
<!-- Using Single Class -->
<ng-content select=".slot-one"></ng-content>
<!-- Using Multi Class -->
<ng-content select=".one.two"></ng-content> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- inside another component using container component -->
<container-component>
  <p class="slot-one">Content For Slot one</p>
  <p class="one two">Content For Slot one & two</p>
</container-component> 
```

Enter fullscreen mode Exit fullscreen mode

*   不使用包装 div

正如您在前面的例子中所看到的，您可以通过用 div 或 element 包装您的内容来使用目标槽，并使用它附加选择器，但是在某些情况下，您只想将它放在那里。

> 在 ng-container 标签或任何您想要的标签上使用`ngProjectAs`角度属性

```
<!-- inside container component -->
<!-- Element -->
<ng-content select="slot-one"></ng-content>
<!-- Attributes -->
<ng-content select="[slot-one]"></ng-content>
<ng-content select="[slot][two]"></ng-content>
<!-- Attributes with Value -->
<ng-content select="[slot='one']"></ng-content>
<ng-content select="[slot='two']"></ng-content> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- Inside ngProjectAs use projected name-->
<ng-container ngProjectAs="slot-one">
  Very <strong>important</strong> text with tags.
</ng-container>
<ng-container ngProjectAs="[slot][two]">
  Very <strong>important</strong> text with tags.
</ng-container> 
```

Enter fullscreen mode Exit fullscreen mode

### 内参`*ngFor`

```
// Container Component
@Component({
  ...
  template: `
    <li *ngFor="let item of items">
      <ng-template [ngTemplateOutlet]="templateRef" [ngTemplateOutletContext]="{$implicit: item}"></ng-template>
    </li>
  `
})
class TabsComponent {
  @ContentChild(TemplateRef) templateRef:TemplateRef;
  @Input() items;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- data here is the array  input for container-component -->
<container-component [items]="data">
  <ng-template let-item>
    <!-- here we can use item -->
    {{ item }}
  </ng-template>
</container-component> 
```

Enter fullscreen mode Exit fullscreen mode

* * *