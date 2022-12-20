# Ruby 中的纤程和枚举器:将块翻转过来

> 原文：<https://dev.to/appsignal/fibers-and-enumerators-in-ruby-turning-blocks-inside-out-2d1a>

Ruby 有多种执行迭代的方式——循环、块和枚举器。大多数 Ruby 程序员至少熟悉循环和块，但是`Enumerator`和`Fiber`经常被蒙在鼓里。在本期的 Ruby Magic 中，客座作者 Julik 向`Enumerable`和`Fiber`展示了流控制可枚举数和翻转块。

## 暂停块和链式迭代

我们已经在以前版本的 Ruby Magic 中讨论过[枚举器](https://blog.appsignal.com/2018/05/29/ruby-magic-enumerable-and-enumerator.html)，在那里我们描述了如何从你自己的`#each`方法中返回一个`Enumerator`以及它可以用来做什么。对于`Enumerator`和`Fiber`来说，一个更广泛的用例是它们可以在飞行途中“暂停阻塞”。不仅仅是给`#each`的块或者给`#each`的整个调用，而是任何块！

这是一个非常强大的构造，可用于实现方法的填充，这些方法通过使用块作为桥梁来连接希望顺序调用而不是使用块的调用方。例如，假设我们想要打开一个数据库句柄并读取我们已经检索到的每一项:

```
db.with_each_row_of_result(sql_stmt) do |row|
  yield row
end 
```

block API 非常棒，因为当 block 终止时，它可能会为我们执行各种清理工作。然而，一些消费者可能希望以这种方式使用数据库:

```
@cursor = cursor

# later:
row = @cursor.next_row
send_row_to_event_stream(row) 
```

实际上，这意味着我们希望“暂时”暂停块的执行，稍后在块内继续执行。因此，调用者接管流控制，而不是由被调用者控制(执行块的方法)。

## 链接迭代器

这种模式最常见的用途之一是将多个迭代器链接在一起。当我们这样做时，我们习惯于迭代的方法(如`#each`)返回一个枚举器对象，我们可以用它来“获取”块使用`yield`语句发送给我们的值:

```
range = 1..8
each_enum = range.each # => <Enumerator...> 
```

然后，枚举器可以被*链接*，这允许我们执行类似“除了索引之外的任何迭代”的操作。在这个例子中，我们在一个范围上调用`#map`来获得一个`Enumerable`对象。然后我们链接`#with_index`来迭代索引为
的范围

```
(1..3).map.with_index {|element_n, index| [element_n, index] }
#=> [[1, 0], [2, 1], [3, 2]] 
```

这非常有用，尤其是当您的系统使用事件时。Ruby 提供了一个内置的方法，用一个枚举器生成器来包装任何方法，这让我们可以准确地完成这个任务。想象一下，我们想要从我们的`with_each_row_of_result`中一行一行地“拉”出来，而不是让方法把它们交给我们。

```
@cursor = db.to_enum(:with_each_row_of_result, sql_stmt)
schedule_for_later do
  begin
    row = @cursor.next
    send_row_to_event_stream(row)
  rescue StopIteration # the block has ended and the cursor is empty, the cleanup has taken place
  end
end 
```

如果我们自己实现它，它可能会这样:

```
cursor = Enumerator.new do |yielder|
  db.with_each_row_of_result(sql_stmt) do |row|
    yielder.yield row
  end
end 
```

## 将积木翻转过来

Rails 允许我们将响应体指定为枚举器。它将调用我们指定为响应体的枚举器上的`next`,并期望返回值是一个字符串——它将被写出到 Rack 响应中。例如，我们可以将对范围的`#each`方法的调用作为 Rails 响应体:

```
class MyController < ApplicationController
  def index
    response.body = ('a'..'z').each
  end
end 
```

这就是我所说的把一个街区翻个底朝天。本质上，它是一个控制流助手，可以让我们在一个 block(或者一个 loop，在 Ruby 中也是一个 block)的中途“冻结时间”。

但是，枚举数有一个限制属性，这使得它们的用处稍微小一些。想象我们想做这样的事情:

```
File.open('output.tmp', 'wb') do |f|
  # Yield file for writing, continuously
  loop { yield(f) }
end 
```

让我们用一个枚举器把它包起来，并写入其中

```
writer_enum = File.to_enum(:open, 'output.tmp', 'wb')
file = en.next
file << data
file << more_data 
```

一切都很好。然而，有一个问题——我们如何告诉枚举器我们已经写完了，这样它就可以“完成”这个块，关闭文件并退出？这将执行许多重要的步骤—例如，资源清理(文件将被关闭)，以及确保所有缓冲的写操作都被刷新到磁盘。我们确实可以访问`File`对象，并且我们可以自己关闭它，但是我们希望枚举器为我们管理关闭；我们必须让枚举器通过这个块。

另一个障碍是，有时我们想要传递挂起的块中正在发生的事情的参数。假设我们有一个具有以下语义的块接受方法:

```
write_file_through_encryptor(file_name) do |writable|
  writable << "Some data"
  writable << "Some more data"
  writable << "Even more data"
end 
```

但是在我们的调用代码中，我们想这样使用它:

```
writable = write_file_through_encryptor(file_name)
writable << "Some data"
# ...later on
writable << "Some more data"
writable.finish 
```

理想情况下，我们将把我们的方法调用包装成某种结构，这将允许我们使用下面的技巧:

```
write_file_through_encryptor(file_name) do |writable|
  loop do
    yield_and_wait_for_next_call(writable)
    # Then we somehow break out of this loop to let the block complete
  end
end 
```

如果我们这样包装我们的作品会怎么样？

```
deferred_writable = write_file_through_encryptor(file_name)
deferred_writable.next("Some data")
deferred_writable.next("Some more data")
deferred_writable.next("Even more data")
deferred_writable.next(:terminate) 
```

在这种情况下，我们将使用`:terminate`作为一个神奇的值，告诉我们的方法它可以完成这个块并返回。**这个**就是`Enumerator`不会真正帮助我们的地方，因为我们不能传递任何参数给`Enumerator#next`。如果可以，我们就能做到:

```
deferred_writable = write_file_through_encryptor(file_name)
deferred_writable.next("Some data")
...
deferred_writable.next(:terminate) 
```

## 输入红宝石的纤维

这正是纤维所允许的。纤程允许你在每次再入时接受参数，所以我们可以这样实现我们的包装器:

```
deferred_writable = Fiber.new do |data_to_write_or_termination|
  write_file_through_encryptor(filename) do |f|
     # Here we enter the block context of the fiber, reentry will be to the start of this block
    loop do
      # When we call Fiber.yield our fiber will be suspended—we won't reach the
      # "data_to_write_or_termination = " assignment before our fiber gets resumed
      data_to_write_or_termination = Fiber.yield
    end
  end
end 
```

它是这样工作的:当你第一次在你的`deferred_writable`上调用`.resume`时，它进入纤程并一直到第一个`Fiber.yield`语句或最外层纤程块的末尾，无论哪个先出现。当你呼叫`Fiber.yield`的时候，它会把控制权还给你。还记得普查员吗？这个块将被*挂起*，下一次你调用`.resume`时，`resume`的参数将变成新的`data_to_write`。

```
deferred_writes = Fiber.new do |data_to_write|
  loop do
    $stderr.puts "Received #{data_to_write} to work with"
    data_to_write = Fiber.yield
  end
end
# => #<Fiber:0x007f9f531783e8>
deferred_writes.resume("Hello") #=> Received Hello to work with
deferred_writes.resume("Goodbye") #=> Received Goodbye to work with 
```

因此，在纤程中，代码流是在对`Fiber#resume`的第一次调用时*开始*，在对`Fiber.yield`的第一次调用时暂停，然后*在对`Fiber#resume`的后续调用中继续*，其中`Fiber.yield`的返回值是对`resume`的参数。代码从最后一次调用`Fiber.yield`的地方继续运行。

这是纤程的一个小怪癖，纤程的初始参数将作为块参数传递给你，而不是通过`Fiber.yield`的返回值。

记住这一点，我们知道通过向`resume`传递一个特殊的参数，我们可以在纤程中决定是否应该停止。让我们试试:

```
deferred_writes = Fiber.new do |data_to_write|
  loop do
    $stderr.puts "Received #{data_to_write} to work with"
    break if data_to_write == :terminate # Break out of the loop, or...
    write_to_output(data_to_write)       # ...write to the output
    data_to_write = Fiber.yield          # suspend ourselves and wait for the next `resume`
  end
  # We end up here if we break out of the loop above. There is no Fiber.yield
  # statement anywhere, so the Fiber will terminate and become "dead".
end

deferred_writes.resume("Hello") #=> Received Hello to work with
deferred_writes.resume("Goodbye") #=> Received Goodbye to work with
deferred_writes.resume(:terminate)
deferred_writes.resume("Some more data after close") # FiberError: dead fiber called 
```

在很多情况下，这些工具会非常有用。由于纤程包含可以手动恢复的挂起代码块，因此纤程可用于实现事件反应器和处理单个线程中的并发操作。它们是轻量级的，因此您可以通过将一个客户机分配给一个纤程，并根据需要在这些纤程对象之间进行切换，来实现使用纤程的服务器。

```
client_fiber = Fiber.new do |socket|
   loop do
     received_from_client = socket.read_nonblock(10)
     sent_to_client = socket.write_nonblock("OK")
     Fiber.yield # Return control back to the caller and wait for it to call 'resume' on us
   end
end

client_fibers << client_fiber

# and then in your main webserver loop
client_fibers.each do |client_fiber|
  client_fiber.resume # Receive data from the client if any, and send it an OK
end 
```

Ruby 有一个额外的名为`fiber`的标准库，它允许你显式地将控制权从一个纤程转移到另一个纤程，这对于这些用途来说是一个额外的便利。

## 控制数据发射率

当您希望能够控制 Ruby 块发出数据的速率时，纤程和枚举器还有另一个很好的用途。例如，在 [zip_tricks](https://github.com/WeTransfer/zip_tricks) 中，我们支持以下块使用作为使用库的主要方式:

```
ZipTricks::Streamer.open(output_io) do |z|
  z.write_deflated_file("big.csv") do |destination|
   columns.each do |col|
     destination << column
   end
  end
end 
```

因此，我们允许对创建 ZIP 存档的那部分代码进行“推送”控制，并且不可能控制它输出多少数据以及输出的频率。如果我们想以比如说 5 MB 的块来编写我们的 ZIP 文件——这将是 AWS S3 对象存储的一个限制——我们将不得不创建一个自定义的`output_io`对象，当该段需要被分割成一个 S3 多部分时，该对象将以某种方式“拒绝”接受`<<`方法调用。然而，我们可以反转控件，使其“拉动”。我们仍将使用同一个块来写入我们的大 CSV 文件，但是我们将根据它提供的输出来恢复和暂停它。因此，我们使以下使用成为可能:

```
output_enum = ZipTricks::Streamer.output_enum do |z|
  z.write_deflated_file("big.csv") do |destination|
   columns.each do |col|
     destination << column
   end
  end
end

# At this point nothing has been generated or written yet
enum = output_enum.each # Create an Enumerator
bin_str = enum.next # Let the block generate some binary data and then suspend it
output.write(bin_str) # Our block is suspended and waiting for the next invocation of `next` 
```

这允许我们控制 ZIP 文件生成器发出数据的速率。

因此，枚举器和纤程是一个**控制流机制**，用于将“推”块转换成接受方法调用的“拉”对象。

纤程和枚举器只有一个缺陷——如果您的块中有类似于`ensure`的东西，或者在块完成后需要做的事情，现在就看调用者是否调用您足够的次数了。在某种程度上，它类似于在 JavaScript 中使用承诺时的约束。

## 结论

这就结束了我们对 Ruby 中流控制的可枚举数的研究。在这一过程中，Julik 揭示了`Enumerable`和`Fiber`类之间的异同，并深入到调用者决定数据流的例子中。我们还学习了`Fiber`的额外魔法，允许在每次块重入时传递参数。快乐控流！

为了获得稳定的魔力，[订阅](https://appsignal.com/ruby-magic)Ruby Magic，我们会将我们的月刊直接发送到你的收件箱。

特邀作者[朱利克·塔哈诺夫](https://www.imdb.com/name/nm3127224/)是一位 VFX 的艺术家，他变得无赖而红宝石。白天和晚上，他在我们的中转站按喇叭，就像[拉链](https://github.com/WeTransfer/zip_tricks)和[图像](https://github.com/WeTransfer/image_vise)T7】