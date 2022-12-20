# 从 Go 呼叫 Swift

> 原文：<https://dev.to/gerwert/calling-swift-from-go-5dbk>

Swift 和 Go 都是现代编译语言。他们有自己典型的用例，[优点和缺点](https://medium.com/@gerwert/notes-on-building-and-distributing-a-small-cross-platform-cli-tool-3eddc07b8449)。在一个项目中结合两种语言的优势不是很好吗？

一个可能的用例是当你想在一个现有的 Golang 代码库中使用 Cocoa API(MAC OS 自带的),比如`NSUserNotificationCenter`。这可以通过 Objective-C 作为中间语言来完成。但是我更喜欢 Swift 而不是 Objective-C。

这种“两全其美的方法”当然有一些注意事项，但是我们稍后会谈到。首先是有趣的部分。

## 以图书馆为桥梁

Swift 和 Go 之间没有直接的互操作性。但是我们能做的，是使用一种通用的方式来链接用任意语言编写的二进制文件:共享库。或者在这种情况下更具体地说:动态库。

先决条件是两种语言支持相同的[调用约定](https://en.wikipedia.org/wiki/Calling_convention)。Golang 和 Swift 都支持创建和链接使用`cdecl`调用约定的库，该调用约定源自 C 编程语言。如果我们构建一个 Swift 库，用 cdecl 调用约定导出函数，我们应该能够从 Go 中调用这些函数。

我们必须确保在 Swift 中按照 cdecl 惯例显式导出函数。从 Go 中调用“普通”Swift 库中的 Swift 函数是不可能的，因为 Swift 和 Golang 有它们自己的(和不同的)调用约定。

但是，即使两种语言使用相同的调用约定，我们也需要某种方法将参数和返回类型从一种语言转换到另一种语言。比如 Golang 里的一个`string`和 Swift 里的一个`String`完全不一样。因此，如果没有某种中间层，我们就不能简单地在 Swift 和 Golang 之间传递字符串。

在我们的例子中，该层是一个动态 C 库。因此，我们将使用 C 作为通用语言，这在桥接编程语言时很常见。

现在，让我们看看从 Swift 代码创建一个可以从 Go 调用的 C 库所需的步骤。

## 创建 Swift 库

让我们创建一个非常简单的 Swift 库，它导出一个打印字符串的函数。第一步是使用 Swift 软件包管理器初始化我们的项目:

```
$ swift package init --type library 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以创建一个包含我们函数的 Swift 文件:`./Sources/go-swift/go_swift.swift`。

在这个文件中，我们声明了一个函数:`sayHello`。这个函数
接受一个指向 C 字符串的指针作为唯一的参数:

```
@_cdecl("sayHello") // export to C as `sayHello`
public func sayHello(namePtr: UnsafePointer<CChar>?) {    
    // Creates a new string by copying the null-terminated UTF-8 data (C String) 
    // referenced by the given pointer.
    let name = String(cString: namePtr!)
    print("Welcome, \(name)!")
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使这个 Swift 函数可以从 C 中访问，我们必须给它添加一个`@ _cdecl`属性。
`@_cdecl`是一个[未记录且不受支持的属性](https://forums.swift.org/t/best-way-to-call-a-swift-function-from-c/9829)(因此在未来的 Swift 版本中可能不再可用)，它在结果库中为 Swift 函数提供了一个“C 名称”。它告诉编译器不要破坏函数名(默认情况下是这样的)，确保我们的函数名像用 c 编写的一样导出。

还要注意，我们不能使用内置的 Swift `String`作为我们参数的类型，因为那不是一个 C 兼容的类型。因此，我们处理的是指向 C 风格空终止字符串的原始指针:`UnsafePointer<CChar>?`。

### 从围棋中调用库

在通过`$ swift build`构建了 swift 库之后，我们可以尝试调用那个库中的`sayHello`函数。

要从 Go 调用我们的函数，我们需要 [cgo](https://golang.org/cmd/cgo/) 。Cgo 充当了 C 和 go 之间的桥梁。

但是在我们使用 cgo 将 Swift 库链接到我们的 Go 程序之前，我们需要创建一个 C 头文件:`./include/go_swift.h`。否则我们的 Go 程序是不知道`sayHello`的 C 函数签名的。

因为我们只有一个函数，我们的头文件可以由一行代码组成:

```
void sayHello(char* name); 
```

Enter fullscreen mode Exit fullscreen mode

使用`$ swiftc -emit-objc-header <swift file>`,您可以自动生成一个 Objective-C 标题。但是据我所知，普通 C 头文件没有自动生成。所以我们必须手动创建 C
头文件。

对于简单的函数，这相对简单。对于更复杂的函数，你可能需要(大量的)反复试验。例如，我没有深入到测试是否有可能将一个指向函数的指针作为参数传递。

定义了表头之后，就该写一个围棋小程序了:

```
package main

/*
#cgo CFLAGS: -I./include
#cgo LDFLAGS: -L.build/debug/ -lgo_swift
#include <stdlib.h>
#include "go_swift.h"
*/
import "C"
import "unsafe"

func main() {
    // Copy Go string to C string
    cstr := C.CString("Bob")

    // Call Swift function `sayHello`
    C.sayHello(cstr)

    // The C string is allocated in the C heap using malloc.
    // Therefore memory must be freed when we're done
    C.free(unsafe.Pointer(cstr))
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 Go 文件中，我们导入 C 包并声明一个前导。来自 cgo 文档:

> 要使用 cgo，请编写导入伪包“C”的普通 go 代码。Go 代码可以引用 C.size_t 等类型、C.stdout 等变量或 C.putchar 等函数。
> 
> 如果“C”的导入后面紧跟着一个注释，那么这个注释就叫做序言，在编译包的 C 部分时，它就被用作头文件。例如:

在序言中，我们需要设置一些链接器标志:

*   `cgo CFLAGS: -I./include`:设置头文件的路径
*   `cgo LDFLAGS: -L.build/debug/ -lgo_swift`设置查找库文件的位置，以及链接到哪些库。`go_swift`图书馆这个名字是来自`Package.swift`吗？库位置是包含 Swift 构建流程输出的路径。

最后，我们可以构建安润自己的围棋程序:

```
$ go build -o hello
$ ./hello
Welcome, Bob! 
```

Enter fullscreen mode Exit fullscreen mode

这很好！但是，如前所述，与 Golang 或 Swift 中的“正常”开发相比，这种方法存在几个问题。

### 内存管理

Swift 和 Go 都有自动内存管理，比如内存分配和垃圾收集。因此，通常在 Swift 或 Go 中编写代码时，我们不需要担心内存泄漏和缓冲区溢出之类的问题。

为了实现自动内存管理，运行时需要完全控制所有分配的内存，并知道所有指针的位置。这意味着 Swift 的运行时不能管理 Go 的运行时创建的指针，反之亦然。

因此，通过共享库传递指针时会有一些限制。传递给 C 代码的 Go 指针必须在 C 调用期间被固定，并且当 C 函数返回时，C 代码必须停止存储 Go 指针。如果你不严格遵守这些和其他规则，事情会很快变得一团糟。

底线是你必须对内存管理小心谨慎。这是一个很大的缺点，因为当使用共享库中的函数时，您将失去自动内存管理的便利性和安全性。我们必须在 Swift 代码中使用的类型名`UnsafePointer`说明了一切。

### 表现

使用 C 作为中间语言意味着我们需要将 Go 类型转换成 C 类型，将 C 类型转换成 Swift 类型，反之亦然。这尤其会导致影响应用程序性能的开销。对于简单的程序来说，这可能根本不是问题。但是当性能很重要时，总有比通过共享库来桥接语言更好的解决方案。

### 跨平台支持

虽然 Go 可以在许多平台上使用，但 Swift 不能。通过将 Go 程序链接到 Swift 库，您将失去 Golang 提供的广泛平台支持。

## 那么，这是个好主意吗？

“是”，因为好玩(！)并在 Golang 和本地 MacOS 框架之间架起了一座桥梁。但也是“不”，因为，因为从 Go 调用 Swift 会产生使用单一语言时不会出现的问题。

换句话说，选择 Golang/Swift 路线是否适合你，取决于你的项目以及是否有其他方法来实现你的目标。例如，可以使用 Objective-C 作为中间语言来调用 MacOS APIs。

但是，除此之外，这绝对是值得尝试的事情。

*在 Github* 上有示例代码[的存储库。](https://github.com/onderweg/swift-from-go)

## 阅读更多

*   反过来说:
    *   [在 Go 和 Swift 中编写应用](https://youngdynasty.net/posts/writing-mac-apps-in-go/)
    *   [从其他语言调用 Go 函数](https://medium.com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c7d8bcc69bf)