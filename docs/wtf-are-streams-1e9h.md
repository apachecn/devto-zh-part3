# WTF 是流？

> 原文：<https://dev.to/manzanit0/wtf-are-streams-1e9h>

流一直是一个复杂的话题，但是在一些例子的帮助下，希望我们今天能够解决这个问题。

本质上，流是一系列随时间推移而可用的数据元素。就像真实的水流一样，数据流动/变得可用，而不是从一开始就拥有一切。这有许多好处，尽管最重要的两个好处是性能的巨大提高和数据并不总是立即可用。

就像刚才指出的，使用流的一个主要原因是因为有时，**数据不是立即可用的**。例如，如果您正在收听天气流媒体 API，当前温度是在当前时刻计算的，因此(1)只要您继续收听该服务，数据就是无限的，以及(2)每分钟都有新数据产生。这无法用有限的数据量来建模，所以我们决定用无限的数据流来建模。

由于流的大小是未定义的，它们可能是无限的，重要的是要记住，我们不能像我们习惯于处理列表或数组那样，将它们作为一个整体来操作。这样，应用于流的函数通常会返回另一个修改了数据的流。这些被称为*过滤器*，链接起来就形成了流水线。

提到的另一个好处是**性能**。当处理非常大量的数据时，这通常会对我们的计算机造成非常大的内存冲击。如果您试图读取一个包含关于猫的数据的 20Gb 文件，对其进行处理，然后将其发送给一个朋友，这将意味着除了我们的应用程序使用的内存量之外，我们还将加载额外的 20Gb 内存。大多数笔记本电脑都会死机！但这并不意味着这是不可行的。

如果我们不是将文件作为一个整体来读取，而是将其建模为一个数据流，我们可以一次读取一行，处理这些行并发送它们，也作为一个数据流。这将使我们的应用程序只使用额外的几个字节，而不是可怕的 20Gb。当考虑流时，我们总是不得不将它们视为我们的应用程序可以处理的可管理的数据块的良好序列——一旦它处理完一个数据块，它就可以读取和处理下一个数据块。

## 简化 I/O 超过一个字符串，在仙丹

我们第一个实用的流方法将是 Elixir 中的`StringIO`模块。一个`StringIO`并不是一个真正的流，只是一个字符串的包装器，它允许我们对字符串应用一些流上的标准 I/O 操作。对我们来说，这将是完美的，因为我们可以用它来熟悉操作:

1.  **Open:** 获得对资源的独占访问权。
2.  **读/写:**主要从/向流中读取或写入数据块。
3.  **关闭:**将资源返回给 OS(操作系统)。

首先，我们打开一个字符串 IO。从`open/1`函数的响应中，我们可以看到它给了我们一个对流的引用。

```
iex>  {:ok, pid} = StringIO.open("content")
{:ok, #PID<0.470.0>} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要检查流的内容，我们可以通过`contents/1`函数来完成，向它传递先前获得的引用。在 elixir 中，`contents/1`函数将总是返回一个带有输入缓冲区和输出缓冲区的元组，比如`{"in", "out"}`。在这种情况下，输出缓冲区将是空的，因为我们没有向其中写入任何内容。

```
iex(1)> StringIO.contents(pid)
{"content", ""} 
```

Enter fullscreen mode Exit fullscreen mode

由于`StringIO`是一个将字符串建模为流的包装器，我们可以使用标准函数从`IO`模块读取和写入流。在这种情况下，要写一些内容，我们可以使用`write/2`功能。请注意，我们现在在输入缓冲区和输出缓冲区中都有数据。

```
iex(2)> IO.write(pid, "written")
:ok

iex(3)> StringIO.contents(pid)
{"content", "written"} 
```

Enter fullscreen mode Exit fullscreen mode

大多数语言中的大多数流模块也给了我们一种方法来*刷新*内容，这意味着它强制写出流中的任何字节。这适用于输出缓冲器。

```
iex(4)> StringIO.flush(pid)
"written"

