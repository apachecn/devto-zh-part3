# 你可以用 Mix 做的事情

> 原文：<https://dev.to/drumusician/things-you-could-do-with-mix-2ni3>

# 用 Mix 可以做的事情

## 第一部分:创建任务

最近，我在一个会议上做了我的第一次演讲，Code Beam Lite Amsterdam，我很开心。所以为什么不以博客的形式来分享我的想法呢……Mix 这个主题是每个使用 Elixir 的人都用过的。但是你可能还没有发现它的力量(和乐趣！)这个小工具可以给你的工作流程带来。

在第一部分，我想从简单开始。我们将从创建第一个混合任务开始。现在，从 Hello World 开始很快就过时了，所以我们要做一些不同的事情。我们将创建一个显示当前时间的任务。我知道，这可能也不是世界上最伟大的奇迹，因为我们也可以只看我们电脑上的时间…但是，嘿，你想不想学习如何创建一个混合任务？

好吧，让我们开始吧！大多数教程会告诉你创建任务本身，因为这是非常简单的，但我们不这样做。先写个测试吧！

为了编写我们的第一个测试，让我们使用 Mix 最著名的任务之一 mix new 来创建我们的项目。

```
mix new clock 
```

之后，我们可以创建我们的测试文件:

```
mkdir -p test/mix/tasks
vim  test/mix/tasks/time_test.exs 
```

让我们为我们的测试创建样板文件:

```
defmodule Mix.Tasks.TimeTest do
  use ExUnit.Case, async: true

  describe "run/1" do
    test "it outputs the current time" do
      ...
    end
  end
end 
```

如果我们看一下 Mix 任务的文档，它的顶部有我们编写测试所需的所有信息:

*只需使用 Mix 即可定义 Mix 任务。以 Mix.Tasks .开始并定义了 [`run/1`](https://hexdocs.pm/mix/Mix.Task.html#c:run/1) 函数*的模块中的任务

这是我们第一个函数的样板:

```
defmodule Mix.Tasks.Time do
  use Mix.Task

  def run(_argv) do
  end
end 
```

现在是时候实际创建一个我们可以使用的测试了。那么我们对这项任务有什么期望呢？首先，让我们从将当前时间作为字符串输出到 stdout 开始。

在我们的测试中，将任务输出到 stdout 不是很有用。我们当然可以使用 capture_io 来捕获 stdout 并测试输出，如果这是我们所期望的。但是我最近看到了 Jesse Cooke 写的一篇不错的博文，它指出了一种更好的测试 mix 的 shell 输出的方法。

所以基本上你可以用 [`Mix.shell/1`](https://hexdocs.pm/mix/Mix.html#shell/1) 函数替换 Mix 用当前进程使用的 shell。所以我们就这么做，把它放在 test_helper.exs
的顶部

```
# Get Mix output sent to the current
# process to avoid polluting tests.
Mix.shell(Mix.Shell.Process)

ExUnit.start() 
```

一旦我们这样做了，我们就可以使用 assert_received 来测试流程邮箱中的最终内容。不错！

我知道我知道，我们就要到了。再多一点点。

下面是更新后的测试:

```
defmodule Mix.Tasks.TimeTest do
  use ExUnit.Case, async: true

  describe "run/1" do
    test "it outputs the current time" do
      Mix.Tasks.Time.run([])

      assert_received {:mix_shell, :info, [time]}

      assert time == # we need to define the output
    end
  end
end 
```

所以我们使用一些很好的模式匹配来捕捉命令的输出。现在我们唯一需要的是定义我们期望的输出。在这一点上，我们不想引入任何像 Timex 这样的依赖项，对于当前的标准库来说，获取当前的本地时间有点棘手。在 1.8 中有一些好的东西，但是还没有登陆。

幸运的是，我们可以使用 erlang 来解决这个问题，并使用`:calendar.local_time`函数。然后，我们可以使用 Elixir 的 NaiveDateTime 模块非常容易地转换成字符串。

这是我们最后的测试:

```
defmodule Mix.Tasks.TimeTest do
  use ExUnit.Case, async: true

  describe "run/1" do
    test "it outputs the current time" do
      Mix.Tasks.Time.run([])

      assert_received {:mix_shell, :info, [time]}

      current_time =
      :calendar.local_time
      |> NaiveDateTime.from_erl!
      |> NaiveDateTime.to_time
      |> to_string

      assert time == current_time
    end
  end
end 
```

有了它，我们就可以运行我们的测试，并通过将它添加到我们的任务中来使它通过。
注意，针对 current_time 的测试仅在使用秒粒度时有效。:)对于这个例子来说已经足够好了。

```
defmodule Mix.Tasks.Time do
  use Mix.Task

  def run(_argv) do
    time =
    :calendar.local_time
    |> NaiveDateTime.from_erl!
    |> NaiveDateTime.to_time
    |> to_string

    Mix.shell.info(time)
  end
end 
```

现在，在您的项目中，您可以运行 mix time 将当前时间输出到 stdout:)。

现在，当您运行 mix help 时，该任务不会显示在任务列表中。在本系列的下一部分，我们将探索如何做到这一点，并看看我们是否可以添加更多的功能到我们的真棒时钟。

你可以在 github 上找到这段代码的存储库:[https://github.com/drumusician/clock](https://github.com/drumusician/clock)

下次见！