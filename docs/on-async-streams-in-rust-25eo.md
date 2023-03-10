# Rust 中的“异步流”

> 原文：<https://dev.to/cad97/on-async-streams-in-rust-25eo>

上下文: [For await 循环](https://internals.rust-lang.org/t/for-await-loops/9819?u=cad97)讨论由@withoutboats 在内部开始。

# 步骤一:什么是`Stream`？

我发现最清晰的解释来自于@withoutboats 在不同的内部线程上的再次[:](https://internals.rust-lang.org/t/pre-rfc-await-generators-directly/7202/10?u=cad97)

```
 | Evaluated immediately     | Evaluated asynchronously
------------------------------------------------------------------------
Return once | `fn() -> T`    (Function) | `async fn() -> T`    (Future)
Yield many  | `fn() yield T` (Iterator) | `async fn() yield T` (Stream) 
```

*   当您使用函数时，您评估一些例程来检索值。
*   当你使用迭代器时，你需要多次计算某个例程来获取下一个值。
*   当你使用 Future 时，你等待某个例程来获取一个值。
*   当你使用一个流时，你需要等待某个例程多次检索下一个值。

# 第二步:我们如何使用`Stream`？

为了确定这一点，让我们看看矩阵中的其他点。

## 功能

琐碎:`function()`。

## 迭代器

一个 for 循环:`for item in iterator()`。

这有效地使用 [`Iterator`](https://doc.rust-lang.org/stable/std/iter/trait.Iterator.html) 特征(简化)对代码进行去糖:

```
let __iter = iterator();
while let Some(item) = __iter.next() {
    // your block
} 
```

## 未来

等待 [`Future`](https://doc.rust-lang.org/stable/std/future/trait.Future.html) 的确切语法仍由 lang 团队决定。唯一比较确定的就是会涉及到保留关键字`await`。我将在本文档中使用`await!(future)`,因为它是当前不稳定的语法并且完全明确。

这是极其简化的，但是等待未来在概念上大致扩展为以下内容:

```
let __future = __stack_pin!(future);
loop {
    match __future.poll(__async_context_waker!()) {
        Poll::Ready(value) => break value,
        Poll::Pending => __async_yield_execution!(),
    }
} 
```

## 流

那么， [`Stream`](https://rust-lang-nursery.github.io/futures-api-docs/0.3.0-alpha.14/futures/prelude/trait.Stream.html) s 是如何在前使用*语法糖的呢？这对理解我们试图表达的语义很重要。*

我们有`Iterator`，但是我们有未来`Stream::poll_next`，而不是函数`Iterator::next`。

```
let __stream = __stack_pin!(stream);
while let Some(item) = await!(__future_from_poll!(__stream::poll_next)) {
    // your block
} 
```

这仍然隐藏了`await!(__future_from_poll!())`中的一些细节，所以让我们内联:

```
let __stream = __stack_pin!(stream);
while let Some(item) = loop {
    match __stream.poll_next(__async_context_waker!()) {
        Poll::Ready(value) => break value,
        Poll::Pending => __async_yield_execution!(),
    }
} {
    // your block
} 
```

哇，一切都变得有意义了！但是我们实际上如何使用它，因为这是许多样板文件来使用看起来有用的数据类型。

# 第三步:什么不是

对于流中的每个元素，这里发生的逻辑操作正在等待流中的下一个值:迭代器(`for item in iterator`)和 futures ( `await!(future)`)的组合。

“显而易见”的解决方案应该是`async for item in stream`。但这是错误的方向:`async`是推动者，但`await`是你如何投票未来完成。

另一个诱人的解决方案是让`for`只与`Stream`一起工作，做相当于`impl<I: Iterator> Stream for I`的事情。但是我们让(正在让)await 操作在 futures 上显式是有原因的，并且不应该在 streams 上失去它，如果没有其他原因的话，它会不一致。

我在很多地方看到过使用`for item in await!(stream)`的建议。希望上面的扩展说明了为什么在语义上不起作用。这将会是等待`Future<Item=Iterator>`。

# 第四步:可能性

我个人认为`await for item in stream`读起来完全符合语义。那些语义是我们`await`“某物”从流中取出每一项，或者换句话说，我们“(a)等待流中的每一项”。也不可能与`await!(for item in stream {})`混淆，因为`for`循环不(也不能)返回值。

@withoutboats 也建议我们可以把`await`做成一个图案，给我们`for await item in stream`。我不讨厌，但是这个有问题。

`await`作为一个模式(为了清楚起见，这里我仍然使用`await!(pattern)`),我希望与等待模式的 rhs 相同。或者在代码中，下面两条语句是等效的:

```
let value = await!(future);
let await!(value) = future; 
```

这实际上是之前在古老的`async` / `await` bikeshedding 线程中提出的。

问题是，这现在表明`Stream<Item=T>`是`Iterator<Item=Future<Item=T>>`。也许有些溪流符合这种类型。但是目前对`Stream`的定义更接近于`StreamingIterator<Item=Future<Item=T>>`，因为`poll_next`需要一个 pin mut ref 到流。这种借用是很多真实流所需要的。(我预计`Stream`会被滥用于`StreamingIterator`，如果它能这样工作，而我们还没有`StreamingIterator`。)

这可以非常简单地实现:让`for`与`StreamingIterator`一起工作，并为`Iterator`行为提供一个后备，让流由上面的`StreamingIterator`和`Future`的组合使用。

但是这仍然缺少许多真实未来想要的“`await?`”行为(因为 IO，大多数异步都是如此)。即使我们也将`?`添加到模式中，这仍然意味着我们有两种方法来完成相同的 await 操作:expr 位置(用于链接，这是 bikeshedding 线程的焦点)和 pat 位置。

# 第五步:结论

我认为`await for`是目前提出的语法中最好的，至少在等待未来包含关键字`await`的时候。

在任何情况下，这都是一个需要权衡的艰难选择，而且在不知道最终的`await`语法是什么的情况下，做出这个选择更加困难。但这是稳定`await`时至少需要考虑的一个问题。