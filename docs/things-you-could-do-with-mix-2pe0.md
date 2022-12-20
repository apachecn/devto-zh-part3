# 你可以用 mix 做的事情

> 原文：<https://dev.to/drumusician/things-you-could-do-with-mix-2pe0>

## 用 mix 可以做的事情

### 第二部分:传递参数

这是我探索混合任务系列的第二篇文章。请在[此处]找到第一部分。在第一部分中，我们探索了如何使用测试来创建一个简单的混合任务，以驱动我们的实现。在这一集里，我们将看到如何通过在命令行上传递参数来扩展我们的任务，这些参数将影响我们时钟的输出。

目前我们在运行 mix 帮助时还没有看到我们的 mix 任务。那么，当调用 mix 帮助时，我们如何让 mix 任务出现在任务列表中呢？要让 mix 帮助输出我们的任务，我们必须做的唯一一件事就是向我们的任务模块添加一个`@shortdoc`模块属性。是的，就这么简单。

因此，我们通过添加 shortdoc 属性来更新我们的任务模块。

```
defmodule Mix.Tasks.Time do
  use Mix.Task

  @shortdoc "A mix clock that can tell time. ;)"
  ...
end 
```

当我们调用`mix help`时，我们将在输出中看到我们的任务。

```
...
  mix profile.fprof     # Profiles the given file or expression with fprof
  mix run               # Starts and runs the current application
  mix test              # Runs a project's tests
  mix time              # A mix clock that can tell time. ;)
  mix xref              # Performs cross reference checks
  iex -S mix            # Starts IEx and runs the default task
... 
```

完成后，让我们看看是否可以为我们的混合时钟添加一个功能。目前`mix time`任务只输出当前当地时间。如果我们也可以通过一个时区来获取世界上任何其他地方的时间，那不是很好吗？

上次我提到当前的 Elixir DateTime 模块还不支持时区功能。幸运的是 1.8.0-rc0 刚刚登陆，所以让我们来利用一些在 Elixir 1.8 中出现的新的和闪亮的特性。DataTime 模块现在包括一个接受时区的`now/2`函数。默认情况下，它只支持“Etc/UTC”时区，但我们可以很容易地添加一个新的时区数据库。我们可以将 TzData 时区数据库添加到 mix.exs 文件中:

```
...
defp deps do
  [
    {:tzdata, git: "https://github.com/lau/tzdata.git", tag: "master"}
  ]
end
... 
```

现在更新我们的依赖关系:

```
mix deps.get 
```

并将 elixir 配置为使用 config.exs 中 time_zone_database。

```
config :elixir, :time_zone_database, Tzdata.TimeZoneDatabase 
```

让我们先看看是否能得到传入的参数。使用测试。

我们创建测试来使用标准库中的新特性，如下所示:

```
test "it accepts a timezone as an argument" do
    Mix.Tasks.Time.run(["Australia/Sydney"])

    assert_received {:mix_shell, :info, [time]}

    {:ok, current_date_time_in_time_zone} = DateTime.now("Australia/Sydney")

    current_time =
      current_date_time_in_time_zone
      |> DateTime.truncate(:second)
      |> DateTime.to_time
      |> to_string()

    assert time == current_time
  end 
```

我们的测试现在失败了，因为我们还没有更改任何实现，所以让我们看看如何传入参数。
目前我们只有一个`run/1`函数匹配所有的函数调用。我们需要根据传入的参数数量来区分这个调用。基本上，当参数列表为空时，我们应该返回当前的本地时间，现在我们假设另一个选项是一个时区列表。
图案匹配来救援！

我们将原来的`run/1`函数改为只在空列表上调用，如下所示:

```
def run([]) do
  # implemetation details unchanged
end 
```

接下来，我们创建新的实现，假设传入了一个参数。

```
def run([time_zone])  do

  {:ok, current_date_time_in_time_zone} = DateTime.now(time_zone)

  time =
      current_date_time_in_time_zone
      |> DateTime.truncate(:second)
      |> DateTime.to_time
      |> to_string()

  Mix.shell.info(time)
end 
```

一旦我们加上我们的测试通过。耶！

现在既然快过年了，那就多加一个功能吧。让我们看看新年是否已经到了我们指定的时区！

我们将添加一个小测试，使用悉尼作为时区，因为在写这篇文章时，新年已经到来...:)当然这是一个非常脆弱的测试，但现在它只是为了好玩...:)

```
test "it shows if the new year has arrived" do
    Mix.Tasks.Time.run(["Australia/Sydney"])

    assert_received {:mix_shell, :info, [time]}

    {:ok, current_date_time_in_time_zone} = DateTime.now("Australia/Sydney")

    current_time =
      current_date_time_in_time_zone
      |> DateTime.truncate(:second)
      |> DateTime.to_time
      |> to_string()

    assert time == current_time <> " Happy New Year Sydney!"
  end 
```

现在开始实施:

```
def run([time_zone])  do
  Mix.Task.run "app.start" # we need this to make sure we have our dependencies loaded. More on that in a later blogpost...

  {:ok, current_date_time_in_time_zone} = DateTime.now(time_zone)

  day_of_year =
    current_date_time_in_time_zone
    |> DateTime.to_date
    |> Date.day_of_year

  city =
      String.split(time_zone, "/")
      |> Enum.drop(1)
      |> to_string()

  time =
      current_date_time_in_time_zone
      |> DateTime.truncate(:second)
      |> DateTime.to_time
      |> to_string()

  output =
    case day_of_year do
      1 ->
        "#{time} Happy New Year #{city}!"
      _ ->
        "Time in #{city}: time"
    end

  Mix.shell.info(output)
end 
```

当我现在运行它时，它将失败并显示以下消息:

```
1) test run/1 it shows if the new year has arrived (Mix.Tasks.TimeTest)
    test/mix/tasks/time_test.exs:37
    Assertion with == failed
    code:  assert time == current_time <> " Happy New Year Sydney!"
    left:  "time in Sydney: 23:53:11"
    right: "23:53:11 Happy New Year Sydney!"
    stacktrace:
      test/mix/tasks/time_test.exs:51: (test) 
```

但是当我再等 7 分钟的时候，我的测试通过了...:)我们将在下一次修复我们脆弱的测试，并看看如何使用 OptionParser 使我们的实现更加可靠。

希望你喜欢这篇文章。在新的一年里，将会有更多来自我的关于灵药和混合物的探索。所以保持警惕！

现在，新年快乐！

下次见！