# 在 Elixir/Ruby 中查找函数/方法

> 原文：<https://dev.to/edisonywh/functionmethod-look-up-in-elixirruby-622>

首先，我是 Ruby dev 学习灵丹妙药。我喜欢将我在 Elixir 中学到的东西与我在 Ruby 中了解的东西进行比较，因为我认为这确实加强了我对这两种语言的理解。今天，我想谈谈函数/方法查找。

这篇文章是我在通过 [PragmaticStudio 的课程](https://pragmaticstudio.com/courses/elixir)学习 [`GenServer`](https://hexdocs.pm/elixir/GenServer.html) 时提示的(我强烈推荐)，他们提到`GenServer`可以注入函数的默认实现，如果你不手动定义的话。这些默认功能是:

*   `init/1`
*   `child_spec/1`
*   `code_change/3`
*   `terminate/2`
*   `handle_info/2`
*   `handle_cast/2`
*   `handle_call/3`

如果你想覆盖它们，只需在当前模块中定义一个具有相同签名(名称+ arity)的函数。

但这让我想到，*如何*是灵药查找函数定义使这成为可能？

在进入 Elixir 之前，我们先来看看 Ruby 中的方法查找是如何工作的。

## 你爸，你爸的爸，你爸的爸的爸。

事实上，我以前写过关于这个的博客，Matz 本人也转发了这个博客。(我实际上不知道这实际上意味着什么，但我仍然会把它当作荣誉徽章佩戴)

[![edisonywh image](img/388d2736cc1db9d9adde421c4812f753.png)](/edisonywh) [## Ruby 没有类方法

### 爱迪生 Yap 11 月 9 日 185 分钟阅读

#ruby #rails](/edisonywh/ruby-has-no-class-methods-39l5)

Ruby 的方法查找链可以用一个方法调用来概括- `ancestors`。

```
Foo = Class.new
Foo.ancestors # Lookup path for instance methods
#=> [Foo, Object, Kernel, BasicObject]

Foo.class.ancestors # Lookup path for class (singleton) methods, read my blog to learn more!
#=> [Class, Module, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

就是这样——这就是你看到的 Ruby 的整个方法查找链！

实际上，`#ancestors`是定义在`BasicObject`本身上的，所以当你调用`Foo.ancestors`时，它会这样问:

```
You: Do you implement `#ancestors`? 
- Foo: No
- Object: No
- Kernel: No + BasicObject: Yes I do! 
```

Enter fullscreen mode Exit fullscreen mode

这就是它的工作原理！

这样，我们知道如果我们想要将一个函数`inject`到 Ruby 中，我们只需要确保新注入的函数是查找过程中找到的第一个方法(在`BasicObject`之前的任何地方定义`#ancestors`)。

所以，Ruby 能够做到这一点是因为`ancestors`，但是 Elixir 并没有真正的`ancestors` / `inheritance`的概念，那么这里到底发生了什么呢？

是时候停止猜测了，所以我决定自己尝试一个例子。

```
defmodule Parent do
  defmacro __using__(_opts) do
    quote do
      def injected do
        "This is from Parent"
      end
    end
  end
end

defmodule Child do
  use Parent

  def injected do
    "This is from Child"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我的预期行为是我的`Parent.injected/0`将被我的`Child.injected/0`覆盖，但是当我试图运行这个文件- `iex lookup.exs`时，弹出以下警告消息:

> 警告:此子句无法匹配，因为第 **12** 行的前一个子句总是匹配 lookup.exs: **14**

奇怪的警告，但让我们试着实际运行它。

```
iex(1)> Child.injected
"This is from Parent" 
```

Enter fullscreen mode Exit fullscreen mode

令我惊讶的是(当我看到警告消息时，惊喜有点被破坏了)，运行`Child.injected`会返回我`Parent.injected/0`而不是`Child.injected/0`，为什么？！

我决定[钻研](https://hexdocs.pm/elixir/GenServer.html)文档并找出原因，但是除了`GenServer` *将*为你注入函数之外，文档并没有告诉我太多。但是我已经知道了，我只是想知道*是怎么做到的*。

所以，医生没有帮助，接下来呢？

## 源代码！

像大多数其他语言一样，Elixir 是开源的，所以我可以很容易地钻研[源代码本身](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/gen_server.ex)。

现在，我看到了一个名为 `defoverridable`的可疑的东西[，我有一种预感，可能就是它，但我想进一步证明我的假设。](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/gen_server.ex#L818)

在谷歌上快速搜索了一下`defoverridable`后，我看到了[灵药论坛](https://elixirforum.com/t/behaviours-defoverridable-and-implementations/3338)上的一个帖子，作者是 José Valim。

## 解

所以通过这篇文章，我发现我可以使用`defoverridable`，这意味着我可以更新我的代码如下:

```
defmodule Parent do
  defmacro __using__(_opts) do
    quote do
      def injected do
        "This is from Parent"
      end

+     defoverridable injected: 0
    end
  end
end 
defmodule Child do
  use Parent

  def injected do
    "This is from Child"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在如果我们运行它..

```
iex(1)> Child.injected
"This is from Child" 
```

Enter fullscreen mode Exit fullscreen mode

瞧，成功了！完美的🎉

## 结论

在他的帖子中，José为我澄清了`defoverridable`，但他也继续说`defoverridable`是**而不是推荐的**，事实上他们正在向`@optional_callback`转移，以将方法标记为可选的，同时还有一个增强功能`@impl`(编译器将使用它来确保“孩子”实现那个功能)，所以这也是我学到的一件有趣的事情。推荐你看帖子和评论多了解一点。

我的问题在他的帖子的前 10 行中得到了回答，所以如果我能在第一时间找到它，那就太好了。话又说回来，如果我没有研究源代码，我就不会知道要寻找什么关键字，因此也不会出现在这篇文章中。

对我来说，这仍然是一次有趣的旅程，因为我一度认为长生不老药有像 Ruby 一样的遗传性。

(此外，title 在技术上是不正确的，因为 Elixir 并没有真正的“函数查找”——它所做的只是查看当前模块！)