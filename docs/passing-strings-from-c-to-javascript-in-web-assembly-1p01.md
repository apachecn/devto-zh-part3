# 在 Web 汇编中将字符串从 C++传递到 JavaScript

> 原文：<https://dev.to/azure/passing-strings-from-c-to-javascript-in-web-assembly-1p01>

我正通过 Web Assembly(简称 WASM)在 Node.js 中运行我的[无线 LED 控制系统](https://github.com/nebrius/raver-lights)的[消息堆栈](https://github.com/nebrius/RaverLightsMessaging)的实验。我现在准备开始将堆栈集成到 Node.js 库中。

我决定连接的第一件事是一些日志功能。这意味着将字符串从 C++传递到 JavaScript。听起来很简单，对吧？我一直这样认为，直到我花了一天半的时间努力让它工作😅。

# 场景

你只能在 JavaScript 和 WASM 之间传递数字。这就是运行时的设计方式。那么如何传递更复杂的数据呢？

有一些关于跨语言代码交互的 [emscripten 文档讨论了如何做到这一点。如果您完全沉浸在 emscripten 世界中，那么您可以使用函数`ccall`和`cwrap`将字符串从一种语言整洁地传递到另一种语言。但是有一个问题:你必须运行一个完整的 C++应用程序来使用这些函数，而不仅仅是一个库。](https://emscripten.org/docs/porting/connecting_cpp_and_javascript/Interacting-with-code.html)

我试图破解输出，这样我就可以在不使它成为完整应用程序的情况下绑定这些函数，类似于我如何[破解输出](https://dev.to/azure/embedding-emscripten-in-a-nodejs-library-3hdm)来绑定 emscripten 的 WASM 引导代码。不过这次没有成功。emscripten 被设置为只有在 C++环境中运行了`int main() {}`之后，这些函数才可用。我没有一个主函数，因为这是一个库。甚至连添加一个空的 main 函数也因为某种原因没有成功。emscripten 抛出一个错误，指出在应用程序初始化和`main`运行之前`ccall`不可用。

所以回到制图板。我到处寻找其他类似的魔术，但是没有这样的运气。然后我就想到了！我把问题过于复杂化了。

WASM 在 JavaScript 中创建了一块内存*供其使用。这个内存块是通过调用`const memory = new WebAssembly.Memory({ initial: 256, maximum: 256 })`创建的。一个`WebAssembly.Memory`实例是一个`ArrayBuffer`的包装器，通过内存实例上的`buffer`属性向我们公开这个缓冲区。这是一种冗长的说法，WASM 内存只是一个我们可以访问的类型化数组！*

# 解

你可能读过最后一段，然后想“好吧，酷，但是这和字符串有什么关系？”在 C 语言中，字符串通常被定义为一个字符数组，例如`const char* myString`。`const char*`表示我们有一个字符数组，实际上是一个有符号的 8 位整数数组。这意味着我们可以查看字符串在上面提到的类型化数组缓冲区中的存储位置，并将连续的内存块解释为相对于字符串内存指针的字符数组。在高级别上，指针是内存块中代表一个值的索引。我们可以用一个名为`str`的指针来表示内存中的字符串，如下所示:

| 潜艇用热中子反应堆（submarine thermal reactor 的缩写） | str + 1 | str + 2 | str + 3 |
| --- | --- | --- | --- |
| seventy-two | One hundred and five | Thirty-three | Zero |

这个内存块构成了字符串`"Hi!"`。看到怎么会有值为`0`的“第四个字符”？这就是我们所说的“空终止符”，它表示内存中字符串的结束。显式处理字符串长度通常比遍历内存寻找`0`更容易。我们可以用`strlen`函数得到 C/C++中任意字符串的长度。有了指针和字符串长度，我们可以遍历内存，用下面的代码重建字符串:

```
const view = new Uint8Array(memory.buffer, pointer, length);
const string = '';
for (let i = 0; i < length; i++) {
  string += String.fromCharCode(view[i]);
}
console.log(string); 
```

现在我们准备编写代码将它们绑定在一起！首先，让我们编写下面的 C++来使用一个 JavaScript 函数:

```
extern "C" void jsPrintString(const char *s, uint16_t len);

void print() {
  const char* str = "Hello from C++!";
  jsPrintString(str, strlen(str));
} 
```

注意第一行`extern "C"`。这做了两件事:1)为我们将在 JavaScript 中实现的名为`jsPrintString`的函数定义了函数签名，2)告诉编译器使用 C 名称管理而不是 C++名称管理。C 和 C++编译器会更改函数签名的名称，以便可以轻松识别重载版本。这是 C 语言中的一个简单算法，因为它不允许太多的重载，只在名字前加了一个`_`。但是 C++要复杂得多，对于代码中一个叫做`RVLMessagingLoop`的函数，你可能会以`_Z16RVLMessagingLoopv`这样的名字结束。我们一会儿就会明白为什么这很重要。

**注意:**确保将`-s ERROR_ON_UNDEFINED_SYMBOLS=0`添加到您的`em++`构建命令中。这将防止编译器在 C++中找不到已定义函数的实现时出错。这是意料之中的，因为函数是在 JavaScript 中定义的，而不是在 C++中。注意:使用这个选项要*小心*，因为启用这个选项你可能会错过 C++代码中的实际问题。请务必将它没有找到的符号列表与您预计不会找到的符号列表进行比较。

然后我们有我们的`print`函数，它将调用 JavaScript 函数。我们用`const char*`定义了一个字符数组，并赋予它一个字符串值。`str`现在是指向内存中字符串的指针。指针也是数字！这意味着我们可以直接将指针从 C++传递到 JavaScript，而不必做任何特殊的事情。

现在是修改 JavaScript 代码的时候了。我们将把我们的字符串重构代码包装在一个名为`handlePrintString`的函数中。然后，我们通过修改传递给 WASM 实例化的`env`对象，将其注入 C++代码。我们用键`_jsPrintString`将这个函数分配给`env`对象(注意前导下划线)。`env`中的这个名字是 C/C++中函数的*变形后的*名字。这就是为什么我们要使用 C mangling 而不是 C++ mangling。最后，我们可以从 JavaScript 调用 C++中的`print`函数，该函数回调 JavaScript 来记录字符串。

```
function handlePrintString(ptr: number, len: number) {
  const view = new Uint8Array(memory.buffer, ptr, len);
  let string = '';
  for (let i = 0; i < len; i++) {
    string += String.fromCharCode(view[i]);
  }
  console.log(string);
}

const env = {
  ...
  _jsPrintString: handlePrintString,
  ...
};
WebAssembly.instantiate(bytes, { env }).then((result) => {
  result.instance.exports._print();
}); 
```

现在我们可以把字符串从 C++传递到 JavaScript 了！这听起来可能很小，但这标志着通过 Node.js 将该系统与 [Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-brhugh) 集成的一大步。