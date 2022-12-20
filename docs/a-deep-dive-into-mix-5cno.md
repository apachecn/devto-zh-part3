# 对混合音乐的深入探究

> 原文：<https://dev.to/bakenator/a-deep-dive-into-mix-5cno>

这篇文章开始时是一个关于“长生不老药”中气泡排序的简短例子。但是当我试图用`mix run bubble.exs`
运行我的脚本时，我遇到了一个有趣的消息

```
** (Mix) Cannot execute "mix run" without a Mix.Project, please ensure you are running Mix in a directory with a mix.exs file or pass the --no-mix-exs flag 
```

在这种情况下，由于文件是一个真正的没有依赖关系的单页脚本，我可以运行`elixir bubble.exs`

但是这条错误消息引起了我的注意，并引出了一些更大的问题。这面旗帜是如何工作的？Mix 是如何工作的？

# 混合任务

在这个例子中，我们使用了`mix run`,所以我想我应该尝试在 mix 源代码中找到 run 命令。Mix 是 Elixir 源代码中的一个子应用程序。所以我去了灵药回购，试着搜索了一下`"def run"`。
回购:[https://github . com/elixir-lang/elixir/tree/master/lib/mix/lib](https://github.com/elixir-lang/elixir/tree/master/lib/mix/lib)

经过一段时间的摸索，我幸运地找到了模块 Mix.Tasks.Run。这符合顶层 mix 模块文件中提供的 Mix 的高级概述。

基本上，您可以在命令行中混合提供的每个默认命令都是一个单独的任务。每个任务都有一个对应的模块，该模块带有一个被调用的“run”方法。

# 描摹混合命令

现在我们知道 Mix 正在将我们的运行命令传递给 Mix。任务。运行模块，让我们深入了解。

该文件以一个捕获所有运行方法开始:

```
 def run(args) do
    {opts, head} =
      OptionParser.parse_head!(
        args,
        aliases: [r: :require, p: :parallel, e: :eval, c: :config],
        strict: [
          parallel: :boolean,
          require: :keep,
          eval: :keep,
          config: :keep,
          mix_exs: :boolean,
          halt: :boolean,
          ...
        ]
      )

    run(args, opts, head, &Code.eval_string/1, &Code.require_file/1)
    unless Keyword.get(opts, :halt, true), do: System.no_halt(true)
    Mix.Task.reenable("run")
    :ok
  end 
```

该方法解析命令行选项，并将解析的数据传递给带有更多参数的 run 方法。有趣的是- no-halt 选项是在这一级处理的。

同样在 OptionParser 文件的深处，我发现这一行很有趣。
`defp tag_option("no-" <> option = original, config) do`
`no-`语法用作选项的开关。我总是假设`--halt`和`--no-halt`会被识别为单独的字符串

接下来，我们有扩展参数的 run 方法。
`def run(args, opts, head, expr_evaluator, file_evaluator) do`
首先，它对命令行参数进行更多的处理，然后我们到达这个关键的代码块。

```
cond do
      Mix.Project.get() ->
        Mix.Task.run("app.start", args)

      "--no-mix-exs" in args ->
        :ok

      true ->
        Mix.raise(
          "Cannot execute \"mix run\" without a Mix.Project, " <>
            "please ensure you are running Mix in a directory with a mix.exs file " <>
            "or pass the --no-mix-exs option"
        )
    end 
```

这里我们可以看到`--no-mix-exs`选项发挥作用的确切位置。

首先，它检查我们是否在一个普通的混合项目目录中。如果返回 false，它将检查`--no-mix-exs`的参数。最后，它将抛出我们在开始时看到的错误。谜团解开了！

# 越走越深

### 混合。Project.get()

我花了很长时间才找到这东西的工作原理！

当 Mix 应用程序第一次被调用时。CLI 模块尝试编译当前目录下的`mix.exs`文件。默认情况下，该文件将包含行`use Mix.Project`。一旦文件被编译，它就在 Mix 中运行这个编译后钩子。项目

```
 def __after_compile__(env, _binary) do
    push(env.module, env.file)
  end 
```

这个钩子将 env.module 名称推到一个内部堆栈上，这个堆栈在`Mix.Project.get()`命令中检查。

### 混合。Task.run("app.start "，args)

如果当前目录中有一个 mix.exs 文件，run 函数将移到第`Mix.Task.run("app.start", args)`行。这启动了一个新任务，正如你可能猜到的那样，这个任务就在组合中。Tasks.App.Start 模块。

这个模块从它的 run 方法开始，向下执行几个函数，直到到达第`case Application.start(app, type) do`行。这将应用程序向下传递给 erlang 调用`:application.start(app, type)`来启动应用程序。

# 回混。任务.运行.运行

在检查我们是否在一个项目目录中之后，这个函数还有一些技巧。

```
 process_load(opts, expr_evaluator)

    if file do
      if File.regular?(file) do
        file_evaluator.(file)
      else
        Mix.raise("No such file: #{file}")
      end
    end 
```

我想包括这一点，因为我认为它特别整洁。如果运行类似`mix run -e "IO.inspect 123"`的命令，Mix 将运行内联代码。这发生在`process_load`函数中。它检查是否提供了-e 或 eval opts，然后使用 Code.eval_string/1 函数动态运行代码。

最后，Mix 将使用&Code.require_file/1 函数运行命令行中给定的任何单个文件。

# 总结起来

谢谢你能走到这一步。准确跟踪 Mix 应用程序中发生的事情是一个挑战，但这是非常值得的。Mix 可能是最常用的灵丹妙药应用程序了！

现在你已经知道了所有关于混合任务的知识，也许你会有灵感去写你自己的？[https://dev . to/drumusician/things-you-could-do-with-mix-2ni 3](https://dev.to/drumusician/things-you-could-do-with-mix-2ni3)

用 Elixir 编码很有趣！