iex(5)> StringIO.contents(pid)
{"content", ""} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果我们想从输入缓冲区中读取数据，我们可以使用`read/2`函数，从而清空数据流:

```
iex(6)> IO.read(pid, :all)
"content"

iex(7)> StringIO.contents(pid)
{"", ""} 
```

Enter fullscreen mode Exit fullscreen mode

注意在这个特定的例子中，Elixir 是如何将`StringIO`建模为一个元组的，这个元组有一个输入缓冲区和一个输出缓冲区，一个是我们可以写入的缓冲区，另一个是我们可以读取的缓冲区。

## 对一个文件的 I/O，在 C#/中。网

转到一个更实际的例子，我们将看看如何处理流中的文件。当处理文件时，打开和关闭流开始变得更加重要，但是我们将在后面讨论。

英寸 NET land，为了创建一个文件，我们可以利用`File.Create`函数。这将为我们提供一个`FileStream`来模拟我们的文件，所以我们可以写入它。一旦我们打开流并向其写入，我们将不得不关闭它，以便*保存*这些更改并释放操作系统给我们的资源。此外，为了再次读取内容，我们将使用`File.OpenRead`重新打开另一个流，并逐字节读取。片段如下，MSDN 的功劳:

```
using System;
using System.IO;
using System.Text;

namespace StreamTime
{
    public class FileTheStream
    {

        public static void Main()
        {
            const string path = @"/Users/jgarcia/Desktop/example.txt";

            //Create the file.
            using (FileStream fs = File.Create(path))
            {
                AddText(fs, "This is some text");
                AddText(fs, "This is some more text,");
                AddText(fs, "\r\nand this is on a new line");
                AddText(fs, "\r\n\r\nThe following is a subset of characters:\r\n");

                for (var i = 1; i < 120; i++)
                {
                    AddText(fs, Convert.ToChar(i).ToString());
                }
            }

            //Open the stream and read it back.
            using (FileStream fs = File.OpenRead(path))
            {
                var b = new byte[1024];
                var temp = new UnicodeEncoding();
                while (fs.Read(b,0,b.Length) > 0)
                {
                    Console.WriteLine(temp.GetString(b));
                }
            }
        }

        private static void AddText(Stream fs, string value)
        {
            var info = new UnicodeEncoding().GetBytes(value);
            fs.Write(info, 0, info.Length);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，就代码而言，我们只是打开一个流，读、写和关闭它。嗯，你可能想知道关闭发生在哪里。考虑到在 C#中，每当我们对资源使用`using`关键字时，一旦它使用完了，它就会关闭资源——就像 Java 的*对资源*的尝试一样。但是让我们进一步讨论流的打开和关闭。

### 为什么流一定要打开或者关闭？

我们提到，每次我们打开一个流时，操作系统都需要投入资源，但我们从未讨论过是哪一个，或者如何投入。大多数时候，这取决于我们打开的流的性质——打开一个套接字和打开一个文件是不一样的，等等。目前，我们可以专注于文件。

每当打开一个新的文件流时，操作系统都会向应用程序提供一个文件描述符，通常称为文件句柄。文件描述符基本上是一个在计算机操作系统中唯一标识打开文件的数字——它描述了一个数据源，以及如何访问它。文件描述符指向内核的全局文件表，其中包含诸如流的偏移量和访问限制等信息。

可以想象，文件描述符不像内存——如果你不把它们返回给操作系统，情况会变得很糟糕。在长时间运行的应用程序中，应用程序崩溃只是时间问题。这通常被称为文件句柄泄漏。在 Windows 电脑中，当你试图删除一个文件时，通常会产生一个后果，那就是它显示正在被另一个程序使用。资源没有被正确释放。

幸运的是，现在大多数语言为我们提供了允许我们适当释放这些资源的结构。如前所述，我们的工具箱中有类似 C#中的`using`或 Java 中的`try`的语句和资源。不过在 Elixir 中，你必须用`IO.close/1`功能关闭它。

## 通过套接字的 I/O，在 Java 中

使用流的另一个场景是当应用程序打开一个套接字时。为了让两个进程进行通信，它们都需要打开一个套接字，然后通过套接字发送消息——套接字通过流发送和接收消息。接下来展示的是一个片段，其中包含一个用 Java 开发的非常简单的 echo 服务器。请注意套接字如何包含输入流和输出流，以及我们如何读取通过输入流发送的数据，然后将其写入输出流。

```
package com.manzanit0;

