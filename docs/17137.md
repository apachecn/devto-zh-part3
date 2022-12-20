# 模块化的 OCaml 项目结构

> 原文：<https://dev.to/yawaramin/a-modular-ocaml-project-structure-1ikd>

由于像 ReasonML、BuckleScript 和 Dune(以前的 JBuilder)这样的 OCaml 生态系统中的新努力，现在有相当多的新 OCaml 项目正在启动。BuckleScript 和 Dune 构建系统提供了自动的项目级命名空间，这非常方便。然而，他们确实设置了一些缺省值，我觉得这失去了项目中一些更好的特性，比如拥有一个可以用 OCaml 内置的[文档注释](https://caml.inria.fr/pub/docs/manual-ocaml/ocamldoc.html#sec351)记录的顶层模块。编辑:使用 Dune 一段时间后，你肯定可以在项目中拥有一个顶层模块。只是每个“索引”模块(见下文)在文件系统层次结构中与其子模块处于同一级别。]

在这篇文章中，我描述了一个将 OCaml 的*模块*放在最前面的项目结构。这涉及到一些跑腿的工作，但是正如我们将看到的，并不比等效的 OCaml 或 JavaScript 项目多，并且在可读性和文档方面有很大的回报。

假设你有一个项目`https://github.com/myname/myproject`，有以下主要代码文件:`add.ml`、`subtract.ml`、`multiply.ml`、`divide.ml`以及相应的接口文件(或者`.re` / `.rei`)。我建议代码文件的布局如下:

```
myproject/
  src/
    Myname__Myproject.ml
    Myname__Myproject/
      Myname__Myproject__Add.ml
      Myname__Myproject__Add.mli
      Myname__Myproject__Subtract.ml
      Myname__Myproject__Subtract.mli
      Myname__Myproject__Multiply.ml
      Myname__Myproject__Multiply.mli
      Myname__Myproject__Divide.ml
      Myname__Myproject__Divide.mli 
```

...对于其他嵌套模块也是如此。关键是在`src/`目录下有一个*单*顶层模块`Myname__Myproject.ml`，对应项目名称。它提供了以下好处:

*   项目的名称空间(因为缺省情况下 OCaml 没有)
*   项目代码文档的统一顶层
*   整个项目的入口点和索引模块

这个顶层模块可以被命名为任何名称，真的；人们使用`Myproject.ml`是为了方便。然而，我强烈推荐`Myname__Myproject`，以便用您的用户名更有力地命名它，并避免冲突。

## 为什么是`Myname__Myproject`而不是`Myname.Myproject`

在 OCaml 中，现有的模块是不可扩展的。这意味着你不能发布一个包含一个`Myname`模块的项目，然后再发布一个包含另一个`Myname`模块的项目，然后一起使用这两个项目。如果从 OCaml 的角度来看，这是不方便的，但也是可以理解的(也就是说，人们不应该弄乱现有的模块)。

在我看来，变通方法`Myname__Myproject`最终不会太麻烦。

## 顶层模块应该包含...

*   项目整体的顶层代码文档
*   项目导出模块的嵌套模块别名，以便用户可以用点标记法引用它们
*   模块别名的代码文档，以便显示相应模块的文档

例如:

```
(** [Myname__Myproject.ml] - this is the toplevel module documentation. *)

(** Module-level documentation for the [Add] module *)
module Add = Myname__Myproject__Add

(** Module-level documentation for the [Subtract] module *)
module Subtract = Myname__Myproject__Subtract

(** Module-level documentation for the [Multiply] module *)
module Multiply = Myname__Myproject__Multiply

(** Module-level documentation for the [Divide] module *)
module Divide = Myname__Myproject__Divide 
```

现在，你的库或应用程序的用户可以使用方便的点符号和自动完成来引用你的模块:`Myname__Myproject.Add`等。为了使用方便，他们可以用长的顶层模块名作为别名:`module Proj = Myname__Myproject`。他们可以通过一个方便的单一入口访问整个项目(代码和文档)。

## 导出模块 vs 未导出模块

前面我提到过，您可以在顶层模块中为您想要*导出的模块提供别名。*这更多的是一种约定；例如，如果您的源代码树中有一个文件`Myname__Myproject__Sqrt.ml`，那么模块`Myname__Myproject__Sqrt`将对您库的用户可见。OCaml 没有隐藏文件模块的方法。[编辑:这也不完全正确——见我的[下一篇关于这个的文章](https://dev.to/yawaramin/bucklescript-best-practice-public-and-private-modules-5ald)。]但是，如果不把它列在顶层模块中，就暗示了它是项目内部的。

这似乎是一种限制，但是请记住，在 Java 这样的主流语言中，您也不能隐藏公共类来使用包(至少，除非您使用新的 Java 模块特性)。

## 别名模块循环依赖

同一路径中有别名的模块不能用点符号相互引用。比如在`Myname__Myproject__Multiply.ml`中，不能调用`Myname__Myproject.Add.whatever`。问题是，`Myname__Myproject`指的是`Myname__Myproject__Multiply`(因为它是 T3 的别名)。所以后者也引用了前者，产生了循环依赖，而 OCaml 不支持独立文件间的循环依赖。

解决方案是使用相同根目录中模块的完整(下划线版本)模块名。

## 整体

我相信这个定制工作总体来说是值得的，因为它为用户(和读者，包括未来的你)提供了更好的体验。OCaml 项目在历史上将所有的源代码都转储到一个目录中，因为 OCaml 没有按目录命名空间。我在这里建议的布局以更小的块暴露了项目的复杂性，具有更容易理解的入口点。

我的希望是，在未来，OCaml 生态系统将更多地围绕模块而不是包(例如，[https://opam.ocaml.org/packages/](https://opam.ocaml.org/packages/)，[https://redex.github.io/](https://redex.github.io/))来组织自己，作为代码重用的标准可搜索单元。OCaml 模块有一些好处，比如自动编译时兼容性检查，这使得它们非常适合扮演来自其他生态系统的“包”的角色。

通过索引“导出”模块(即从顶层模块可达的别名模块)工作的可搜索模块索引将消除必须在生态系统中搜索包以找到我们需要的特定模块的努力。

另一个我还没有提到的好处是，这种结构非常适合理解 OCaml 文档注释的文档工具链。例如，我有一个 OCaml [项目，其中生成了文档](https://github.com/yawaramin/bs-webapi),我希望在不久的将来在博客上发表。