# 注入灵药的方案

> 原文：<https://dev.to/appsignal/pouring-protocols-in-elixir-5fo2>

在今天的炼丹术中，我们将搅拌成协议的药剂。Elixir 有几个机制允许我们编写表达性和直观的代码。

例如，模式匹配是一种处理多种场景的强大方法，而不必进入复杂的分支。它让我们的每一个功能都清晰简洁。

# 什么是协议？

在某种程度上，协议类似于模式匹配，但是它们允许我们基于我们正在处理的数据类型编写更有意义和特定于上下文的代码。

让我们以内容交付网站为例。这个网站有多种类型的内容:音频剪辑，视频，文本，以及任何你能想到的内容。

显然，每种内容类型都有不同的属性和元数据，因此用独立的结构来表示它们是有意义的:

将它转化为灵丹妙药，你会得到如下结构:

```
defmodule Content.Audio do
  defstruct [:title, :album, :artist, :duration, :bitrate, :file]
end

defmodule Content.Video do
  defstruct [:title, :cast, :release_date, :duration, :resolution, :file]
end

defmodule Content.Text do
  defstruct [:title, :author, :word_count, :chapter_count, :format, :file]
end 
```

每种类型都有几个不同的字段，其中大多数是该类型特有的。我们还有一个公共的`:file`字段，它指向保存实际数据的文件。

现在，让我们假设你想让你的内容尽可能的易于访问。例如，您可能希望允许听力受损的用户查看您的音频和视频的抄本。为此，您将使用令人敬畏的`AudioTranscriber`和`VideoTranscriber`模块，它们分别提供`transcribe_audio/1`和`transcribe_video/1`功能。

这些功能的实现使用了最先进的机器学习，并将委托给未来的博客帖子。让我们假设他们工作，并与它滚动。

两个转录器模块被分成单独的模块。除了用于转录内容的不同函数名之外，它们可能是完全不同的库。为了允许我们以透明的方式使用这两种内容，我们将实现一个名为`Content.Transcribe`的协议，它有一个统一的 API，可以处理这两种类型的内容。

# 实现协议

使用协议，我们可以很容易地定义转录对每种数据类型意味着什么。首先定义一个转录协议:

```
defprotocol Content.Transcribe do
  def transcribe(content)
end 
```

然后为我们的每种类型分别实现它:

```
defimpl Content.Transcribe, for: Content.Video do
  def transcribe(video), do: VideoTranscriber.transcribe_video(video.file)
end

defimpl Content.Transcribe, for: Content.Audio do
  def transcribe(audio), do: AudioTranscriber.transcribe_audio(audio.file)
end

defimpl Content.Transcribe, for: Content.Text do
  def transcribe(text), do: File.read(text.file)
end 
```

我们为所有 3 种内容类型分别定义了相同功能的实现。

您可能会注意到，对于文本内容，实现只是读取相应的文件，因为它已经是文本格式，而对于其他两个，我们在文件上调用相应的机器学习魔术函数。

然后，我们能够为我们实现的所有数据类型调用`transcribe/1`:

```
iex> %Content.Video{...} |> Content.Transcribe.transcribe()
{:ok, "We're no strangers to love\nYou know the rules and so do I..."}

iex> %Content.Audio{...} |> Content.Transcribe.transcribe()
{:ok, "Imagine there's no heaven\nIt's easy if you try..."}

iex> %Content.Text{...} |> Content.Transcribe.transcribe()
{:ok, "in a hole in the ground there lived a hobbit..."} 
```

# 回退实现

现在，假设我们在我们的平台上增加了一种新的媒体类型:游戏(我们在开玩笑！我们是一家非常雄心勃勃的假设性创业公司，不可否认，成功可能会进入我们的头脑。

当我们试图转录新添加的内容时会发生什么？

```
iex> %Content.Game{...} |> Content.Transcribe.transcribe()
** (Protocol.UndefinedError) protocol Content.Transcribe is not implemented for %Content.Game{...}. This protocol is implemented for: Content.Audio, Content.Text, Content.Video 
```

哎呦！我们遇到了一个错误。这很有道理。我们没有为此类型提供任何转录实现。

但是这样做真的没有意义，不是吗？游戏应该是互动的体验，可能没有办法让每个人都能玩到。

所以我们可以提供一个总是失败的实现:

```
defimpl Content.Transcribe, for: Content.Game do
  def transcribe(game), do: {:error, "not supported"}
end 
```

但这似乎不太具有可扩展性，不是吗？如果我们不断添加新的内容类型，我们最终将不得不为我们无法转录的每一种类型重复这些内容。

相反，我们可以简单地为我们没有指定的任何类型添加一个后备实现。这正是通过为`Any`类型提供一个实现，然后在我们的协议中声明我们希望在必要时使用它来实现的。

```
defimpl Content.Transcribe, for: Any do
  def transcribe(_), do: {:error, "not supported"}
end

defprotocol Content.Transcribe do
  @fallback_to_any true
  def transcribe(content)
end 
```

通常可以通过请求 Elixir 自动从其派生实现来使用`Any`的实现(你可以在官方的 [Elixir 入门指南](https://elixir-lang.org/getting-started/protocols.html#deriving)中了解更多)。

但是通过将`@fallback_to_any true`添加到我们的协议中，我们声明无论何时找不到特定的实现，都应该使用`Any`实现。这使得我们可以从容地应对任何不支持的数据类型:

```
iex> %Content.Game{...} |> Content.Transcribe.transcribe()
{:error, "not supported"}

iex> %{key: :value} |> Content.Transcribe.transcribe()
{:error, "not supported"} 
```

# 优雅地失败了

我们能比优雅的失败更好的结束吗？我们将离开你，因为我们已经试验了协议，今天我们还没有优雅地打破任何 alembic。

如果你喜欢尝试代码，请确保不要错过任何一集《炼丹术》！

*本文由客座作者[米盖尔·帕哈斯](http://naps62.github.io/)撰写。米盖尔是专业的总工程师[@子视觉](https://twitter.com/subvisual)，组织 [@rubyconfpt](https://twitter.com/rubyconfpt) 和 [@MirrorConf](https://twitter.com/mirrorconf) 。*