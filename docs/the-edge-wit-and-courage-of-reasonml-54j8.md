# 理性的锋芒、智慧和勇气

> 原文：<https://dev.to/iwilsonq/the-edge-wit-and-courage-of-reasonml-54j8>

*Donald CHO deva 在 Unsplash 上拍摄的照片*

ReasonML 最近偷走了我的注意力。

如果我不在工作或四处奔波，我可能会像瘾君子一样在里脊肉中挖掘一些理性。

也许我喜欢它无与伦比的增量构建速度。

也许我喜欢它是因为它与 JavaScript 没有太大的不同，但它为我带来了太多的内置优势，比如类型安全。

也许我喜欢它是因为我在学校对数学的亲和力，函数范式给了我内心一种温暖的模糊感。

我对理性的热爱有几个原因，无意双关。就像一个函数式程序使用具有不可变状态的函数组合来实现其目标一样，Reason 组合了 OCaml 和 JavaScript 的精华，这使它成为未来编程的一个强有力的候选。

## OCaml 的边缘

首先，Reason 基本上是 OCaml 的语法升级。让我先描述一下这对我们有什么帮助。

### 速度

OCaml 是一种非常快速的语言。它的增量构建时间让几乎任何其他语言都相形见绌。因为 Reason 只是 OCaml 之上的一个工具链，它保持了 OCaml 的许多相同特征。

例如，从 fastpack 的文档中可以看到这个表格，fast pack 是 Webpack 或 package 的替代品，用于捆绑 JavaScript 应用程序。

*~1100 模块/ 5.3Mb / MB Pro 2017*

|  | 长坡度的车顶 | 网络包 | 包裹 |
| --- | --- | --- | --- |
| 初始构建 | 0.811 秒 | 3.86 秒 | 11.07 秒 |
| 永久缓存 | 0.208 秒 | 不适用的 | 1.39 秒 |
| 观看模式 | 0.088 秒 | 0.226 秒 | 0.291 秒 |

Fastpack 仍然是相当新的，但是这些数字是有希望的——并且结果暗示我们可以为一个普通的 JavaScript 工作流制造超级快速的开发工具。

### 静态打字

Reason 从 OCaml 继承的另一个东西是一个强大的、几乎无法穿透的类型系统。编译器做了扎实的工作，确保您用尽了分支逻辑中的所有选项。

它还消除了一些愚蠢的错误，比如“undefined 不是一个函数”或者“无法读取属性‘x’”。采用类型脚本或流程也是如此，但是它们不会强迫你去考虑这些情况。

### ...推断得很好

它强大的类型推断清除了大量与用类型注释函数和变量相关的冗长。下面的函数将两个整数相加。不是两个浮点数或者两个字符串，只是两个整数。

```
 /* the type system knows that the arguments and the return value are ints here */
  let add = (a, b) => a + b; 
```

如果这个例子太简单，那就试试这个。请注意，我没有在函数签名中标注类型:

```
 type animal =
    | Dog
    | Cat
    | Octopus;

  let animalToString = animal =>
    switch(animal) {
    | Dog => "dog"
    | Cat => "cat"
    | Octopus => "octopus"
    };

  let getWelcomeMessage = (name, visits, animal) => {
    "Hello " ++
    name ++
    ", you've visited this website " ++
    string_of_int(visits) ++
    " times and your favorite animal is the " ++
    animalToString(animal);
  }; 
```

ReasonML/OCaml 类型系统能够根据我们的使用来推断返回类型以及每个参数。多棒啊。

### 业经验证

OCaml 是学术界和工业界公认的语言，在这些领域，安全和关键任务代码至关重要。这是它被作为推理基础的原因之一。

