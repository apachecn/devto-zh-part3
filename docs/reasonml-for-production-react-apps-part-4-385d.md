# 生产反应应用的原因？🤔(第四部分)

> 原文：<https://dev.to/seif_ghezala/reasonml-for-production-react-apps-part-4-385d>

这是该系列的最后一篇文章。我们已经用 React 构建了一些东西，现在是时候分享我对使用 React 应用程序的看法了。

不过，如果你也在评估 ReasonML，我的观点应该没什么关系。这就是为什么我将分享一种方法，这种方法应该可以帮助您决定是否在生产中使用某些东西。

我们还将看到我在创建本系列时学到的 5 个技巧，它们在使用 ReasonReact 构建应用程序时非常有用。

# 类型覆盖率 vs 发展速度

## 类型覆盖范围

在编译时确保良好的*类型覆盖*很重要，因为这使我们的代码更加可靠。当应用程序的行为不同于我们预期的行为时，就会出现 bug。类型覆盖迫使我们在编译时，也就是在“编码时”(你实现它的时候)，对该行为非常明确。没错，并不是所有的 bug 都和类型有关。然而，我们越明确地输入值，就越能把检查错误(测试)的工作委托给编译器本身。

静态类型化代码的一个副作用是增强了代码的可读性。代码编辑器和语法插件可以使用编译器提供的静态类型信息，并给你提示你正在阅读的代码。代码库越大，你就越欣赏它。

## 发展速度

我们交付特性的速度绝对是我们效率的一个度量，不应该被忽略。在某些情况下，这甚至是第一要务。

开发速度也很重要，因为它是开发者体验中的一个重要因素。当一个工具可以很容易地快速实现某个东西时，它通常更容易被人们接受，也更容易被采用。这仅仅是因为我们中的大多数人喜欢我们所建立的成果，并且希望尽可能快地得到它们。

## 那么，如何决定呢？

当选择一个你每天都会用到的构建工具时，考虑类型覆盖率和开发速度是很重要的。

理想情况下，我们应该这样:

类型覆盖率:██████████ 100%
发展速度:██████████ 100%

不幸的是，这是不现实的。

谈到开发速度，JavaScript 是惊人的。这种语言非常动态，只需几行代码就可以快速完成任务:

这里有一个单行串联函数:

```
let concat = (a, b) => a + b;
// concatenate strings
concat("Hello ", "World"); // output: "Hello World"

// concatenate strings with numbers
concat("hello", 3); // output: "Hello 3 
```

然而，JavaScript 也没有提供我们从静态类型覆盖中获得的任何可预测性和可读性的好处。

## 我的裁决

我开始这个系列的时候已经知道 ReasonML 肯定是围绕 a💯在类型覆盖率方面得分。

尽管如此，我过去使用该库的经验使我对开发速度非常怀疑。这一点在我面临某些挑战时得到了证实，比如:

*   反应上下文 API。
*   异步请求。
*   正在反序列化 JSON。

然而，ReasonReact 的新语法使开发速度跃升到一个非常高的分数。我们肯定没有达到 JavaScript 的开发速度，但也不远了。事实上，我提到的问题不会妨碍您在生产环境中创建应用程序。这只能归功于 ReasonML 的灵活性和社区。

这很好，因为我们有一个工具来构建 React 应用程序，它提供了一个非常强大的覆盖类型，而不会影响开发速度。

在接下来的会议中，我提出了一些由 Reason 社区提供的技巧来解决这些问题。

# 原因反应提示

## 提示 1:对上下文做出反应

要创建和使用 React 上下文，我们必须将上下文提供者包装在一个定制组件中:

```
/* MyContextProvider.re */
let context = React.createContext(() => ());
let makeProps = (~value, ~children, ()) => {
  "value": value,
  "children": children,
};

let make = React.Context.provider(context); 
```

然后我们可以如下使用创建的上下文提供者:

```
[@react.component]
let make = (~children) => {
<MyContextProvider value="foo">
    children
  </MyContextProvider>
}

module ChildConsumer = {
[@react.component]
let make = (~children) => {
  let contextValue = React.useContext(MyContextProvider.context);
}; 
```

## 提示 2:需要 CSS

BuckleScript 提供了在不牺牲类型安全性的情况下要求 JavaScript 模块的方法。然而，当我们需要一个 CSS 文件时，我们并不真的需要任何输入。因此，我们可以直接使用 BuckleScript 嵌入原始 JavaScript 的语法，编写一个普通的 JavaScript require 语句:

```
[%raw {|require('path/to/myfile.css')|}]; 
```

## 技巧 3:使用 JavaScript React 组件🤯

下面是一个如何使用现有 JavaScript React 组件而不损害类型安全的例子:

```
[@bs.module "path/to/Button.js"] [@react.component]
external make: (
  ~children: React.element,
  ~variant: string,
  ~color: string,
  ~onClick: ReactEvent.Form.t => unit
) => React.element = "default"; 
```

### 使用 SVGR

SVGR 是一个很棒的工具，可以让你自动将 SVG 转换成 React 组件。

您可以使用前面的技巧，通过 SVGR:
自动安全地将 SVG 组件作为 React 组件导入

```
[@bs.module "./times.svg"] [@react.component]
external make: (~height: string) => React.element = "default"; 
```

