# NGRX -从头开始，第三部分，NGRX 商店

> 原文：<https://dev.to/angular/ngrx-from-the-beginning-part-iii-ngrx-store-13bf>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> NGRX 是 Redux 模式的一个实现。它是为框架 Angular 设计的，增加了 RxJS 的类型脚本和反应性

本文是系列文章的一部分:

*   [NGRX —从头开始](https://dev.to/azure/ngrx-from-the-beginning-7k7)，第一部分，发布订阅，
*   [NGRX —从头开始，第二部分](https://dev.to/azure/ngrx-from-the-beginning-part-ii-redux-4006)，第二部分 Redux
*   NGRX —从头开始，第三部分，NGRX store，**我们在这里**
*   NGRX —从头开始，第四部分，NGRX 存储，改进我们的代码
*   NGRX —从头开始，第五部分，NGRX 效果，*进行中*
*   NGRX —从开始，第六部分，NGRX 实体，*进行中*

如果你阅读这一部分时不知道 Redux 或 Pub-Sub 是什么，我建议你先阅读第一部分和第二部分。我们不会解释 Reducer 或 Action 之类的东西，也不会解释我们如何*发送*消息，因为那会使这篇文章太长。

在本文中，我们将介绍

*   **设置并安装**，我们将解释如何搭建一个项目，并安装必要的依赖项，我们还将添加我们的第一个状态属性和 Reducer 函数
*   **显示数据**，这里我们将介绍如何注入商店服务以及如何在我们的标记中显示来自商店的数据
*   **更改状态**，在我们展示了如何显示数据之后，我们将在这里关注如何更改数据，或者如 Redux 所指，如何分派动作

## 设置和安装

好的，我们需要做以下事情:

1.  搭建一个有棱角的工程
2.  安装@ngrx/store
3.  设置@ngrx/store

### 安装

好了，首先，让我们通过在终端中键入以下命令来创建一个 Angular 项目:

```
ng new NameOfMyProject
cd NameOfMyProject 
```

此后，我们留在航站楼，进入:

```
npm install @ngrx/store 
```

这安装了我们的库，我们准备好配置我们的项目到`@ngrx/store`。

### 设置

好，我们有一个项目，让我们设置它。我们需要做两件事:

1.  导入`StoreModule`
2.  调用`StoreModule.forRoot()`并为其提供一个由多个状态、缩减器对组成的对象

好的，我们打开`app.module.ts`并输入:

```
import { StoreModule } from '@ngrx/store'; 
```

接下来我们打电话给`StoreModule.forRoot()`。如上所述我们需要给它一个由若干状态组成的对象，还原成对，像这样:

```
{
  state: stateReducer, 
  secondState: secondStateReducer
  ...
} 
```

好了，我们理解了整体的形状，那么让我们用实际的代码来展示它可能是什么样子:

```
const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    default:
      return state;
  }
} 
```

接下来，在同一个文件`app.module.ts`中，我们需要将我们的`StoreModule`添加到我们的`imports`列表中，就像这样:

```
imports: [
  BrowserModule,
  AppRoutingModule,
  StoreModule.forRoot({
    counter: counterReducer
  })
] 
```

如你所见，我们现在向包含我们的`counter`、`counterReducer`对的`StoreModule.forRoot()`提供一个对象。

## 显示数据

好了，这包括了让@ngrx/store 工作所需的最基本的设置。然而，我们现在还不能展示我们的数据。为了显示数据，我们需要将`Store`服务注入到旨在显示数据的组件的构造函数中。让我们进入`app.component.ts`并添加所需的导入:

```
import { Store } from '@ngrx/store'; 
```

以及需要注入组件类构造函数:

```
constructor(private store: Store<any>) {} 
```

在这一点上，你可能会对使用`any`作为`Store`的模板参数感到疑惑，让我们暂时把它放在一边，然后再稍微改进一下。

### 选择数据

好的，如果我们使用方法`pipe()`和操作符`select()`,`Store`服务会让我们从它的内部状态中选择数据，就像这样:

```
import { Store, select } from '@ngrx/store';

@Component({
  ...
})
export class AppComponent {
  counter$;

  constructor(private store: Store<any>) {
    this.counter$ = this.store.pipe(
      select('counter')
    )
  }
} 
```

我们从`@ngrx/store`导入了`select()`操作符，并且我们还将`pipe()`调用的结果赋给了一个变量`counter$`。`counter$`是可观察的，这意味着我们可以使用`async`管道来显示内容。现在，让我们转到我们的`app.component.html`文件，键入以下内容:

```
My counter: {{ counter$ | async }} 
```

保存安润你的项目，你的浏览器现在应该显示`My counter: 0`

### 带功能选择器

到目前为止一切正常，但是让我们回到如何在`AppComponent`构造函数中注入我们的`Store`服务。我们给了它模板参数`any`。我们肯定可以改善这一点，但如何改善呢？想法是给它一个表示你的商店状态的接口。目前，您的商店状态如下:

```
{
  counter
} 
```

所以让我们创建一个与此对应的接口，即:

```
// app-state.ts
export interface AppState {
  counter: number;
} 
```

上面我们声明了唯一的`existing`状态属性`counter`，并且声明了它的类型。好了，下一步是使用`AppState`作为我们的商店模板，所以让我们更新`app.component.ts`。
首先添加这个导入:

```
import { AppState } from './app-state'; 
```

现在让我们用`AppState` :
代替`any`

```
constructor(private store: Store<AppState>){} 
```

我们可以做得更多，我们可以通过从接受字符串的`select()`方法切换到接受函数的方法来利用这一点，就像这样:

所以这个:

```
this.counter$ = this.store.pipe(
    select('counter')
  ) 
```

变成:

```
 this.counter$ = this.store.pipe(
    select(state => state.counter)
  ) 
```

你问这个有什么了不起的？现在，如果我们试图从不存在的状态中选择一个属性，编译器会帮助我们，只有`state.counter`是有效的选择。同样，如果我们的状态是嵌套的，这是我们能够到达像`state.products.data`这样的状态的唯一方法，例如，如果我们有一个状态`products`。

## 改变状态

好的，很好，我们可以从我们的商店显示一个状态属性，改变它怎么样？为此，我们有一个`dispatch()`方法，允许我们向商店发送消息。该消息采用动作对象的形式。好了，让我们构建一下我们的 UI。进入`app.component.html`并添加以下内容:

```
// app.component.html

<button (click)="increment()">Increment</button> 
```

现在，转到`app.component.ts`并添加方法`increment()`，就像这样:

```
@Component({})
export class AppComponent {
  increment() {
    // add dispatch call here
  }
} 
```

好的，我们需要做以下事情来向商店发送消息:

*   构造一个消息类型为`INCREMENT`的动作对象
*   在商店服务实例上调用`dispatch()`

好的，为了构造一个对象，我们可以使用一个对象文字，就像这样:

```
const action = { type: 'INCREMENT' }; 
```

使用消息增量的原因在于我们如何构造`counterReducer`函数，我们来快速看一下:

```
const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    default:
      return state;
  }
} 
```

很明显，它期望消息具有属性`type`，并且消息类型值为`INCREMENT`。好的，看起来不错，我们打电话给`dispatch()` :

```
@Component({})
export class AppComponent {
  // constructor omitted for brevity
  increment() {
    const action = { type: 'INCREMENT' };
    this.store.dispatch(action);
  }
} 
```

调用`increment()`将导致我们的消息被发送到商店，调用`counterReducer()`并将我们的`counter`值更新 1。你自己试试。

## 总结

这就是我们计划在这一部分讨论的全部内容。我们已经展示了如何在我们的`StoreModule.forRoot()`中安装`@ngrx/store`以及如何设置状态、减速器对。此外，我们还展示了如何从`Store`服务中选择数据，以及如何使用提到的`Store`服务及其`dispatch()`方法来更新数据。在下一部分，我们将看一个更丰富的例子，看看我们如何使用枚举和内置接口来改进事情，所以请继续关注:)