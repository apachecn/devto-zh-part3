# 用 Rust 的特性扩展 Rust 的现有功能

> 原文：<https://dev.to/matsimitsu/extending-existing-functionality-in-rust-with-traits-in-rust-3622>

在 [AppSignal](https://appsignal.com) 我们使用 Protobuf 通过 Kafka 传递消息。我们选择这个是因为我们已经在栈中的其他地方使用了 Protobuf，它对我们的用例非常有用。

Protobuf 的一个好处是，它根据协议定义生成 Rust 代码，我们可以通过 traits 对其进行扩展，以添加额外的功能。

我们在处理管道中必须做的一件常见的事情是将两条消息合并成一条，例如合并两个(计数)度量。

在这种情况下，我们希望合并两条类似于
的`Counter`消息

```
message Counter {
  int64 count = 1;
} 
```

我们可以使用`protoc`生成该协议的 Rust 实现，并使用 trait 扩展该协议。

> 一个**特征**可以用来定义一个类型必须提供的功能。您还可以为可以被覆盖的特征实现默认方法。

在这种情况下，我们为我们的`CounterExt`特征实现了一个默认函数。

```
extern crate protobuf;

pub mod protocol;

use protocol::Counter;

pub trait CounterExt {
    fn merge(&mut self, to_merge: &Counter)
} 
```

在上面的代码中，我们使用了`protobuf` crate，并用`protoc`将生成的 Rust 代码定义为一个公共模块。我们还使用我们在协议中定义的`Counter`消息。然后我们为计数器定义一个新的特征，称为 CounterExt。

这段代码为 CounterExt 定义了一个新函数，称为`merge`，它接受另一个计数器进行合并。

接下来，我们需要为这个函数创建一个默认实现。

```
 impl CounterExt for Counter {
    fn merge(&mut self, to_merge: &Counter) {
        let our_count = self.get_count();
        self.set_count(our_count + to_merge.get_count());
    }
} 
```

在这个方法中，我们获取给定的计数器，并将其值添加到`self`。

现在我们已经用默认实现创建了这个特征，我们可以用它在 Protobuf 生成的代码上直接合并两个计数器。

这意味着我们可以直接对反序列化的 Protobuf 消息进行操作，而不必将它们转换为结构或创建新的消息来包含计算出的值。

```
use rdkafka::message::ProtobufMessage;

// Use the protocol Counter and the trait.
use protocol::protocol::Counter;
use protocol::CounterExt;

fn process_message(key: String, message: ProtobufMessage) {
    match cache.get_mut().entry(key) {
        // We have an entry, merge the counter
        Entry::Occupied(mut cache_entry) => {
            cache_entry.get_mut().merge(&message);
        },
        // No entry, insert it
        Entry::Vacant(cache_entry) => {
            cache_entry.insert(message);
        }
    }
} 
```

上面的代码为每个 Kafka 消息调用，并使用接收到的消息的合并值(如果存在)更新本地缓存。

如果消息不存在，它会将消息插入缓存中。

通过用默认特征扩展我们的 Protobuf 消息，我们在消息处理函数中省去了很多麻烦。

除了合并之外，我们还在 Protobuf 消息上实现了一些其他方法，用于处理分位数/百分位数/平均值的合并和计算。

喜欢这篇文章或者有什么意见？通过推特或 hello@matsimitsu.com[联系我](//mailto:hello@matsimitsu.com)