# BuckleScript 最佳实践:公共和私有模块

> 原文：<https://dev.to/yawaramin/bucklescript-best-practice-public-and-private-modules-5ald>

在之前的一篇文章中，我提到过 OCaml 没有隐藏文件模块的方法。事实证明，在编译器层面，这是准确的；但是在构建系统级别，不太准确。

## 隐藏一个模块界面

OCaml power 用户长期以来一直能够利用编译器只能“看到”发布了`.cmi`(编译接口)文件的模块这一事实，在他们的项目中维护私有模块。模块的`.cmi`文件是模块接口的有效中间表示，编译器在构建过程中用它来判断模块中有什么。如果没有`.cmi`，编译器根本看不到模块。

## 在 BuckleScript 中

您可以利用 BuckleScript 中这个巧妙的技巧来保存尽可能多的私有模块。BuckleScript 构建系统有一个功能，允许您列出项目的“公共”模块；一旦完成，BuckleScript 将强制只有那些模块的`.cmi`文件被发布并对消费者项目可见。(原生 OCaml 的构建系统， [dune](https://dune.build/) ，也提供了类似的特性。)

它的工作方式是，你列出你的 [`bsconfig.json`](https://bucklescript.github.io/bucklescript/docson/#build-schema.json) 文件中的公共模块，在`public`字段内的`sources`字段中。例如:

```
{  "name":  "@myname/myproject",  "version":  "0.1.0",  "sources":  [  {  "dir":  "src",  "subdirs":  true,  "public":  ["Myname_Myproject"]  }  ],  ...  "warnings":  {  "error"  :  "+49+101"  },  ...  } 
```

这意味着,`src`子目录被 BuckleScript 视为源目录，通过递归遍历目录层次结构找到的所有模块都将被编译，并且公共模块(在本例中只有一个)是列出的模块。稍后我会解释`warnings`字段。

关于`public`行为的更多细节:

*   如果不存在，BuckleScript 假定所有的模块都是公共的
*   如果存在，*只有*列出的模块是公共的

## 模块化 FTW

你可能会想，我们通常不是有很多公共模块想要在这里列出吗？这不会变得很乱吗？嗯，这就是[模块化项目结构](https://dev.toa-modular-ocaml-project-structure-1ikd#the-toplevel-module-should-contain)的回报所在！在那里，您看到了如何建立一个*单个*顶层模块，该模块别名为*所有*您想要发布给用户的模块。记住，别名看起来像这样:

```
/** [src/Myname_Myproject.re] - this is the toplevel module documentation. */

/** Module-level documentation for the [Add] module */
module Add = Myname_Myproject_Add;

/** Module-level documentation for the [Subtract] module */
module Subtract = Myname_Myproject_Subtract;

/** Module-level documentation for the [Multiply] module */
module Multiply = Myname_Myproject_Multiply;

/** Module-level documentation for the [Divide] module */
module Divide = Myname_Myproject_Divide; 
```

凭借这些别名，实现模块`Myname_Myproject_Add`等也将被自动发布。所以，你通常只需要在你的`public`数组中列出*一个*顶层模块。无论是现在还是将来，从该顶层模块可到达的任何模块(通过别名或通过实现)都将获得同样的好处。(在极少数情况下，您可能希望发布多个顶层模块。)

## 有用的私有模块

如果您不能立即想到您想要保持私有的模块类型，那么测试和演示模块是一些有用的模块。您可以将您的单元测试放在与运行时代码本身相同的`src/`目录层次结构中，它们将对用户完全隐藏。您可以在与您正在开发的库相同的`src/`目录结构中构建一个完整的演示应用程序——同样，没有人能够访问它。

例如，测试:

```
/** [src/Myname_Myproject/__tests__/Myname_Myproject_Add_Test.re] */

module Add = Myname_Myproject_Add;

let () = {
  assert(Add.int(1, 1) == 2);
  assert(Add.float(1., 0.) == 1.);
}; 
```

## 命名策略

请注意，我使用了与公共模块相同的完整文件命名约定。私有模块仍然需要在它们的项目中明确命名。您不希望与同一个项目中的另一个模块发生冲突。

当然，如果你确信私有模块是完全明确的，那么你可以给它们取一个更短的名字。例如，这就是我在 Hyperapp 绑定的进展项目中所做的， [re-hyperapp](https://github.com/yawaramin/re-hyperapp/tree/695a80f62f1b1cad19d581480c0c7df572f19417/src) 。`Demo.re`模块和`Demo`子目录在项目中是明确的，因此它们的名称较短。`Yawaramin_ReHyperapp`模块是公共的，所以它有一个完全限定名。(`ReactDOMRe`和`ReasonReact`模块也是公开的，但它们需要那些确切的名字，因为它们是使 Reason 的 JSX 与 Hyperapp 一起工作的垫片。)

## `no cmi file`警告-和错误

实际上，正如我前面提到的，BuckleScript 通过简单地不输出私有模块的`.cmi`文件，使用您的构建规范来“隐藏”私有模块。当一个消费者项目试图引用一个私有模块时，他们会得到一个默认的编译警告:

```
$ bsb -w
>>>> Start compiling
[2/2] Building src/Main.mlast.d
[1/1] Building src/Main.cmj

  Warning number 49
  (No file name)

  no cmi file was found in path for module MyPrivateModule 
```

当他们试图实际访问私有模块的*内容*时，他们会得到通常的`module or file MyPrivateModule can't be found`编译*错误。*换句话说，没有实际的方法*使用*这个模块。

虽然这将防止明显的错误，但如果 BuckleScript 在默认情况下将警告 49 设为错误，这样用户甚至不能将*引用到私有模块，那就更好了。用户当然可以通过在他们的项目中设置`warnings`字段来自己尝试错误，就像我上面展示的那样。这将把警告变成一个编译错误:* 

```
>>>> Start compiling
[2/2] Building src/Main.mlast.d
[1/1] Building src/Main.cmj

  Warning number 49
  (No file name)

  no cmi file was found in path for module MyPrivateModule

  We've found a bug for you!
  /Users/yawar/src/public-test/src/Main.re

  Some fatal warnings were triggered (1 occurrences)

>>>> Finish compiling(exit: 1) 
```

此错误会精确定位源文件，并当场强制修复。

## 野外隐私

总的来说，尽管不像它的模块和接口系统那样优雅，OCaml 的私有模块特性还是很有用的。当然，除了像 Java 和 Scala(以及标准的 ML 基础系统)这样的工业级语言之外，很难想到有哪种语言提供了“私有模块”的特性。在我看来，这是 OCaml(和 ReasonML)作为“工业力量”语言的另一个标志。