> 确保安装[相应的 Webpack 加载器](https://www.smooth-code.com/open-source/svgr/docs/webpack/)并添加必要的 Webpack 配置。

## 提示#4:执行获取网络请求

为了从 React 应用程序执行网络请求，我们需要使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)。

这里有一个关于如何在 Fetch 上创建自己的包装器来发出 POST 请求的例子:

```
let post = (url, payload) => {
  let stringifiedPayload = payload |> Js.Json.object_ |>  Js.Json.stringify;

Js.Promise.(
  Fetch.fetchWithInit(
    url,
    Fetch.RequestInit.make(
      ~method_=Post,
      ~body=Fetch.BodyInit.make(stringifiedPayload),
      ~headers=Fetch.HeadersInit.make({"Content-Type":
       "application/json"}),
      (),
    ),
  )
  |> then_(Fetch.Response.json)
);
}; 
```

您可以针对其他类型的请求调整这个包装器。

## 技巧 5:处理 JSON

Reason 仍然没有合适的内置 JSON 处理。在本系列的第 2 部分中，我设法在不使用任何第三方库的情况下反序列化了一个 JSON 响应:

```
/* src/Request.re */

exception PostError(string);

let post = (url, payload) => {
  let stringifiedPayload = payload |> Js.Json.object_ |> Js.Json.stringify;

  Js.Promise.(
    Fetch.fetchWithInit(
      url,
      Fetch.RequestInit.make(
        ~method_=Post,
        ~body=Fetch.BodyInit.make(stringifiedPayload),
        ~headers=Fetch.HeadersInit.make({"Content-Type": "application/json"}),
        (),
      ),
    )
    |> then_(Fetch.Response.json)
    |> then_(response =>
         switch (Js.Json.decodeObject(response)) {
         | Some(decodedRes) =>
           switch (Js.Dict.get(decodedRes, "error")) {
           | Some(error) =>
             switch (Js.Json.decodeObject(error)) {
             | Some(decodedErr) =>
               switch (Js.Dict.get(decodedErr, "message")) {
               | Some(errorMessage) =>
                 switch (Js.Json.decodeString(errorMessage)) {
                 | Some(decodedErrorMessage) =>
                   reject(PostError(decodedErrorMessage))
                 | None => reject(PostError("POST_ERROR"))
                 }
               | None => resolve(decodedRes)
               }
             | None => resolve(decodedRes)
             }

           | None => resolve(decodedRes)
           }
         | None => resolve(Js.Dict.empty())
         }
       )
  );
}; 
```

尽管如此，我对这个解决方案并不满意，因为它导致了一个巨大的模式匹配地狱。

从那以后，在社区的帮助下，我找到了一些使用第三方库的不错的选择。

### bs-json

使用 [bs-json](https://github.com/glennsl/bs-json) ，你可以用更简洁的方式达到同样的结果。目标是使用 bs-json 将我们的 json 转换成记录。
我们首先声明我们的记录类型。在我们的例子中，我们需要处理 response JSON 对象，它有一个可选的错误 JSON 对象。我们可以这样做:

```
type error = {message: string};
type response = {
  error: option(error),
  idToken: string,
}; 
```

然后我们可以创建函数来解码 JSON 对象(响应和错误):

```
module Decode = {
  let error = json => Json.Decode.{message: json |> field("message", string)};

let response = json =>
  Json.Decode.{
   error: json |> field("error", optional(error)),
   idToken: json |> field("idToken", string),
  };
}; 
```

最后，我们可以使用我们的解码器轻松解码收到的 JSON:

```
|> then_(json => {
let response = Decode.response(json);
   switch (response.error) {
     | Some(err) => reject(PostError(err.message))
     | None => resolve(response)
  };
}) 
```

### ppx_decco

另一种实现解析 JSON 的优雅方式是使用 [ppx_decco](https://github.com/ryb73/ppx_decco) 模块。

我们首先声明我们的记录，并在它们前面加上`[@decco]`装饰符:

```
[@decco]
type error = {message: string};

[@decco]
type response = {error: option(error)}; 
```

这将创建 2 个函数，我们可以用它们来反序列化相应的 JSON 值:

*   `error_decode`
*   `response_decode`

然后，我们可以使用我们声明的记录和创建的函数轻松解码 JSON 值

```
|> then_(response =>
   switch (response_decode(response)) {
    | Belt.Result.Ok({error: Some({message})}) =>
       reject(PostError(message))
    | response => resolve(response)
  }
) 
```

# 结论

本系列旨在真实地反映构建 React 应用程序的原因。通过构建类似于我们在通常的生产环境中的 UI 特性，我们成功地掌握了好的东西和你在生产中决定使用理性时将会面临的困难。不可否认的是，rational 有一个强大的类型系统，它有一个非常强大的类型推理，可以让你写出可靠的代码。通过这个系列，我们也看到了 React 应用程序的开发速度如何使用 Reason 也不受影响。所以，是的，React 已经准备好在生产中创建 React 应用程序了！

特别感谢[论坛](https://reasonml.chat/) & [不和](https://discordapp.com/invite/reasonml)上的理智社区，特别感谢 [@yawaramin](https://twitter.com/yawaramin) 一直阅读文章并提供帮助。