import java.io.*;
import java.net.ServerSocket;

public class EchoServer {
    public static void main(String[] args) {
        int portNumber = 4098;

        try (
                var serverSocket = new ServerSocket(portNumber);
                var clientSocket = serverSocket.accept();
                var outputStream = clientSocket.getOutputStream();
                var inputStream = clientSocket.getInputStream()
        ) {
            while(true) {
                var bytes = readAllBytes(inputStream);
                outputStream.write(bytes);
            }

        } catch (IOException e) {
            System.out.println(e.getMessage());
        }
    }

    private static byte[] readAllBytes(InputStream stream) throws IOException {
        StringBuilder data = new StringBuilder();

        // available only returns a value after reading at least 1 character -> do/while.
        do {
            data.append((char) stream.read());
        } while (stream.available() > 0);

        return data.toString().getBytes();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我想提到的另一件事是，由于流处理未定义的数据量，可能是无限的，如果您尝试执行程序，您会注意到它不会停止运行，直到您向它输入一个 abort 命令(Ctrl/Cmd + C)。我们可以继续输入数据，只要我们不这样做，流将继续喂养它。在这个特殊的例子中，我精心设计了一个特定的`bytes[] readAllBytes(InputStream stream)`函数，它只读取可用的字节并返回它们，但是`InputStream`类为我们提供了一个`readAllBytes()`方法，它阻塞直到流被关闭，然后返回所有接收到的字节。

您可能想知道，如果我想再次读取我的流的数据呢？可能吗？。确实有可能，但是为了理解如何实现，我们必须介绍最后一个概念:**寻找**。

## 寻找一个流——了解阅读的副作用

如果您再次尝试读取一个流，您可能会发现自己无法读取以前的数据，而只能读取新的数据。有些流不支持查找，但假设它们支持，背后的原因是流有一个指向最后读取的字节的光标。每当我们读取一个新字节时，光标就前进到新的位置。为了读取已经处理过的字节，我们需要将光标一直倒回起点。这叫求。

在 Java 中，这样做的方法是使用 [`InputStream`](https://docs.oracle.com/javase/6/docs/api/java/io/InputStream.html) 类的`mark(int)`和`reset()`方法，在 C#示例中，我们简单地设置 [`file.InputStream.Position = 0`](https://stackoverflow.com/questions/4266448/reading-stream-twice) 。这些都是读流的副作用。如果一个流不支持查找，另一个解决方案是将我们读取的字节复制到另一个数组并维护一个副本。尽管如此，要考虑到有时使用流的目的之一是减少内存消耗，我们将所有读取的数据复制到一个内存数组中，然后我们将完全取消这一点。

## 包装完毕

我们已经讨论了很多内容，但如果我们要做一个非常快速的 TLDR 并总结一些要点，我只会说:

1.  流管理不确定的数据量，有时是无限的。
2.  流可以极大地提升应用程序的性能，因为它们不必在处理数据之前将所有数据都加载到内存中。
3.  正如流必须打开一样，它们也必须关闭，否则操作系统的资源不会被释放，这可能是一个非常昂贵的成本，无论是套接字还是文件句柄。
4.  一些语言提供了处理资源处置的结构，比如 C#中的`using`或者 Java 中的`try`。
5.  流有一个指向最后一个读取字节的光标。在某些情况下，我们可以倒回光标，以便再次读取相同的数据。这叫求。

接下来，如果你想更深入地研究套接字，作为一个概念，请随意查看我关于它们的另一篇文章: [URL](https://manzanit0.github.io/networks/2018/11/09/sockets-explained.html) 。

*最初发布于:[https://manzanit0.github.io/](https://manzanit0.github.io/)T3】*