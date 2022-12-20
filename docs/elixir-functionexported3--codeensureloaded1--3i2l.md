# 仙丹:功能 _ 导出？/3 する前には代码.确保 _ 已加载/1 を呼び出す

> 原文：<https://dev.to/gumi/elixir-functionexported3--codeensureloaded1--3i2l>

本文将讨论“[function_exported？ /3 之前，根据调用 Code.ensure_loaded/1 的](https://qiita.com/melpon/items/c43b2a41c2323e29d653)】进行修改，整理了文章。

# function_exported？ 在/3 中验证行为函数的实现

从安装了行为的模块调用这些函数时，事先通过[`function_exported?/3`](https://hexdocs.pm/elixir/Kernel.html#function_exported?/3)确认是否具备该函数。 例如，假设您有一个实现了以下`Foo`行为的`FooImpl`模块:

```
defmodule Foo do
  @callback foo() :: :ok
end

defmodule FooImpl do
  @behaviour Foo

  @impl Foo
  def foo() do
    :ok
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

从要调用的函数中，确认是否已在`function_exported?/3`中导出参数模块的函数，即是否实现了`Foo`行为，然后调用，如下所示。 这样可能看起来完全没有问题的代码。

```
defmodule Bar do
  def call_foo(mod) do
    if not function_exported?(mod, :foo, 0) do
      raise "Foo behaviour is not implemented"
    end
    mod.foo()
  end
end

call_foo(FooImpl) 
```

Enter fullscreen mode Exit fullscreen mode

但是，该代码从`mix`启动时，可能会出现**不动作的情况**。

# 为什么不动作

在文档的[`function_exported?/3`](https://hexdocs.pm/elixir/Kernel.html#function_exported?/3)一项中，有以下注意事项。 未预先加载参数的模块时，`function_exported?/3`不会读取模块。 也就是说，不仅是未实现函数时，模块还未加载时也会返回`false`。

> 注意，如果模块没有加载，这个函数不会加载它。

要使用模块，模块必须预先加载到 Erlang VM 中。 要理解这一点，您需要知道 Erlang 加载模块的策略。 有以下两种策略( Erlang VM 的默认策略是后者) :

*   **嵌入式模式**启动 APP 程序时，将加载所有模块。
*   **交互模式**最初调用该模块的函数时，将加载该模块。

从`mix`移动 APP 键时，进入对话模式。 因此，移动上述代码时，如果`FooImpl`模块的函数都还没有被调用，`function_exported?/3`就会返回`false`并出错。 另一方面，如果先执行的代码调用了`FooImpl`模块的函数，则`function_exported?/3`的返回值为`true`，正确地调用了`mod.foo()`。

也就是说，从`mix`启动时，动作会根据通过了哪个代码而变化。 这就是我说有“不动作的情况”的理由。 另外，如果用[Distillery](https://hex.pm/packages/distillery) 生成，则默认为[嵌入模式](https://github.com/bitwalker/distillery/blob/7790d50913b766ffdbbb5ab46e44c9f615d6c249/priv/libexec/env.sh#L16)。

# 预先确认模块的加载

文档的`function_exported?/3`一项会提示参考[`Code.ensure_loaded/1`](https://hexdocs.pm/elixir/Code.html#ensure_loaded/1)。 这是确认是否加载了参数的模块，如果结果还没有的话就可以加载的函数。 如下所示添加`Code.ensure_loaded/1`的调用，将执行行为的模块的读取。

```
defmodule Bar do
  def call_foo(mod) do
    Code.ensure_loaded(mod)
    if not function_exported?(mod, :foo, 0) do
      raise "Foo behaviour is not implemented"
    end
    mod.foo()
  end
end

call_foo(FooImpl) 
```

Enter fullscreen mode Exit fullscreen mode

不过，模块的读取可能会因为某些原因而失败。 此时的返回值为`{:error, reason}`，因此写下错误的分支更严密(如果成功则为`{:module, module}`)。 不过，在这种情况下，下面的`function_exported?/3`会返回`false`，结果会产生没有安装行为的错误。 如果是这样简单的代码，有时这样就可以了吧。