# 如何在 Ruby 中下载图像文件

> 原文：<https://dev.to/twilio/how-to-download-image-files-in-ruby-4f2a>

你曾经需要在你的 Ruby 应用程序中下载并保存一个图像吗？请继续阅读，找出方法。

## 朴素的老红宝石

下载一个没有任何依赖关系的文件最流行的方法是使用标准库 [`open-uri`](https://ruby-doc.org/stdlib-2.6.2/libdoc/open-uri/rdoc/OpenURI.html) 。

[`Kernel#open`](https://ruby-doc.org/core-2.6.2/Kernel.html#method-i-open) 是一种可以用来打开文件、流或进程进行读写的方法。例如，您可以使用以下代码打开一个文件并读取其内容:

```
open("./test.txt") do |file|
  puts file.read
end 
```

Enter fullscreen mode Exit fullscreen mode

`open-uri`扩展了`Kernel#open`,这样它可以像打开文件一样打开 URIs。我们可以用它来下载图像，然后保存为文件。

为此，我们首先需要`open-uri`，然后使用`open`方法来访问图像 URL。然后，我们可以打开一个文件，将图像的内容写入文件。打开 IRB 并尝试以下操作:

```
require "open-uri"

open("https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/test.original.jpg") do |image|
  File.open("./test.jpg", "wb") do |file|
    file.write(image.read)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在你打开 IRB 的目录中，你会找到你下载的图像。

这是成功的，但这是一个简单的例子。实际上，您可能希望处理潜在的错误，例如丢失图像的 404 错误。另外，使用`open-uri`还有很多其他潜在的问题。

## open-uri 的问题

问题是，像这样使用`open-uri`并不理想。首先，上面的代码不是很有效，它将整个图像加载到内存中，然后写入磁盘。原来`open-uri`还有一些其他的怪癖。Janko Marohni 在[神殿](https://github.com/shrinerb/shrine)工作时发现了一堆这样的问题。值得注意的是，`open-uri`:

*   [使您容易受到远程代码执行的攻击](https://sakurity.com/blog/2015/02/28/openuri.html)
*   有时返回一个`Tempfile`有时返回`StringIO`
*   不允许您限制重定向或文件大小

为了解决这一切，Janko 创造了 [`Down`宝石](https://github.com/janko/down)。它可以让您避免这些问题，安全有效地下载文件。

## 用`Down`宝石提高下载量

让我们使用`Down`下载相同的图像。从安装宝石开始:

```
gem install down 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`Down.download` :
将图像下载到 [`Tempfile`](https://docs.ruby-lang.org/en/2.6.0/Tempfile.html)

```
require "down"

tempfile = Down.download("https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/test.original.jpg") 
```

Enter fullscreen mode Exit fullscreen mode

如果你想把这个文件保存到文件系统中，`Down`有一个选项。将目录作为`:destination`选项传递会将文件保存到该目录。

```
require "down"

Down.download(
  "https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/test.original.jpg",
  destination: "./"
) 
```

Enter fullscreen mode Exit fullscreen mode

这将给下载的文件一个由`Tempfile`生成的随机名称。如果你想从 URL 中保留文件名，你需要做更多的工作。在这种情况下，我们可以将文件下载到一个`Tempfile`中，然后将它移动到我们驱动器上的一个永久位置。

为此，我们将使用 [`FileUtils#mv`](https://ruby-doc.org/stdlib-2.6.2/libdoc/fileutils/rdoc/FileUtils.html) ，它有两个参数，文件名和我们要将它移动到的目的地。为了获得文件的原始名称，我们可以使用`Down`返回的`Tempfile`对象的`original_filename`方法。

```
require "down"
require "fileutils"

tempfile = Down.download("https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/test.original.jpg")
FileUtils.mv(tempfile.path, "./#{tempfile.original_filename}") 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经安全有效地下载了文件，并将其永久存储在我们的硬盘上。

## 高级用法

为了控制下载体验，还需要一系列其他选项。默认情况下，它只允许 2 次重定向，但是你可以用`max_redirects`选项来控制。您也可以使用`max_size`选项来限制文件的大小。这将阻止攻击者通过下载大量图像来占用您的服务器。如果您想下载一个大小不超过 5MB、最多包含 5 次重定向的文件，您可以使用:

```
Down.download(
  "https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/test.original.jpg",
  max_redirects: 5,
  max_size: 5 * 1024 * 1024
) 
```

Enter fullscreen mode Exit fullscreen mode

文档中有[更多选项，包括如何将文件流下来，将 gem 的后端从`open-uri`和`Net::HTTP`改为](https://github.com/janko/down) [HTTP.rb](https://github.com/janko/down#httprb) 或 [Wget](https://github.com/janko/down#wget-experimental) 。

## 下一步

在这篇文章中，我们已经看到了如何使用`open-uri`和`Down`下载图片。`Down`要安全得多，因为它让我们避免了无限的重定向循环和远程代码注入，而且它对用户更加友好，因为它总是返回一个`Tempfile`，让我们可以轻松地限制文件大小。

你可以用它来从 Twilio MMS 或 WhatsApp messages 下载媒体，或者用 Rails 中的 T2 活动存储为用户上传图片提供更多选择。

你在 Ruby 中使用过其他下载图片的方法吗？请在下面的评论中或者在 Twitter 上告诉我，地址是 [@philnash](https://twitter.com/philnash) 。