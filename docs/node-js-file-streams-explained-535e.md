# Node.js 文件流解释！

> 原文：<https://dev.to/areknawo/node-js-file-streams-explained-535e>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容。**

在今天的帖子中，我们继续[发现 node . js](https://areknawo.com/tag/nodejs/)(*v 10 . 15 . 3 LTS*)API！上次，我们讨论了用于读写文件的 [**文件系统(FS) API**](https://areknawo.com/node-js-file-system-api-beginner-friendly-guide/) ，但不是全部。我们还没有谈到相当多的东西，包括**流**，它提供了一种伟大的、替代性的读写数据的方式。流数据不是一次做所有的事情(即使是异步的)，而是一种更高效的方式——尤其是在处理大文件的时候。你的文件是逐块读取或写入的，而不是一次性全部读取或写入。他们有几个专用的 FS API 方法，但也有一整套自己的 API，叫做 [**流 API**](https://nodejs.org/dist/latest-v10.x/docs/api/stream.html) 。这就是我们将在这篇文章中探索的所有美好！

# 溪流

在深入研究与 FS 相关的文件流之前，我们首先应该了解 Node.js 流 API 本身。其核心是一个基于 [`EventEmitter`](https://nodejs.org/dist/latest-v10.x/docs/api/events.html#events_class_eventemitter) 类的接口，由 Node.js API 中的多个方法实现。这些例子包括 HTTP 请求和这里提到的文件系统操作。`EventEmitter`本身是一个非常简单的类，许多其他实体都使用或继承它。它负责监听和发出事件，方法有`.on()`和`.emit()`。我认为文档以清晰易读的方式提供了所有信息。

流可以是**可读的**、**可写的**或者两者兼有。大部分的*【流】*模块 API 作为一个整体，是针对创建流接口的实现，这不是本文的重点。也就是说，在这里，我们将只对可读和可写的流接口做一个很好的概述，考虑到“消费者使用”。

默认情况下，流只对字符串和缓冲区进行操作，这恰好是我们在上一篇文章中用来读写文件的相同数据形式。通过在创建流时设置`objectMode`属性，您可以让您的流使用其他类型的值。处于*“对象模式”*的流支持所有可能的值类型，除了用于特殊目的的`null`。然而，当使用 FS API 时，不应该真的需要这个技巧。

```
createReadableStreamSomehow({ objectMode: true }); 
```

Enter fullscreen mode Exit fullscreen mode

## 可读

可读流是可以从中读取数据的流。它们由`stream.Readable`类定义，在 **2 种不同的读取模式**下运行(不要误解为“对象模式”)。这些是*【流动】**【停顿】*。默认情况下，所有新创建的流都处于*“暂停模式”_ 下，因此，它们需要用户明确请求另一个流数据块。_“流动模式”*，另一方面，让数据自动“流动”，你只需要处理-消费或忽略-传入的数据。

### 缓冲

无论你以何种模式传输数据，它都必须首先被**缓冲**。为此，可读流在内部使用`.readableBuffer`属性，而可写流- `.writableBuffer`。这些缓冲区的大小限制由传递给流构造函数的配置的`highWaterMark`属性决定。它被认为是存储的最大字节数(默认为 16 KB)或最大对象数(如果处于“对象模式”，默认为 16)。

```
createReadableStreamSomehow({ highWaterMark: 8192 }); 
```

Enter fullscreen mode Exit fullscreen mode

不同种类的流处理缓冲的方式不同。在可读流的情况下，数据被不断地读取并放置在缓冲区中，直到它达到所提供的限制。然后，数据读取停止，直到缓冲区内的数据将被消耗，有效地释放它。

### 暂停模式

使用流式数据在很大程度上取决于您当前的阅读模式。当处于默认的“暂停模式”时，你必须手动请求下一个数据块。为此，您必须使用`.read()`方法。将返回内部缓冲区中的全部数据，除非您传递一个参数，指定要读取的数据的大小限制。

```
// ...
readable.read(); 
```

Enter fullscreen mode Exit fullscreen mode

在“对象模式”中，不管*大小*参数如何，总是返回单个对象。

### 切换

从“暂停模式”切换不需要太多工作。最简单的方法是**为*“数据”*事件添加一个处理程序**。其他方法包括调用`.resume()`方法，该方法恢复“数据”事件的发射，或者通过管道传输一个写流(稍后将详细介绍)。

```
// ...
readable.on("data", dataChunk => {
    // code
});
// or
readable.resume(); 
```

Enter fullscreen mode Exit fullscreen mode

如果出于某种原因，你想让**回到**的“暂停模式”，你可以用两种方法。您可以使用`.pause()`方法来停止发出“数据”事件，或者，如果您以前使用过。pipe()方法，使用`.unpipe()`来...取消管道化的写流。

```
// ...
readable.pause(); 
```

Enter fullscreen mode Exit fullscreen mode

有一个事件叫做*【可读】*，如果听了，会让你**陷入【暂停模式】**，从而让`.pause()``.resume()`之类的调用方法没用。当新的数据部分可以从缓冲区中读取时，在流结束之前，当读取的数据等于`null`时，就会发出该信号。移除事件处理程序后，一切恢复正常。

```
// ...
const handler = () => {
  readable.read();
  // handle reading manually
}
readable.on("readable", handler);
readable.off("readable", handler); 
```

Enter fullscreen mode Exit fullscreen mode

### 流动模式

“流动模式”在本质上肯定更复杂一些。在这里，`.read()`方法被自动调用，只剩下**使用“数据”事件中的**给定数据，在`.read()`调用后立即发出，并带有一个新的数据块。

```
// ...
readable.on("data", dataChunk => {
    // code
}); 
```

Enter fullscreen mode Exit fullscreen mode

此外，“流动模式”有一个内置的**保护**，如果没有合适的处理程序，它会阻止数据被自动读取。因此，只有当您添加“数据”事件处理程序时，数据才会开始流动。如前所述，这也使得从“暂停”模式切换到“流动”模式。尽管如此，你仍然需要小心谨慎！在没有“数据”事件处理程序的情况下调用`.resume()`方法，或者移除处理程序，都不会停止读取过程，并且会导致**数据丢失**！

### 事件

除了“可读”和“数据”事件，可读流还可以发出 3 个事件- *、【结束】、*、【关闭】、*、【错误】、*。当流结束并且**所有数据都已消耗完**时，发出“end”事件。** 

```
// ...
readable.on("end", () => {
    console.log("Stream ended");
}); 
```

Enter fullscreen mode Exit fullscreen mode

当底层的**源已经关闭**时，就会发出“关闭”事件。这样的例子包括用`fs.close()`方法关闭底层文件描述符，这在上一篇文章中已经讨论过了。

```
// ...
readable.on("close", () => {
    console.log("Stream ended");
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们有“错误”事件，坦率地说，每当某种类型的**错误发生**时，就会发出该事件。一个错误对象将被传递给回调函数。

```
// ...
readable.on("error", err => {
    console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 检查

为了保持对流的正确控制，Node.js 为您提供了一些额外的方法和属性。

您可以通过调用`.isPaused()`方法来检查流是否处于“暂停模式”。

```
// ...
readable.isPaused(); // false
readable.pause();
readable.isPaused(); // true 
```

Enter fullscreen mode Exit fullscreen mode

以我们目前的知识，第一个`.isPaused()`检查的输出可能会让你吃惊。如果我们还没有添加任何“数据”处理程序或调用`.resume()`，为什么可读流没有暂停？答案是，从内部来说，我们所说的操作模式要稍微复杂一些。我们所讨论的只是对读流状态的抽象，由内部`.readableFlowing`属性决定，你不应该乱动它。它可以有三个值- `null`、`true`或`false`。虽然`true`和`false`可以与我们的“暂停”和“流动”模式相比较，但`null`不能。因此，在流创建之后，内部状态为 null(稍后可以通过`.pause()`或“data”事件处理程序来更改)，所以不会暂停。这就是为什么第一次调用`.isPaused()`返回 false。

官方 Node.js 文档为您提供了另外 3 个元数据属性。`.readable`通知您是否可以安全地调用`.read()`(在 Node.js 代码中，它被记录为一个遗留特性)，`.readableHighWaterMark`向您提供您的**缓冲区大小限制**，并且`.readableLength`指示**当前缓冲区大小**。这两者都可以指示字节数或对象数，这取决于是否打开了“对象模式”。当然，Stream 实例有更多您可以访问的内部属性，但是，除非您正在创建自己的 Stream 实现，否则您不应该真的这样做，甚至不需要这样做。

```
// ...
readable.readable; // true
readable.readableHighWaterMark; // 16384 by default
readable.readableLength; // number of bytes currently in buffer 
```

Enter fullscreen mode Exit fullscreen mode

### 变化

除了标准工作流之外，与可读流的交互是有限的。不过这不是问题，因为流并不真的需要那么多东西。

`.destroy()`方法确实如其名所示——它**破坏流**,释放内部资源(缓冲数据)并发出“错误”和“关闭”事件。您可以选择传递一个 error 对象，稍后将在“error”事件处理程序中检索该对象。

```
// ...
readable.destroy(); 
```

Enter fullscreen mode Exit fullscreen mode

使用`.setEncoding()`方法，你可以**改变读取数据的编码**。默认情况下等于*【缓冲】*。在前一篇文章中，我们已经深入讨论了编码。

```
// ...
readable.setEncoding("utf8"); 
```

Enter fullscreen mode Exit fullscreen mode

要知道大多数流实现都允许传递一个可以提供有`encoding`属性的**配置对象**，从一开始就有效地设置了它。

在您不希望以某种不同的方式线性地使用所有流数据的场景中，`.unshift()`方法可能会有所帮助。它将检索到的数据块**放回内部缓冲区**。它可以在任何时候被调用，除了在“结束”事件之后。不过，你需要记住，当`.unshift()`完成时，你的数据将回到你的内部缓冲区，准备好被第一个即将到来的`.read()`调用再次读取。

```
// ...
readable.setEncoding("utf8");

readable.on("readable", () => {
  let data = readable.read();

  // Let's say our streamed data is a string - "Hello World!";
  while (data === "Hello World!") {
    // Infinite loop!
    readable.unshift(data);
    data = readable.read();
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 配管

管道的过程将我们带入了可写流的领域。`.pipe()`方法做的所有事情就是简单地用**管道**(传递或连接)可读的流到可写的流。这样，你可以轻松地将数据从一个文件转移到另一个文件。

```
const readable = createReadableStreamSomehow();
const writable = createWritableStreamSomehow();

readable.pipe(writable); 
```

Enter fullscreen mode Exit fullscreen mode

就像我之前提到的操作模式一样，`.pipe()`方法自动将可读流切换到“流动模式”。它还无缝地**管理数据流**，并最终返回传递的可写流。通过这种方式，您可以使用双向流(本文没有讨论)来创建可链接的连续流，就像 Node.js ZLIB (compression)实现的那样。

当来自可读流的“结束”事件发生时，`.pipe()`方法自动**关闭可写流**(不再有数据可写)。您可以通过以布尔值的形式传递带有 end 属性的可选配置对象来更改这种行为。

```
// ...
readable.pipe(writable, {end: false}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您想分离管道流，您可以很容易地调用`.unpipe()`方法来完成。如果没有传递可写流，它将分离所有管道流，否则只分离提供的流。如果操作模式是通过使用`.pipe()`方法设置的，它将回到先前的“暂停模式”。

## 可写

即使一个可写的流可能看起来服务于一个稍微复杂一点的任务**写数据**，有一个简单得多的 API。相对于事件，它更倾向于方法的**使用，但通常与我们所看到的可读流非常相似。也没有复杂的操作模式之类的概念。一般来说，如果你已经知道如何使用可读的流，学习可写的流应该不难。** 

```
const writable = createWritableStreamSomehow(); 
```

Enter fullscreen mode Exit fullscreen mode

### 缓冲

由于写作和阅读有很大的不同，所以**缓冲过程**也是不同的！在可写流中，每次调用`.write()`方法时，要写入的数据都会添加到缓冲区中。

```
// ...
let bufferNotFull = writable.write("Hello World!", "utf8", () => {
    // code
}); 
```

Enter fullscreen mode Exit fullscreen mode

`.write()`方法相当复杂，可以接受 1 到 3 个参数。第一个应该包含要写入的**数据——字符串或缓冲区。如果它是一个字符串，那么如果您不想使用给定的可写流的默认编码，您可以提供一个可选的第二个参数，指示传递的数据的编码。最后，您可以传递一个**回调函数**，以便在数据写入缓冲区后调用。**

`.write()`方法的结果将是一个布尔值，表明内部缓冲区中是否还有一些剩余的**空间**。如果写满了(返回值是`false`)，你应该**停止写你的数据**，等待*“drain”*事件，重新开始写。不遵循这种做法会导致高内存使用率、错误，从而导致崩溃。

```
// ...
writable.on("drain", () => {
    console.log("You can continue the writing process!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过`.pipe()`使用时，`.write()`和*“drain”*事件的处理是自动高效完成的。因此，对于要求更高的场景，建议尽可能将数据封装在可读的流形式中。

### 相似之处

就像我之前提到的，可写流和可读流有很多相似之处。现在我们知道有一个内部缓冲区，其大小可以通过 config 对象的`highWaterMark`属性来设置。

```
const writable = createWritableStreamSomehow({
    highWaterMark: true
}); 
```

Enter fullscreen mode Exit fullscreen mode

可写流对象配置也接受许多其他选项。其中一个是`encoding`。就像在可读流中一样，它设置在整个流中使用的默认编码。同样可以使用`.setDefaultEncoding()`方法进行设置。命名的不同(*“默认”*部分)来源于这样一个事实:它可以在你每次`.write()`呼叫中自由更改。

```
// ...
writable.setDefaultEncoding("utf8"); 
```

Enter fullscreen mode Exit fullscreen mode

除了“drain”事件，可写流还会发出一些。两个你已经知道的- *【错误】*和*【接近】*。它们分别在出错时发出，例如文件描述符关闭或`.destroy()`(也可用于可写流)方法调用时发出。

```
// ...
writable.on("error", err => {
    console.log(err);
});

writable.on("close", () => {
    console.log("No more operations will be performed!");
});

writable.destroy(); 
```

Enter fullscreen mode Exit fullscreen mode

可写流还实现了一些与可读流类似的属性，但命名略有不同。出于显而易见的原因，使用了*“可写”*短语，而不是*“可读”*。

这种改变可以在`.writable`属性中看到，它表示`.write()`方法是否可以安全调用，`.writableHighWaterMark`和`.writableLength`，提供关于内部**缓冲区大小限制**的元数据，它是**当前大小**。

```
// ...
writable.writable; // true
writable.writableHighWaterMark; // 16384 by default
writable.writableLength; // number of bytes currently in buffer 
```

Enter fullscreen mode Exit fullscreen mode

### 结局

流写数据**并不是一个无止境的过程**。要结束它，你需要调用`.end()`方法。它的行为就像`.write()`方法一样，只是允许您写入最后一块数据。可选的回调函数可以作为*“完成”*事件的处理程序，在流结束后直接调用。在这之后，**无法使用给定的流写入更多的数据**，尝试这样做将导致错误。

```
writable.end("The last chunk", "utf8", () => {
     console.log("Writable stream ended!");
     // Just like writable.on("finish", ...);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 配管

的。可写流一侧的 pipe()没有太大意义。这就是为什么这里唯一能回忆起管道过程的是*【管道】*和*【解管道】*事件。当在可读流端调用`.pipe()`和`.unpipe()`方法时，事件发生。对于这两个回调，都提供了管道可读流。

```
// ...
writable.on("pipe", readable => {
    console.log("Piped!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 瓶塞

当提供小块数据时，过多调用`.write()`方法可能会导致**性能下降**。对于这样的场景，可写流提供了`.cork()`和`.uncork()`方法。调用`.cork()`方法后，所有使用`.write()`写入的数据将**保存到内存**而不是缓冲区。通过这种方式，较小的数据块可以很容易地**批处理**以提高性能。您可以稍后使用`.uncork()`方法将数据从内存推送到缓冲区。要知道这些方法以类似 LIFO(后进先出)的顺序线性工作。需要完成与`.cork()`方法相同数量的`.uncork()`调用。

```
// ...
writable.cork();
writable.write("Hello");
writable.cork();
writable.write("World!");
process.nextTick(() => {
    stream.uncork();
    stream.uncork();
}); 
```

Enter fullscreen mode Exit fullscreen mode

在`nextTick`回调中进行`.uncork()`调用的技巧是另一个性能技巧，它通过`.write()`调用的内部批处理使**获得更好的性能**。我们将在以后的文章中学习更多关于`process`的知识，以及它的方法和属性。

# 文件系统流

咳...真是一路顺风，你不觉得吗？然而，我们还没有完成。还记得上面概述中的基本示例吗？我用过类似`createReadableStreamSomehow()`的东西。这是因为我不想到那时用 FS 相关的流来扰乱你的思维，而且来自*“stream”*模块的基本`stream.Readable`和`stream.Writable`类只是实现的参考，不能正确地处理事件和其他东西。是时候修复这个小错误了！

## 读小溪

FS API 通过`fs.ReadStream`类实现可读流接口。它还公开了实例化它的特殊方法- `fs.createReadStream()`。它将一个指向要读取的文件的**路径作为第一个参数，将一个可选的**配置对象**作为第二个参数。** 

```
const fs = require("fs");
const readStream = fs.createReadStream("file.js"); 
```

Enter fullscreen mode Exit fullscreen mode

配置对象接受多个属性。其中两个是我们已经知道的- `encoding`和`highWaterMark`(在这个实现中默认为 65536 ~ 64 KB)。您还可以传递指定 [**FS 标志**](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_file_system_flags) 和操作`mode`的`flags`字符串(参见上一篇文章)，尽管您很可能不会经常使用。同样适用于`fd`属性，它允许你忽略传递的路径参数，并使用从`fs.open()`调用中获得的提供的**文件描述符**。

```
// ...
const readStream = fs.createReadStream("file.js", {
    encoding: "utf8",
    highWaterMark: 128 * 1024
}); 
```

Enter fullscreen mode Exit fullscreen mode

更有趣的是`start`、`end`和`autoClose`属性。使用前两个选项，您可以指定您希望**开始和结束读取过程**的字节数。另一方面，`autoClose`是一个布尔值，指示底层文件描述符是否应该自动**关闭**(因此得名)，导致“关闭”事件的发出。

```
// ...
const readStream = fs.createReadStream("file.js", {
    encoding: "utf8",
    end: 10
});
/* With "utf8" encoding, the "end" number of bytes, 
specifies the number of characters to read */ 
```

Enter fullscreen mode Exit fullscreen mode

当然，在创建了一个流之后，工作流基本上保持不变，正如我们之前所讨论的。FS API 实现增加了一些自己的东西。这涉及到像*【关闭】**【打开】**【就绪】*——新的——与底层**文件描述符**有直接联系的事件。“open”在打开时触发，“close”在关闭时触发，而“ready”在“open”事件之后立即触发，此时**流准备好被使用**。此外，还有一些新的属性- `.path`和`.bytesRead`，指定读取文件的传递路径(可以是字符串、缓冲区或 URL 对象)，以及在给定时间点读取的**字节数**。

```
// ...
readStream.on("ready", () => {
    if(readStream.bytesRead === 0) { // meaningless check
        console.log(readStream.path);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是请记住，这些新的添加不应该影响与流交互的基本方式。它们的存在只是为了给你提供更多的数据。

## 写溪流

FS API 写流与可读流有许多相似之处**——就像它的参考实现一样。它们被创建为`fs.WriteStream`类的实例，使用`fs.createWriteStream()`方法。它接受与前面描述的几乎相同的配置，唯一的区别是缺少`end`属性，这在写入流中毫无意义。** 

```
// ...
const writeStream = fs.createWriteStream("file.js", {
    encoding: "utf8",
    start: 10 // start writing from 10th byte
}); 
```

Enter fullscreen mode Exit fullscreen mode

至于可写流实现本身，同样，情况非常相似。与文件描述符相关的*【打开】*、*、【关闭】*和*【就绪】*事件，`.path`属性保持不变，唯一不同的是，`.bytesWritten`属性指示已经写入的**字节数**。

```
// ...
writeStream.on("ready", () => {
    if(writeStream.bytesWritten === 0) { // meaningless check
        console.log(writeStream.path);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 你怎么看？

我希望这篇文章很好地达到了它的目的——用一种**好听的**、**易懂的**和**不正式的方式**来解释一个相当复杂的话题。流对于 Node.js 基础设施至关重要，因此，理解它是一个非常重要的概念。如果你喜欢这篇文章，我真的很高兴。请记得**在评论中留下你的观点**并在下面回复！如果你愿意，可以**分享**，这样其他人可以更快的学习到给定的题目。此外，您还可以在 Twitter [**上**关注我的****](https://facebook.com/areknawoblog) **[**，在我的脸书页面**](https://twitter.com/areknawo) 上[**关注我，并查看**](https://facebook.com/areknawoblog) **[**我的个人博客**](https://areknawo.com) 了解更多关于这个 [**Node.js 相关系列**](https://areknawo.com/tag/nodejs/) 和我博客中许多其他初学者友好的内容。再次感谢你阅读这篇文章，我希望你今天过得愉快！******