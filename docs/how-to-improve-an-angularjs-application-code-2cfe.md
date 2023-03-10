# 如何改进 AngularJS 应用程序代码

> 原文：<https://dev.to/meenakshi052003/how-to-improve-an-angularjs-application-code-2cfe>

AngularJS 越来越稳定，每个新版本都消除了浪费，专注于进步。此外，大型软件公司现在开始依靠它来处理大型项目。

在这篇文章中，我们试图强调许多这样的特性，它们可以在某种程度上改进 AngularJS 应用程序，或者为更好的实现指明方向。这些建议围绕 Angular、TypeScript、ReactiveX 和一些编码最佳实践。

## TOC

*   [使用带 NGF 的 track by for](#tip-1)
*   [定值或变量，决定](#tip-2)
*   [轻量级可允许操作符的使用](#tip-3)
*   [在最重要的时候使用运算符](#tip-4)
*   [需要时加载模块](#tip-5)
*   [类型为](#tip-6)的声明
*   [应用 lint 规则](#tip-7)
*   [拆分组件以保持其小尺寸](#tip-8)
*   [使组件专用于显示目的](#tip-9)
*   [记干](#tip-10)

### 1。对使用带 NGF 的 trackBy

在 Angular 中，我们通常使用 ngFor 指令来遍历一个项目列表。这里的问题是你不能在循环过程中改变对象的值。这仅仅是因为 Angular 不跟踪列表中的项目，也不知道任何添加或删除。

但是，如果我们添加了 trackBy 选项，那么它可以让 Angular 知道哪个项目已经更改，并且只针对那个项目进行更改。

使用 trackBy 选项，我们可以在 ngFor 子句中附加一个函数，为每个列表项返回一个惟一的标识符。

使用 ngFor 的角度代码

```
<tr *ngFor="let set of sets;">
    <td><h1>{{set}}</h1></td>
</tr> 
```

使用 ngFor 和 trackBy 选项
的角度代码

```
// Update the template
<tr *ngFor="let set of sets; trackBy: trackSet">
    <td><h1>{{set.name}}</h1></td>
</tr> 
// Update the component
trackSet(index, set) {
    console.log(set);
    return set ? set.id : undefined;
} 
```

### 2。定值还是变量，决定

你应该清楚地区分你所使用的常量和那些会改变它们的值的常量。

像许多其他语言一样，Angular 也提供了内置的标识符来分离常量和变量。

不考虑常量与 let 准则的角度代码

```
let pen = 'parker pen';
let myPen = `My ${pen}`;
let yourPen = `Your ${pen};
if (iHaveParker) {
    myPen = `${myPen} is the best`;
}
if (youHaveParker) {
    yourPen = `${youPen} is the best`;
} 
```

使用 const vs. let 指南的角度代码

```
const pen = 'parker pen';
let myPen = `My ${pen}`;
let yourPen = `Your ${pen};
if (iHaveParker) {
    myPen = `${myPen} is the best`;
}
if (youHaveParker) {
    yourPen = `${youPen} is the best`;
} 
```

### 3。轻量级可写操作符的使用

在 react vex(RxJS)5.5 中，我们看到了 lettable(也称为 pipeable)运算符的引入。

不仅仅是运算符，这些都是纯函数。它们作为独立的操作符工作，代替可观察对象上的方法。

它们非常轻量级，使您的代码高度可重用，并可以减少总体构建足迹。

不带字母的角码

```
import { from } from 'rxjs/observable/from';

const numlist$ = from([1, 2, 3, 4, 5, 6, 7, 8, 9]);

numlist$
  .filter(x => x % 2)
  .map(x => x * 2)
  .scan((acc, next) => acc + next, 0)
  .startWith(0)
  .subscribe(console.log) 
```

使用字母表修改角度代码

```
import { filter, map, c } from 'rxjs/operators';

const doEven = filter(x => x % 2);
const doSum = reduce((acc, next) => acc + next, 0);
const doDouble = x => map(value => value * x); 
```

### 4。在最重要的时候使用运算符

当你打算用一个运算符来处理这些可观测量时，请在仔细评估后选择一个。以下是这种情况的一些快速示例。

a)如果出现新的发射，而您希望忽略已经出现的发射，则使用开关图。

b)为了同时处理所有发射，使用合并图。

c)为了根据它们的入口逐个处理发射，使用 concatMap。

d)在处理当前排放的同时，暂停所有新的排放，使用排气图。

最佳实践是一次使用一个操作符，而不是组合多个操作符来获得相同的效果。

### 5。需要时加载模块

它被称为懒加载，这根本不是一个新概念。但是，也可以将其应用于角度代码。例如，仅在应用程序需要时加载角度组件。

启用延迟加载后，应用程序的加载速度会快一点。通过只加载所需的模块集，它的引导时间将会缩短。

无延迟加载的角度代码

```
// File: app.routing.ts
{ path: 'not-lazy-loaded', component: NotLazyLoadedComponent } 
```

延迟加载的角度代码

```
// File: app.routing.ts
{ 
  path: 'target',
  loadChildren: 'target.module#TargetModule' 
}
// target.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';
import { TargetComponent }   from './target.component';
@NgModule({
  imports: [
    CommonModule,
    RouterModule.forChild([
         { 
             path: '',
             component: TargetComponent 
         }
    ])
  ],
  declarations: [
    TargetComponent
  ]
})
export class TargetModule {} 
```

### 6。类型为的声明

动态类型是一个突出的特性，但是最好在代码中设置变量时指定类型。

如果我们没有提到变量的类型，那么它会在运行时根据值被赋值。有时，我们会因此而陷入困境。

```
const a = 1;
const b = 'x';
const c = a + b;
console.log(`Value of c is: ${c}`)

// Result
Value of c is 1x 
```

在上面的代码中，问题是我们期望“b”也是一个数字。在声明时给它们一个适当的类型，就不会出现这样的问题。

```
const a: number = 1;
const b: number = 'x';
const c: number = a + b;

// This will give a compile error saying:
// Type 'x' is not assignable to type 'number'. 
```

### 7。应用 lint 规则

我们可以通过 lint 规则来改进代码。它会立即突出编码问题。

还有“tslint”，它有内置选项，如“no-any”、“no-magic-numbers”、“no-console”。我们可以在“tslint.json”中设置它们，使它们对代码有效。

不带绒毛的角度代码

```
public ngOnInit (): void {
    console.log('Simple message');
    console.warn('Warning message');
    console.error('Error message');
} 
```

执行后，代码将成功运行，并在控制台中显示以下内容:

```
Simple message
Warning message
Error message 
```

带绒毛的角码

```
// File: tslint.json
{
    "rules": {
        .......
        "no-console": [
             true,
             "log",    // console.log disabled
             "warn"    // console.warn disabled
        ]
   }
}
// File: xcomponent.ts
public ngOnInit (): void {
    console.log('Simple message');
    console.warn('Warning message');
    console.error('Error message');
} 
```

在执行时，lint 将显示“console.log()”和“console.warn()”语句的错误。但是“console.error”不会有任何错误，因为它不在配置中。

```
Calls to 'console.log' are not allowed.
Calls to 'console.warn' are not allowed. 
```

### 8。拆分组件以保持其小巧

如果你的代码中有大量代码成分，那么挑选并把它们分成更小的部分。尽可能使它们独立，以便它们可以在大多数情况下工作。

如果一个组件没有任何特定的逻辑，并且接受输入并相应地处理它们，那么您可以拆分它。

这种组件也称为哑组件，因为它们简单且不易出错。

### 9。使组件专用于显示目的

防止组件添加任何与显示无关的业务逻辑。最好将它们限制为显示应用程序处理的数据。

它们的目的是呈现数据并控制视图应该如何操作。不要用任何业务逻辑来混淆它们，而是为它们提供适当的方法或服务。

### 10。记得干

您不应该在应用程序的几个地方复制相同的代码。鼓励您团队中的定期代码审查，以便更快地发现任何此类不当行为。

维护重复代码是一项可怕的任务，很快就会变成一场噩梦。因此，你必须尽早发现这些问题，阻止简单的感染成为最终的死亡。

想象一下，如果你在一个发布的末尾，在这样一个代码中有一个问题需要修复，那么所有受影响的区域都需要修改。这只会使开发和测试工作翻两番。

### 脚注

开发人员的角色不仅仅是开发健壮和高质量的应用程序，还要确保它们能够维持理想的负载水平。

而且也没有能瞬间提升质量的魔杖。只有从项目一开始就制定编码基础知识，并在挑战出现时发现并做出改变来解决挑战，这才是可能的。

你可以从这篇关于[角度技巧和三头肌](https://www.techbeamers.com/angularjs-tutorial-beginners-essential-tips-tricks/)的文章中参考和学习更多角度技巧和最佳实践。