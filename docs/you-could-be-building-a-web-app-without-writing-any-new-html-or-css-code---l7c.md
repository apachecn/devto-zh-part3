# 您可以不用编写任何新的 HTML 或 CSS 代码来构建 Web 应用程序。😳 🤯

> 原文：<https://dev.to/sirtimbly/you-could-be-building-a-web-app-without-writing-any-new-html-or-css-code---l7c>

很长时间以来，我一直在编写大量的 web 应用程序。在过去一年左右的时间里，我一直在慢慢地为基于浏览器的应用开发一个功能性的 JavaScript 库。我受到了各种基于虚拟 dom 的库和函数式反应式编程概念的启发。具体来说，有一种模式抓住了我的想象力，它叫做 [SAM](https://sam.js.org) 。

SAM 代表国家行动模式。这是一种构建应用程序的方式，与简单的普通 JS 配合得非常好。我想用这种模式写点东西，但是要借助 TypeScript 的额外帮助。

我喜欢从基本原则出发构建一些东西来理解真实情况的想法。所以我开始研究 React、Vue、Preact 和 Mithril 这些库是如何工作的。我发现你不必使用 JSX 或模板编译器，你可以只写 hyperscript 函数来呈现你的用户界面。我找到了一个在 TypeScript 中实现的[轻量级独立 virtualdom，并开始围绕它包装一些助手，以使 SAM 模式易于理解。](https://maquettejs.org)

烦恼由此而生。

[![FRETS logo](img/638eabe101e5113c19965a45f652083c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KBZ946y8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://uploads.timbendt.com.s3.amazonaws.com/dropzone/fretslogo4%401x.png)

[https://github.com/sirtimbly/frets#readme](https://github.com/sirtimbly/frets#readme)

如果你想看看 FRETS 应用程序运行起来是什么样子，那么[看看这个小演示](https://gallant-stonebraker-acf958.netlify.com/)。它演示了导航、验证和异步更新的基本功能。

看看 [frets-starter 应用程序](https://github.com/sirtimbly/frets-starter)中的代码，看看它们是如何组合在一起的。UI 组件是纯函数。它们只是返回一堆 VNodes(虚拟 Dom 节点)给虚拟 dom [渲染层](https://maquettejs.org)用于区分。函数优于对象和模板字符串，因为它们易于组合和重构。(特别是用 TypeScript)。

用户界面是一个没有副作用的纯函数，因为它只负责表达 modelProps 对象的内容，每当开发人员希望与它进行交互时，它都被冻结并标记为`Readonly`。这是 Typescript 可以真正帮助防止深度修改和意外副作用的一个方面。

## 原子 CSS 转换成 JS

当您看到这些 UI 呈现方法是如何编写的时，您会注意到它们看起来不像普通的 hyperscript 函数。

```
 import { $, $$ } from "./base-styles.ts";

  export const grayBox = (childNodes) => $.div.p2.m1.border.rounded.bgLightyGray.h(childNodes); 
```

我没有将标记名和类名作为字符串传递，而是创建了一个工具，该工具可以分析任何 CSS 文件，并生成一个充满可链接帮助器方法的 typescript 文件，以生成只能实现一组严格的类的 hyperscript 函数。这种方法非常适合原子 CSS 库，这些库提供了大量的类集合，这些类恰好应用一个可视 CSS 属性。我喜欢用[超光速粒子](https://tachyons.io)或者它更轻的祖先[低音 CSS](http://basscss.com/) 。api 很流畅，每次点击“.”您将获得自动完成功能，列出所有可添加的 CSS 属性。当你链接完原子 CSS 类后，你调用`.h()`方法并传入属性和子节点，就像你对任何其他 hyperscript 函数所做的那样。

因此，最终的结果是，你没有为你的应用程序编写任何看起来像 HTML 语法或 CSS 语法的东西。您只需编写在 Dom 中创建 HTML 的函数，并使用开发人员友好的工具来帮助您找到和使用可用于可视化格式化的 CSS 类。这就像 CSS 在 JS 中的演变，但是 JS 不需要在运行时解析或创建 CSS。

我喜欢 HTML 和 CSS。我做网站已经快 20 年了。我非常了解 CSS，我不讨厌它。我只是觉得尝试一些新的东西很有趣，作为一名开发人员，我喜欢 IDE 帮助我编写非常实用和可组合的代码，而不必做任何模板解释。

## 状态

FRETS 中的状态是您传递到 FRETS 构造函数中的自定义类。您的 UI 将是这个不可变对象的纯表达式。

```
export class TodoListProps extends PropsWithFields {
  public name: string;
  public email: string;
  public todos: ITodo[];
  public complete: number;

  constructor() {
    // set some defaults
  }
} 
```

## 动作

动作是对用户交互和事件的响应。当有人在字段中点击或输入内容时，状态必须更新。行动对此负责。首先，编写一个空类来指定动作名。

```
export class TodoListActions extends ActionsWithFields {
  public saveName: (e: Event) => void;
  // and the rest with this same type signature
} 
```

在 FRETS 对象初始化之后，您必须指定它的具体实现。

```
const F = new FRETS<TodoListProps, TodoListActions>(new TodoListProps(), new TodoListActions());

F.actions.changeName = F.registerAction((e: Event, data: TodoListProps) => {
  data.name = (e.target as HTMLInputElement).value;
  // add action specific business logic here, but not validation
  // also you can add 3rd party API calls here
  return data;
}); 
```

任何启动异步函数的动作都可以调用它的 promise `.then`处理程序中的`F.render()`。

### 型号

模型负责处理、验证和计算“动作”返回的建议的新属性。这是通过你在应用上指定的两个功能来完成的，`validator`和`calculator`。

```
// Register the state calculation function
F.calculator = (newProps: TodoListProps, oldProps: TodoListProps): TodoListProps => {
  // add your derived state business logic here
  const completedCount = newProps.todos.filter(t => (t.completed === true)).length;
  return {...newProps, completed: completedCount };
}; 
```

对显示的 UI 的所有更改都必须基于最终从“calculator”方法返回的值。它们存储在`F.modelProps`上，并且可以从 UI 呈现方法内部访问，因为它们在每次呈现时都会接收一个对应用程序的引用作为参数(在任何动作被触发后自动进行)。

所有这些工作一起创建了一个可预测的逻辑循环，这对于开发人员来说应该很容易理解。您总是确切地知道您的业务逻辑在哪里:突变发生在动作内部，而派生的值在计算器函数内部。呈现逻辑总是在呈现函数中，并且您应该能够推断出 UI 在应用程序生命周期中的任何时刻是如何进入当前状态的。

在 [API 文档](https://sirtimbly.github.io/frets/)中有更多关于这个的内容。一些更友好的文档很快就会出现。谢谢！