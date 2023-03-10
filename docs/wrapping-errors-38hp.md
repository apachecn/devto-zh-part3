# 包装错误

> 原文：<https://dev.to/alexsasharegan/wrapping-errors-38hp>

当在 Node.js 服务器中执行 I/O 时，有许多错误处理需要处理。我总是寻找尽可能多的错误背景，因为它大大减少了我发现、理解和修复错误的时间。

我们不会在这里讨论错误传播策略*(这将是即将到来的材料
的一部分)*，但我想分享一个快速的想法，如何
在错误通过堆栈传播时用上下文修饰错误。

## 原生错误

JavaScript 附带了一些内置的错误类。它们包括:

*   `Error`:异常的基类。
*   `EvalError`:关于全局`eval()`功能的错误。
*   `InternalError`:JavaScript 引擎内部发生的错误。
*   `RangeError`:值不在允许值的集合或范围内时的错误。
*   `ReferenceError`:引用不存在的变量时出错。
*   `SyntaxError`:试图解释语法上无效的代码时出错。
*   `TypeError`:值不是预期类型时的错误。
*   `URIError`:错误使用全局 URI 处理函数时的错误。

虽然我们有时会发现特定内置的用法，如`RangeError`、
，但大多数用户定义的异常都使用了基类`Error`。它的属性
包括一个`name`、`message`和`stack`。在 Node.js 中，错误实例
还包含一个`code`，用于识别各种错误类型，包括
潜在的系统错误。

创建我们自己的错误*(并抛出)*看起来像这样:

```
function parseIntStrict(numLike) {
  let n = parseInt(numLike, 10);
  if (!Number.isInteger(n)) {
    throw new Error(`unable to parse input as integer: ${numLike}`);
  }
  return n;
} 
```

这里，当`parseInt`返回`NaN`值时，我们创建并抛出一个错误。错误中的
消息是我们在没有更多
上下文的情况下所能做到的最具体的消息。这个函数可能被调用来转换用户输入，以更新购物车中的
产品数量，解析 csv 文件中的字段，等等。-我们
无法知道它会被如何使用。

### 在上下文中

假设我们正在编写一个 api，它获取一个 csv 文件并从其行字段中解析库存
数量。当我们解析文件中产品的库存数量时，我们将使用我们的`parseIntStrict`函数
。我们可能会这样看待我们的 api
调用栈:

*   `[4]`解析数量字段
*   `[3]`解析 csv 行
*   `[2]`逐行读取文件
*   `[1]`将请求映射到磁盘上的 csv 文件
*   `[0]`接收网络请求

在我们使用`parseIntStrict`功能之前，这里有许多步骤。
理想情况下，如果对我们函数的调用失败，我们希望知道我们的请求 id，
磁盘上 csv 文件的名称，行索引，以及导致
失败的字段。如果我们有所有的信息，我们可以直接解决任何
损坏的功能，而不是浪费宝贵的时间来诊断问题的根源
。

## 换行错误

为了实现给错误添加上下文的目标，我们需要一种方法来
修饰错误，然后将它们放回堆栈中。我们可以这样做的一个方法
是添加一个表示当前上下文的消息。我们不希望我们的
消息以任何方式破坏原始消息。这阻止了
试图操纵原始的错误消息，但是由于我们不能
总是保证错误生产者使用`Error`类来生成异常，
这首先不是一个选项。

让我们扩展`Error`类，这样我们就可以添加一个对未受影响的、
原始错误值的引用。这将让我们添加我们的上下文消息，并保留对原始错误值的
引用，而不管它是否是`Error`实例
。

```
class WrappedError extends Error {
  constructor(message /* context */, previous /* original error value */) {
    // First we have to call the Error constructor with its expected arguments.
    super(message);
    // We update the error's name to distinguish it from the base Error.
    this.name = this.constructor.name;
    // We add our reference to the original error value (if provided).
    this.previous = previous;
  }
} 
```

我们对基础类没有太大的改变。我们将免费获得默认的
`Error`行为，因为我们继承了*(生成堆栈跟踪，
字符串化，等等。)*，但是现在我们的错误类型可以引用一个以前的错误。
如果我们使用一个`WrappedError`来生成我们的第一个异常，先前的错误
将会是`undefined`。如果我们创建一个传入一个`Error`或一个文字
值的实例，`previous`将维护一个对它的引用。最有趣的是，如果我们
创建一个实例传入另一个`WrappedError`实例，我们就把一系列
错误连接在一起。

