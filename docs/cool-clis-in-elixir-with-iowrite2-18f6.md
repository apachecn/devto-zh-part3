# IO.write/2 灵药中的清凉 CLIs

> 原文：<https://dev.to/chenge/cool-clis-in-elixir-with-iowrite2-18f6>

今天我读了这篇文章，它用 3 个简单有趣的例子告诉我们:时钟、计数器和进度条。

[![](img/d2f93db9728369dbe3247562e7998de8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TipLrZpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dennisbeatty.com/content/2019-01-09-cool-clis-in-elixir-with-io-write-2/progress.gif)T3】

```
defmodule Progress do
  @rounding_precision 2
  @progress_bar_size 50
  @complete_character "█"
  @incomplete_character "░"

  def bar(count, total) do
    percent = percent_complete(count, total)
    divisor = 100 / @progress_bar_size

    complete_count = round(percent / divisor)
    incomplete_count = @progress_bar_size - complete_count

    complete = String.duplicate(@complete_character, complete_count)
    incomplete = String.duplicate(@incomplete_character, incomplete_count)

    "#{complete}#{incomplete} (#{percent}%)"
  end

  defp percent_complete(count, total) do
    Float.round(100.0 * count / total, @rounding_precision)
  end
end

total = 50

Enum.each(1..total, fn task ->
  IO.write("\r#{Progress.bar(task, total)}")
  Process.sleep(50)
end)

IO.write("\n") 
```