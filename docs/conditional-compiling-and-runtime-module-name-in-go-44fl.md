# Go 中的条件编译和运行时模块名

> 原文：<https://dev.to/geosoft1/conditional-compiling-and-runtime-module-name-in-go-44fl>

Go 语言中的条件编译是一个非常好的特性，但是如果我们想在运行时知道我们刚刚编译了什么模块呢(不要和新的 Go 模块混淆！).我们可以将文件定义为应用程序的一个特定模块。因此，知道文件是否被编译可能很有趣，但是我们没有一个标准的机制在运行时做到这一点。

假设我们有三个空文件`main.go`、`module1.go`和`module2.go`什么都不做，并且是包`main`的一部分。

`main.go`文件

```
package main

func main() {
} 
```

`module1.go`文件

```
//+build module1

package main 
```

`module2.go`文件

```
//+build module2

package main 
```

编译时，编译参数以`tags`的形式给出，例如:

```
 go build -tags="module1 module2" -o test 
```

不幸的是，编译过程结束后，构建参数不再可用，没有地方写什么编译了，什么没有。例如，如果我们想在运行时打印有效的编译文件，我们就不能。

棘手的部分来了。在 Go `runtime`包中，我们有 [Caller()](https://golang.org/pkg/runtime/#Caller) 函数，它知道当前的调用文件，可以用来识别文件是否被编译。因此，我们的虚拟程序变成如下所示。

`main.go`文件

```
package main

import (
    "fmt"
    "path/filepath"
    "runtime"
    "strings"
)

var mods []string

// include this in every init() function to detect linked modules
func _init() {
    _, file, _, _ := runtime.Caller(1)
    mods = append(mods, strings.TrimSuffix(filepath.Base(file), ".go"))

}
func init() {
    _init()
}

func main() {
    fmt.Println(mods)
} 
```

`module1.go`文件

```
//+build module1

package main

func init() {
    _init()
} 
```

`module2.go`文件

```
//+build module2

package main

func init() {
    _init()
} 
```

我们已经定义了`_init()`函数，我们将把它包含在每个模块的每个`init()`函数中。在运行时，`init()`函数将更新一个易于打印的名字数组。`Caller()`参数表示嵌套层次。

再次奔跑。

```
go build -tags="module1 module2" -o test
./test
[main module1 module2]
go build -tags="module1" -o test
[main module1] 
```

这种技术假设您将在文件名和构建标签之间保持一致，因此，要注意这一点以避免混淆。

享受吧。