当我们开始在整个应用程序代码中使用我们的`WrappedError`时，我们的
本质上是将我们的错误值转换成链表。列表头应该是
我们当前错误在堆栈中的位置，或者它的**上下文**。我们可以通过检查是否存在以前的错误来遍历列表，如果存在，则检查它是否是 T1 的实例，这保证了我们可以检查一个 T2 字段来继续遍历。

让我们看看如何执行遍历来获得根错误
值——是`WrappedError`、`Error`还是其他。我们将使用一个计算的
属性——一个
[对象获取方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)—
允许我们像计算属性一样计算和访问`root`值。

```
class WrappedError extends Error {
  // ...

  get root() {
    // Base case, no child === this instance is root
    if (this.previous == null) {
      return this;
    }
    // When the child is another node, compute recursively
    if (this.previous instanceof WrappedError) {
      return this.previous.root;
    }
    // This instance wraps the original error
    return this.previous;
  }
}

let error1 = new Error("the first error");
let error2 = new WrappedError("the second error wraps #1", error1);
let error3 = new WrappedError("the third error wraps #2", error2);

console.assert(
  error2.root === error1,
  "the root of error2 should be strictly equal to error1"
);
console.assert(
  error3.root === error1,
  "the root of error3 should be strictly equal to error1"
);
// Passes if no error appears in the console 
```

你可以在
[这个 repl 这里](https://repl.it/@alexsasharegan/Wrapping-Errors-1)测试这个例子，但是
可以说严格的等式检查都是真的。这里我们可以做很多事情
，但是简单地用`console.error`进行日志记录会在 Node.js
*(来自同一个 repl)* :
中产生这个输出

```
{ WrappedError: the third error wraps #2
    at evalmachine.<anonymous>:27:14
    at Script.runInContext (vm.js:74:29)
    at Object.runInContext (vm.js:182:6)
    at evaluate (/run_dir/repl.js:133:14)
    at ReadStream.<anonymous> (/run_dir/repl.js:116:5)
    at ReadStream.emit (events.js:180:13)
    at addChunk (_stream_readable.js:274:12)
    at readableAddChunk (_stream_readable.js:261:11)
    at ReadStream.Readable.push (_stream_readable.js:218:10)
    at fs.read (fs.js:2124:12)
  name: 'WrappedError',
  previous:
   { WrappedError: the second error wraps #1
    at evalmachine.<anonymous>:26:14
    at Script.runInContext (vm.js:74:29)
    at Object.runInContext (vm.js:182:6)
    at evaluate (/run_dir/repl.js:133:14)
    at ReadStream.<anonymous> (/run_dir/repl.js:116:5)
    at ReadStream.emit (events.js:180:13)
    at addChunk (_stream_readable.js:274:12)
    at readableAddChunk (_stream_readable.js:261:11)
    at ReadStream.Readable.push (_stream_readable.js:218:10)
    at fs.read (fs.js:2124:12)
     name: 'WrappedError',
     previous: Error: the first error
    at evalmachine.<anonymous>:25:14
    at Script.runInContext (vm.js:74:29)
    at Object.runInContext (vm.js:182:6)
    at evaluate (/run_dir/repl.js:133:14)
    at ReadStream.<anonymous> (/run_dir/repl.js:116:5)
    at ReadStream.emit (events.js:180:13)
    at addChunk (_stream_readable.js:274:12)
    at readableAddChunk (_stream_readable.js:261:11)
    at ReadStream.Readable.push (_stream_readable.js:218:10)
    at fs.read (fs.js:2124:12) } } 
```

不错吧？

## 开分机

我们能够用它创建的上下文引用提供了很多扩展的机会。根据您的应用需求，您
可能会考虑:

*   添加一个`spans` getter 来计算所有字符串化错误的数组
*   使用 [`Object.defineProperty`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 在第一次访问后通过覆盖它们来记忆 getters
*   通过实现 [`WrappedError.prototype[@@iterator]()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator) 使列表可重复
*   为您的自定义数据类型 *(如 WrappedError、Error 和 any)* 创建类似`forEach`或`map`的迭代器方法，实现[模式匹配](https://blog.parametricstudios.com/posts/pattern-matching-custom-data-types/)

如果你
好奇的话，我已经在
[repl 链接](https://repl.it/@alexsasharegan/Wrapping-Errors-1)中实现了`spans`属性。如果你为这个想法想到了新的应用或功能，你可以
在 Twitter 上找到我，地址是 [@alexsasharegan](https://twitter.com/AlexSashaRegan) 。