# 你可以用 mix 做的事情

> 原文：<https://dev.to/drumusician/things-you-could-do-with-mix-5d9m>

## 用 mix 可以做的事情

### 第三部分:重构，函数式风格！&介绍 OptionParser

又见面了！这是我的博客文章系列的第 3 部分，探索你可以用 mix 做的事情。如果你没有读过前两部，你可以在这里找到那些和[这里](https://www.theguild.nl/thing-you-could-do-with-mix-2/)。上一次，我们在 Elixir 1.8.0 中探索了一些新的日期时间特性，并根据新年到来的时间更改了输出。我们为此编写的测试非常脆弱，因为它依赖于实际时间。因此，让我们首先确保我们的完整测试套件再次变绿，并借此机会重构我们的代码，并在新的一年开始清理我们的实现。

这是我们写的测试，检查新年是否已经到来。

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

现在，我们可以通过时间旅行或创建一个当前时间的模拟，或其他听起来很复杂的方法来测试这一点。但是让我们从一个不同的角度来解决这个问题。首先，让我们更仔细地看看我们接受时区的`run/1`函数的实现。

```
def run([time_zone]) do
  Mix.Task.run "app.start"

  {:ok, current_date_time_in_time_zone} = DateTime.now(time_zone)

  day_of_year =
    current_date_time_in_time_zone
    |> DateTime.to_date()
    |> Date.day_of_year()

  city =
    String.split(time_zone, "/")
    |> Enum.drop(1)
    |> to_string()

  time =
    current_date_time_in_time_zone
    |> DateTime.truncate(:second)
    |> DateTime.to_time()
    |> to_string()

  output =
    case day_of_year do
      1 ->
        "#{time} Happy New Year #{city}!"

      _ ->
        "time in #{city}: #{time}"
    end

  Mix.shell().info(output)
end 
```

如果我们现在运行测试，它会失败，因为输出与我们在 12 月 31 日收到的新年祝福不匹配。我们可以通过在不同的函数中提取这一部分，然后单独测试，从而使这一点非常容易测试。

让我们为我们希望看到的新功能创建一个新的测试。

```
test "the output contains a new years greeting on the first day of the year" do
  time = ~T[02:00:00.005]
  date = ~D[2019-01-01]
  {:ok, date_time} = NaiveDateTime.new(date, time)

  output = Mix.Tasks.Time.format_output(date_time, "Australia/Sydney")
  truncated_time = time |> Time.truncate(:second) |> to_string()

  assert output == truncated_time <> " Happy New Year Sydney!"
end 
```

我在这里硬编码了时区，但是现在已经足够了。我们可以通过提取一些处理输出格式化的函数来完成这个测试。我们将继续将其他转换也提取为私有函数，以下面的实现结束:

```
def format_output(date_time, time_zone) do
  time = time(date_time)
  city = city(time_zone)

  case day_of_the_year(date_time) do
    1 ->
      "#{time} Happy New Year #{city}!"

    _ ->
      "time in #{city}: #{time}"
  end
end

defp city(time_zone) do
  String.split(time_zone, "/")
  |> Enum.drop(1)
  |> to_string()
end

defp day_of_the_year(date_time) do
  date_time
  |> DateTime.to_date()
  |> Date.day_of_year()
end

defp time(date_time) do
  date_time
  |> DateTime.to_naive
  |> NaiveDateTime.truncate(:second)
  |> NaiveDateTime.to_time()
  |> to_string()
end 
```

这比我最初想的要多得多的重构，但是我们很好地把它分成了独立的功能！

现在我们为什么不给我们的时钟增加一个新功能呢！我希望能够给我们的时钟添加一个标志，以 12 小时或 24 小时格式输出时间。当前输出使用 24 小时时间，因此我们需要将其转换为 am / pm。

正如我在上一篇文章中提到的，我想将 [OptionParser](https://hexdocs.pm/elixir/OptionParser.html) 添加到我们的任务中。基本上`OptionParser`给我们带来了一些添加别名和将我们的参数转换成选项和开关的又好又方便的方法。

像往常一样，让我们测试我们的实现方式。

这是我们的测试。我已经创建了一个助手函数来创建我们测试中的 am 或 pm。

```
test "the output can be switched to 12-hour format with a flag" do
  Mix.Tasks.Time.run(["Europe/Amsterdam", "--am-pm"])

  assert_received {:mix_shell, :info, [time]}

  {:ok, current_date_time_in_time_zone} = DateTime.now("Europe/Amsterdam")

  current_time =
    current_date_time_in_time_zone
    |> DateTime.truncate(:second)
    |> DateTime.to_time()

  suffix = suffix(current_time.hour)

  assert time == "time in Amsterdam: #{to_string(current_time)}" <> "  #{suffix}"
end

defp suffix(hour) when hour < 12, do: "am"
defp suffix(_), do: "pm" 
```

这个测试很快就会失败，因为它只需要一个时区作为参数。这就是`OptionParser`的用武之地。OptionParser 有一个 [`parse/2`](https://hexdocs.pm/elixir/OptionParser.html#parse/2) 函数，它从带有选项列表的命令行中获取参数，并返回一个三元素元组，其中包含:{ parsed_switches，remaining arguments，invalid_options }。我强烈推荐使用`:strict`模式，它可以让你明确定义你允许的开关以及你期望的类型。

这是我们使用`OptionParser`修改后的`run/1`函数。

```
def run(args) do
  Mix.Task.run "app.start"

  {opts, [time_zone], _} = OptionParser.parse(args, strict: [am_pm: :boolean])
  {:ok, current_date_time_in_time_zone} = DateTime.now(time_zone)

  output = format_output(current_date_time_in_time_zone, time_zone, opts)

  Mix.shell().info(output)
end 
```

如您所见，我们现在也将 OptionParser 中的选项传递到 format 函数中。我们更新它，这样我们就可以相应地改变输出。下面你可以看到新的实现增加了一个后缀函数，当标志被传递时，它处理我们的 am/pm 输出。我非常喜欢 Elixir，你可以通过创建不同的函数声明来对函数参数进行模式匹配！当 am_pm 标志存在时，下面的第一个`suffix/2`函数将匹配，而第二个`suffix/2`声明将捕捉对该函数的任何其他调用。我喜欢的另一点是，arity 不同的函数确实是其他函数。这就是为什么我可以在匹配了`suffix/2`函数中的 am_pm 布尔值之后，轻松地创建一个`suffix/1`函数来返回实际的后缀(pm 或 am)。非常好！最后，您甚至可以在模式匹配时提取值！请看`suffix/2`的第一个声明，我从%Time{}结构中提取了小时。

```
def format_output(date_time, time_zone, opts \\ []) do
  time = time(date_time)
  city = city(time_zone)
  suffix = suffix(time, opts)

  case day_of_the_year(date_time) do
    1 ->
      time <> suffix <> " Happy New Year #{city}!"

    _ ->
      "time in #{city}: #{time}" <> suffix
  end
end

defp suffix(%Time{hour: hour}, [am_pm: am_pm]) when am_pm do
  suffix(hour)
end

defp suffix(_, _), do: ""
defp suffix(hour) when hour < 12, do: "am"
defp suffix(hour) when hour >= 12, do: "pm" 
```

那些特别注意的人可能已经发现输出仍然不正确...对吗？你和我在一起吗？当然，下午 13:00:00 不是一件事。这意味着当时间是下午时，我们将转换小时。让我们为此创建一个测试。

```
test "the time is updated when it is pm" do
  time = ~T[13:00:00.005]
  date = ~D[2019-01-02]
  {:ok, date_time} = NaiveDateTime.new(date, time)

  output = Mix.Tasks.Time.format_output(date_time, "Europe/Amsterdam", am_pm: true)

  assert output == "time in Amsterdam: 01:00:00 pm"
end 
```

在我们的实现中，我们唯一要做的事情就是当小时超过 12 点时进行转换。我们将为此添加一个助手函数，并在输出中使用它。我们需要添加助手函数的两个声明。1 代表 12 点的时候，因为它需要保持在 12 点。另一个函数将使用`rem/2`轻松转换为 12 小时格式。

他们在这里:

```
defp convert_pm(%Time{hour: hour} = time)
      when hour == 12 do
  time
end

defp convert_pm(%Time{hour: hour, minute: minute, second: second}) do
  {:ok, time} = Time.new(rem(hour, 12), minute, second)

  time
end 
```

为了使用它，我们将修改后缀函数来返回一个包含转换后的时间的二元元组。

```
defp suffix(%Time{hour: hour} = time, am_pm: am_pm) when am_pm do
  {convert_pm(time), suffix(hour)}
end 
```

然后我们最后的`format_output/3`函数看起来像这样:

```
def format_output(date_time, time_zone, opts \\ []) do
  time = time(date_time)
  {time, suffix} = suffix(time, opts)
  city = city(time_zone)

  case day_of_the_year(date_time) do
    1 ->
      to_string(time) <> suffix <> " Happy New Year #{city}!"

    _ ->
      "time in #{city}: #{to_string(time)} " <> suffix
  end
end 
```

好了，我现在就知道这么多了。下一篇文章我们将继续探索`Mix`的世界，并发现一些我们可以用 Mix 做，但可能不应该做的疯狂的事情...

查看当前时钟在 github 上的完整实现:[https://github.com/drumusician/clock](https://github.com/drumusician/clock)

下次见！