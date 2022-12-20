# 第一个原因生产应用程序-经验教训

> 原文：<https://dev.to/baransu/first-reasonml-production-app-lessons-learned-l3o>

在过去的 5 个月里，我们在 [Codeheroes](https://codeheroes.io/) 有机会为 iOS 和 Android 开发一个移动应用程序，以及为移动应用程序提供“管理”功能的基于网络的面板。我们总是希望在下一个项目中进一步提高我们的技能，创造更好更可靠的软件，所以我们对这两个应用程序都使用了 ReasonML。

> 如果你想了解更多关于 Reason 的知识，我强烈推荐官方文档和一本由 Axel Rauschmayer 博士写的很棒的书- [探索 ReasonML 和函数式编程](http://reasonmlhub.com/exploring-reasonml/)

我以前有过使用 Reason 的经验，但是团队的其他人(一个初级人员和两个各有两年经验的开发人员)只使用 Flow、React 和 React Native 处理过类型化的 JavaScript。这个项目不仅是我们作为一个团队的第一个 Reason 项目(以前我在 Reason 中编写另一个应用程序的部分内容，以测试它是否符合我们的需求)，而且我还必须学习和支持其他开发人员的 Reason 之路。

我想分享我们在 ReasonML 中创建移动和网络应用的经验和教训！

## 为什么原因？

我们相信静态类型和函数方法，就像我们以前处理 Flow 一样。我们也试图用一种在 JavaScript 中有意义的功能性方式来编写软件。此外，我们的后端是用 Scala 编写的，所以我们的后端开发人员对我们的思维方式影响很大。

我想了很多关于将 Elm 引入我们的项目。这种语言非常封闭，在我看来，使用我们以前所有的 React 经验和多年来开发的技术来创建一个项目是不可能的。

当我在 2017 年的某个地方了解到 Reason 时，它还不够成熟，无法在生产中使用。但在 2018 年，Reason 社区在工具、绑定、会谈、博客帖子方面的大量努力使得这种语言足够成熟，可以作为我们现有 JavaScript 应用的一部分进行测试。在那之后，随着进展顺利，它让我们相信，对整个项目进行理性思考是合理的。

在一些实际问题的情况下，我们知道我们可以回到 JavaScript 来按时完成项目。幸运的是，我们不必这样做。过去的 5 个月让我们确信这是一个非常好的决定。

我们担心的事情之一是与 JavaScript 的互操作性...

## 1。不要害怕捆绑

该应用程序的核心功能是一个用于女性的医疗蓝牙设备。他们可以将设备连接到应用程序，并使用他们的骨盆肌肉“玩”简单的游戏。

我们知道我们必须使用一些没有现有绑定的库。最重要的两个是: [react-native-ble-plx](https://github.com/Polidea/react-native-ble-plx) ，我们用它来进行蓝牙连接；以及 [react-native-game-engine](https://github.com/bberak/react-native-game-engine) ，它提供了很好的 API，我们用它来创建由蓝牙设备控制的游戏。

绑定迫使我们为我们使用的每个函数定义类型。这和 Flow 或 Typescript 类型定义是一样的，但是它给了我们更多的东西！

在 Flow 中，你可以省略类型，你可以做任何你想做的事情，如果你决定写类型(或者使用已经存在的来自 [flow-typed](https://github.com/flow-typed/flow-typed) 的类型),它们可能对你撒谎，没有反映真正的 API。它们不是实现，只是类型注释。按理说，您必须创建绑定，这些绑定既是类型注释，也是我们希望如何连接到现有 JavaScript 代码的实际实现。当然，您可以创建关于 API 的绑定，但是在开发过程中，Reason 比 Flow 要快得多。

你可以不同意我的观点😉。

另一个很酷的推理特性是抽象类型。这些类型没有内部结构。您将它们定义如下:

```
/* abstract type */
type someComplexJavaScriptType;

/* normal record type */
type person = {name: string, age: int}; 
```

Enter fullscreen mode Exit fullscreen mode

您可以创建一个只在函数间传递的类型。你不必关心它是一个字符串，int 还是一些复杂的对象，以及它有什么字段。它只存在于绑定中。下面是一个使用抽象类型
的 [react-native-ble-plx](https://github.com/Polidea/react-native-ble-plx) 绑定的例子

```
type bleManager;
[@bs.module "react-native-ble-plx"] [@bs.new]
external createBleManager: unit => bleManager = "BleManager";

type subscription;
[@bs.send] external removeSubscription: subscription => unit = "remove";

[@bs.send]
external onStateChange:
  (bleManager, string => unit, bool) => subscription = "onStateChange"; 
```

Enter fullscreen mode Exit fullscreen mode

第一，我们定义了`bleManager` type，不关心它的内部。然后我们有了一个凭空创造它的函数。太好了。`subscription`型也是如此。我们知道`onStateChange`函数返回订阅，但我们不必关心它是什么。我们只需要知道有`removeSubscription`功能可以删除订阅。

一旦我们被迫创建这些绑定(编写它们的过程既不困难也不耗时)，我们就有了一个好地方来稍微调整库以满足我们的需要。我们可以创建一个中间层，在这个中间层中，我们可以重新安排函数参数的顺序以实现快速管道，或者对它们进行标记，我们可以将一个或多个函数组合在一起，我们可以为我们的应用程序用例建模 API。如果没有绑定，我们围绕这些库创建的 API 会更糟糕。

当然，这使得将绑定作为开源项目共享变得更加困难，但是我认为如果您被迫在应用程序代码中创建绑定，那么它永远不应该是首要目标。这样做很好，这样其他人也能受益，但我认为这是第二步。首先，为您需要的任何功能创建绑定，然后使用它们编写您的应用程序，以便您可以验证它们是否有用，然后将其作为开源发布并与社区共享，以收集来自其他开发人员的反馈。

## 2。渲染道具，用 let-everything 和 bs-epitath，很牛逼

渲染道具流行[反应模式](https://reactjs.org/docs/render-props.html)。它允许您在多个地方重用组件逻辑。一个流行的用例是`react-apollo`查询组件，它允许您创建 GraphQL 查询。问题是它使你的组件更大，更难阅读。如果你有一个这样的组件，这是可以的，但是如果你有两个或更多，你就创建了一个嵌套的回调函数。

PPX 来救援了！PPX 被称为语法重写者。它是 OCaml/ReasonML 编译器的扩展，允许你创建编译时宏。其中一个是由杰瑞德·福赛思创作的 [let-anything](https://github.com/jaredly/let-anything) 。再加上来自 Astrocoders 的 [bs-epitath](https://github.com/Astrocoders/bs-epitath/) ，它给了我们真正的超能力。让我们看看 React 上下文渲染道具示例:

```
/* without let-anything and bs-epitath */
let component = ReasonReact.statelessComponent(__MODULE__);
let make = _ => {
  ...component,
  render: _ => {
    <Context.Consumer>
      ...{context =>
        <BsReactNative.Text>
          {ReasonReact.string("Logged as: " ++ context.user)}
        </BsReactNative.Text>
      }
    </Contect.Consumer>
  },
};

/* with let-anything and bs-epitath */
let component = ReasonReact.statelessComponent(__MODULE__);
let make = _ => {
  ...component,
  render: _ => {
    let%Epitath context = children =>
      <Context.Consumer>
        ...children
      </Contect.Consumer>;

    <BsReactNative.Text>
      {ReasonReact.string("Logged as: " ++ context.user)}
    </BsReactNative.Text>;
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

很可爱，对吧？

但是要注意！我们陷入了在大型组件中过度使用它的陷阱，不仅使用数据呈现 props 组件，还与视图组件结合使用。

> bs-epitath 的最新变化将使得编写如下代码变得几乎不可能。但是因为他们想支持新旧语法，所以请谨慎！

```
/* don't do this, please */
let component = ReasonReact.statelessComponent(__MODULE__);
let make = _ => {
  ...component,
  render: _ => {
    let%Epitath context = children =>
      <Screen>
        <Section>
          <Card>
            <Context.Consumer>
              ...children
            </Contect.Consumer>
          </Card>
        </Section>
      </Screen>;

    /* real part of the component */
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

虽然这样做是正确的，但我认为它引入了很多间接性，使内容更难阅读。如果你想了解更多，Gabriel Rubens Abreu 写了一个很棒的后期渲染道具合成，详细描述了这个概念。

当开始使用一门新语言时，通常很难学习最佳实践并深刻理解如何对应用程序建模。这对我们来说是真的，我们在开发应用程序的核心功能时了解到了这一点...

## 3。创建更多隐藏实现细节的模块。

当我们围绕蓝牙连接创建功能时，我们必须收集设备发送的样本。我们用它们来控制游戏，并将它们发送到我们的后端进行进一步的分析。虽然将样本发送到后端相当容易，并且需要很少或不需要与样本交互，但游戏部分相当复杂，因为我们希望以各种方式处理样本。对样本的一部分应用一些变换来迭代样本，在某个时间范围内获取样本，在样本列表中找到峰值等等。

我们失败了，但没有创建专用的`Sample`模块。它应该有`sample`类型和我们希望在整个应用程序中使用的所有功能。这是一个非常糟糕的决定，影响了依赖该数据的每个部分的开发。我们有许多模块中的功能，不同开发人员实现了相同或相似的功能。总的来说，很乱。

这里学到的经验——为数据类型创建模块来隐藏实现。让我们假设您有一个简化的示例，它由一个时间戳和在该时间收集的一些值组成。示例模块应该是这样的:

```
/* Sample.re */

type t = (float, float);
type samples = list(t);

let make = (time, value) => (time, value);

let getTime = sample => sample->fst;
let getValue = sample => sample->snd;

let mapTime = (sample, fn) => {
  let (time, value) = sample;
  (fn(time), value);
};

let mapValue = (sample, fn) => {
  let (time, value) = sample;
  (time, fn(value));
};

/* more complex functions... */ 
```

Enter fullscreen mode Exit fullscreen mode

后来，您发现 tuple 不是适合您的用例的数据结构，并且您想要改变它。让我们假设记录，因为您有两个以上的元素元组。您只需更改与`type t`交互的底层函数，一切都会按预期运行。无需使用`Sample`浏览每个模块。一个文件，一次提交。完成了。

```
/* Sample.re */

type t = {time: float, value: float};
type samples = list(t);

let make = (time, value) => {time, value};

let getTime = sample => sample.time;
let getValue = sample => sample.value;

let mapTime = (sample, fn) => {
  {...sample, time: fn(sample.time)};
};

let mapValue = (sample, fn) => {
  {...sample, value: fn(sample.value)};
};

/* other functions... */ 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的例子，对我们来说是最重要的，也可能是整个开发过程中最糟糕的决定，但是我们从中学到了很多，不会再犯同样的错误。

## 4。反应——导航很难，但如果理智的话，也没那么难

在我们之前的 React 原生应用中，我们在 [react-navigation](https://reactnavigation.org/) 周围度过了一段艰难的时光。真的很难做到静态类型化，流中有声。在开发之前的应用程序时，对屏幕间传递的屏幕名称和属性进行重构给我们带来了很多麻烦。

当我们开始开发这个应用程序时，我有点担心它会如何发展。一次没有好的绑定来反应导航。但是多亏了 Callstack 的人在 [bs-react-navigation](https://github.com/callstackincubator/bs-react-navigation) 上的初步工作，我们有了一些可以改进的东西。

这里有三个部分使整个导航更加容易。

> 1.  Explicitly deliver navigation to each screen that wants to change the currently active screen.
> 2.  Variable describes all available screens and their parameters.
> 3.  `getScreen` function according to screen variables
> 
> 渲染当前活动屏幕

一个简化的版本大概是这样的:

```
/* our screen type */
type screen =
  | Products
  | Product(string);

/* get screen function rendering currently active screen */
let getScreen = (route, navigation) => {
  switch(route) {
    | Products =>
      (
        <ProductsScreen navigation />,
        screenOptions(~title="Products", ())
      )
    | Product(id) =>
      (
        <ProductScreen id navigation />,
        screenOptions(~title="Product", ())
      )
  };
};

/* example screens creating our application */
module ProductsScreen = {
  let component = ReasonReact.statelessComponent(__MODULE__);
  let make = (~navigation, _) => {
    ...component,
    render: _ =>
      <BsReactNative.ScrollView>
        <Product onPress={id => navigation.push(Product(id))} />
        /* other products... */
      </BsReactNative.ScrollView>
   };
};

module ProductScreen = {
  let component = ReasonReact.statelessComponent(__MODULE__);
  let make = (~id, ~navigation, _) => {
    ...component,
    render: _ =>
      /* product query */
      <BsReactNative.View>
        /* product details... */
      </BsReactNative.View>
   };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这使得它几乎不可能通过错误的参数，忘记一些东西，并在添加更多参数或改变现有路线的过程中
你知道你必须调整的每个地方。如果你犯了这些错误，编译器会告诉你哪里出错了，并且一旦编译成功——在 99%的情况下，它都会像预期的那样工作。

## 5。不要害怕多态变体。

我们使用 [graphql_ppx](https://github.com/mhallin/graphql_ppx) 和 [reason-apollo](https://github.com/apollographql/reason-apollo) 进行 graphql 客户端-服务器通信。在我们的模式中，我们有许多 GraphQL 枚举类型。从应用程序区域到可用的游戏及其配置。在 graphql_ppx 中，生成的代码枚举是多态变体。

当我们开始编写应用程序时，我们“害怕”多态变体，并决定为我们在 GraphQL 模式中使用的每个枚举创建正常的变体类型。我们为每一个都创建了一个模块，并提供了一系列功能来来回转换它们。我们必须将它们转换到应用程序的每个地方。从多态变体到变体，从变体到字符串，从变体到 i18n 消息。这是许多重复的逻辑，只是因为我们害怕使用多态变体。那么在 API 中添加或删除一些东西呢？我们不得不改变我们的类型两次，包括多态变体和正常变体。

我们知道多态变异和正常变异一样好。它们给你的保证更少，代码更难调试，因为当你使用它们时，编译错误可能会出现在与 please 没有直接关系的奇怪地方。尽管如此，你不应该害怕。但是请记住，它们比普通的变种更贵，所以还是要小心使用😉。

我的经验是——如果你只是从 API 向视图传递一些变体，使用多态变体完全没问题。如果它们只存在于你的应用程序中(比如导航),最好使用普通的变体。

## 社区很棒，开源也很棒！

如果没有像 [bs-react-native](https://github.com/reasonml-community/bs-react-native) 、 [re-formality](https://github.com/alexfedoseev/re-formality) 、 [graphql_ppx](https://github.com/mhallin/graphql_ppx) 和 [reason-apollo](https://github.com/apollographql/reason-apollo) 、 [reason-apollo](https://github.com/apollographql/reason-apollo) 、 [bs-react-navigation](https://github.com/callstackincubator/bs-react-navigation) 和 [bs-react-intl](https://github.com/alexfedoseev/bs-react-intl) 这样的大项目，我们的工作会更加困难，甚至可能无法完成。没有这些图书馆，我们不会选择理性。我要感谢所有参与制作这些库和我们使用的其他开源库的人。

如果你想更多地谈论我们的工作或原因，请在 [Twitter](https://twitter.com/_cichocinski) 上给我发消息！