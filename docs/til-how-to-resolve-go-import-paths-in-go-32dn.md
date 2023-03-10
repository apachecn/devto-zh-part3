# TIL:如何在 Go 中解析 Go 导入路径

> 原文：<https://dev.to/rhysd/til-how-to-resolve-go-import-paths-in-go-32dn>

Go 导入路径对应于文件系统中的一个目录。例如，在我的环境中，`import "fmt"`被解析为`/usr/local/opt/go/libexec/src/fmt` ( `opt`是一个指向自制程序最新 Go 安装目录的符号链接)。

我需要在 Go 中解析导入路径。

一开始尝试了`go/ast`和`go/types`套餐。它们都有`ast.Package`、`ast.ImportSpec`和`types.Package`结构。但是它们都没有包的目录路径。他们只有它的路径(如`"fmt"`)。

*   [https://golang.org/pkg/go/ast/#Package](https://golang.org/pkg/go/ast/#Package)
*   [https://golang.org/pkg/go/ast/#ImportSpec](https://golang.org/pkg/go/ast/#ImportSpec)
*   [https://golang.org/pkg/go/types/#Package](https://golang.org/pkg/go/types/#Package)

但是 Go 编译器会解析每个导入路径。为了知道它是如何做到的，我研究了 Go 资源。类型检查器需要解析导入路径以了解外部包中的符号类型。因此它必须解析导入路径。

实际上解析路径是在类型检查中的`Importer`接口值中完成的。Go 标准库提供了三类导入器；`gc`、`gccgo`和`source`，其中`gc`为标准 Go 编译器，`gccgo`为 cgo，`source`为静态分析工具。
`source`似乎符合我的用例。

`source`导入器的实现在`go/internal`包中。

*   [https://github . com/golang/go/blob/624 e 197 c 71 b 673 f 0 B3 ebc 57 f 774536131 B4 f0f 26/src/go/internal/srcimporter/srcimporter . go # L23](https://github.com/golang/go/blob/624e197c71b673f0b3ebc57f774536131b4f0f26/src/go/internal/srcimporter/srcimporter.go#L23)

我发现了它是如何解析导入路径的。

> bp，err := p.ctxt.Import(path，srcDir，0)

其中`ctxt`是`go/build.Context`的实例。它利用了`go/build.Context.Import`的方法。

*   [https://golang.org/pkg/go/build/#Context.Import](https://golang.org/pkg/go/build/#Context.Import)

让我们看看它是否给出了我想要的:

```
package main

import (
    "fmt"
    "go/build"
)

func main() {
    // Use default context
    ctx := build.Default

    for _, importPath := range []string{
        "fmt",
        "github.com/pkg/errors",
    } {
        pkg, err := ctx.Import(importPath, ".", build.FindOnly)
        if err != nil {
            panic(err)
        }
        fmt.Println(importPath, "->", pkg.Dir)
    }
} 
```

输出:

```
fmt -> /usr/local/Cellar/go/1.11.2/libexec/src/fmt
github.com/pkg/errors -> /Users/rhysd/.go/src/github.com/pkg/errors 
```

耶！我终于可以将导入路径解析为目录路径。

设置`build.FindOnly`标志更好地避免了收集整个包信息的成本。

注意:Go 版本是 1.11.2