在脸书，OCaml 被用来构建一些重要的日常工具，比如 [Flow](https://flow.org) 和 [Hack](https://hacklang.org) 。但他们也在研究理性，在我看来，这很好，因为理性比心流带来更多的快乐。

## JavaScript 的机智

随着 Reason 的出现，出现了一种更轻便、更像 JavaScript 的语法。我在上面展示的第一个例子在普通的 JavaScript 中同样适用。编写普通 JavaScript 的一个好处是，你可以编写看起来非常简单的代码。

加上 TypeScript 或 Flow，你就有了不那么简单的代码，可能还有一些笨拙的函数签名。Reason 利用 OCaml 的类型推断来大大降低程序中的代码密度。

结果是一个简单的足迹，就像我们在 JavaScript 程序中读到的具有 OCaml 脚本所有功能的内容一样。

### 语法

Reason 语法比 OCaml 和大多数其他函数式语言更容易阅读和理解。让我们看看这个来自 [Cohttp](https://github.com/mirage/ocaml-cohttp) 的例子，它是一个类似 Express 或 Axios 的库，用于创建和处理 http 请求。

这是对库
一个简单的 GET 请求

```
open Lwt
open Cohttp
open Cohttp_lwt_unix

let body =
  Client.get (Uri.of_string "https://ianwilson.io/") >>= fun (resp, body) ->
  let code = resp |> Response.status |> Code.code_of_status in
  Printf.printf "Response code: %d\n" code;
  Printf.printf "Headers: %s\n" (resp |> Response.headers |> Header.to_string);
  body |> Cohttp_lwt.Body.to_string >|= fun body ->
  Printf.printf "Body of length: %d\n" (String.length body);
  body

let () =
  let body = Lwt_main.run body in
  print_endline ("Received body \n" ^ body) 
```

现在，如果你的背景不包含 OCaml，你可能只想把目光移开。在这个例子中有一些时髦的操作符。例如:

*   第一个字母绑定是...一个了结？
*   `>>=`和`>|=`到底是什么东西？
*   为什么它用“->”而不是“= >”？
*   选择性使用括号是怎么回事？
*   为什么有这么多管道？
*   关键词`fun`是怎么回事？
*   这里的关键字`in`是什么意思？
*   OCaml 对花括号有什么问题？

这些问题中的一些只是我们在 OCaml 中学习处理的语法上的有趣的东西。管道对于许多其他函数式编程语言来说是常见的，在某些情况下，它有助于更清晰的数据管道。

这个例子在逻辑上可能是什么样的呢？

```
open Lwt;
open Cohttp;
open Cohttp_lwt_unix;

let body = 
  Client.get(Uri.of_string("https://ianwilson.io/")) >>= (resp, body) => {
    let code = resp |> Response.status |> Code.code_of_status;
    let headers = resp |> Response.headers |> Header.to_string;
    Printf.printf("Response code: %d\n", code);
    Printf.printf("Headers: %s\n", headers);
    body |> Cohttp_lwt.Body.to_string >|= (body) => {
      Printf.printf("Body of length: %d\n", (String.length(body));
      body;
    }
  }

let () = {
  let body = Lwt_main.run(body);
  print_endline("Received body \n" ++ body);
} 
```

我认为这段代码更容易阅读。那些时髦的操作符仍然存在，但我会注意到它们的存在是为了帮助处理 OCaml 承诺，有时被称为“轻量级线程”。

Reason code 吸收了 JavaScript 风格和 OCaml 风格的优点，找到了一个既时尚又实用的中间地带。

### 轻松构建系统

与 OCaml 相比，Reason 对新手来说更容易理解，因为您可能不会花几天时间来尝试正确安装您的依赖项。使用 BuckleScript 时，通常只是另一个 npm 安装{package}，然后将该软件包的名称添加到 bsconfig 文件中。

编译为 native 的工作流程仍在进行中，但 [Esy](https://esy.sh) 正在努力使其变得更加顺畅。

如果你在写理由的时候需要辅助轮，不要害怕。您可以使用特殊指令直接在原因代码中编写 JavaScript。这不是欺骗，这种语言的设计是为了让移植代码更容易。

Reason 与 JavaScript 的互操作性使它变得更容易访问，直到开发人员习惯于去掉退路。这类似于在 TypeScript 中使用`any`。

## 理智的勇气

在过去的几周里，我一直在探索编写原生原因代码。我认为一些正在开发的项目显示了展示理性潜力的巨大勇气。

虽然我很欣赏它在前端转换成 JavaScript 时的好处，但如果我也能在服务器上编写它，那就太棒了。

我想与数据库通信，制作超快的命令行工具，并编写超高效的服务器，也许服务于 GraphQL。

我想用我为自己的 web 应用程序编写的语言来做这件事。

目前，我们可以用 JavaScript 做到这一点，但我认为我们可以做得更好。

通过进一步利用 OCaml 生态系统，我们实际上可以编写非常健壮的服务器和开发工具。

[Esy](https://esy.sh) 包管理器允许我们以方便的 package.json 方式从 npm 或 opam 安装和构建包，而不必手动破解系统依赖项。

随着 Esy 的进一步发展，后端的 Reason 将变得越来越容易被希望编写愚蠢的快速本机代码的开发人员所理解。

如果您对 Esy 已经取得的成果感兴趣，请查看以下项目:

*   fnm ，nvm 的替代品，用于管理你的 NodeJS 版本。比 nvm 快多了。
*   fastpack ，一个快得离谱的 JavaScript 捆绑器
*   revery ，一个类似于 electron 的桌面 UI 框架，除了不需要将整个浏览器下载到用户的电脑上。

开源的**森林里还有更多。**

这当然不会是我关于这个话题的最后一篇文章，所以请继续关注即将到来的更多理性福音。

如果你想知道 ReasonML 如何与 GraphQL 无缝协作，可以看看我写的这篇关于 ReasonML 与 GraphQL 的文章，类型安全 Web 应用的未来。

如果你想继续关注未来的帖子，[在这里注册我的时事通讯](https://buttondown.email/iwilsonq)！