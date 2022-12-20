# Golang:指针–详细概述

> 原文：<https://dev.to/setevoy/golang-pointers-detailed-overview-2n8d>

[![](img/16e8310c93f925081d728c221199ea64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cxvuWL-R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2018/04/golang-color-icon2.png) 什么是*指针*？

简而言之，指针是一个变量，它存储另一个变量的地址，在那里存储一些数据。

### 指针示例

让我们举一个使用指针的最简单的例子:

```
package main

import "fmt"

func main() {
    a := 1
    b := &a
    fmt.Println("A: ", a)
    fmt.Println("B: ", b)
    fmt.Println("B: ", *b)
} 
```

这里:

1.  使用整数类型和值 *1* 创建变量`a`
2.  用指向整数类型的*指针创建一个变量`b`(见下文)*
3.  和数据输出:
    1.  首先是`a`值
    2.  *值*(！)或`b`变量的*内容*
    3.  最后，我们正在获取`a`的值，其中`b`被指向(稍后将查看`*`和`&`操作符)

运行代码:

```
$ go run pointers_example.go
A:  1
B:  0xc0000140e8
B:  1 
```

在第二行，我们看到了内存地址，`b`指针指向了这个地址。

第三行——我们从这个内存地址中得到值。

指针可以用类型规范更充分的方式初始化，而不是使用`:=`，所以代码看起来像:

```
...
func main() {
    var a int = 1
    var b *int = &a
    fmt.Println("A: ", a)
    fmt.Println("B: ", b)
    fmt.Println("B: ", *b)
}
... 
```

在`var b *int = &a`行中，我们设置`b`变量是一个指向整数数据的指针。

以同样的方式，一个指向字符串数据的指针可以用`*string`而不是数据类型
中的`*int`来创建

```
...
    var c *string  
    fmt.Printf("The C var type: %T, default value: %v\n", c, c)
    var d string = "This is a string"
    c = &d
    fmt.Printf("The C var type: %T, default value: %v, string value: %s\n", c, c, *c) 
... 
```

这里:

1.  `c`变量被创建为指向字符串数据的指针
2.  使用`Printf()`的修饰符显示`c`变量的数据类型(`%T`)及其值(`%v`)
3.  `d`变量是用字符串数据类型和“*这是一个字符串*值创建的
4.  对于`c`变量，设置`d`变量的存储地址
5.  使用`Printf()`的修饰符`c`变量的数据类型(`%T`)、其值(`%v`)以及来自存储在`c`中的内存地址的值

运行:

```
$ go run pointers_example.go
The C var type: *string, default value: <nil>
The C var type: *string, default value: 0xc0000101e0, string value: This is a string 
```

### `*`和`& operators`

我们已经在上面的例子中使用了它们，但是让我们仔细看看。

`*`操作符是一个*解引用操作符*。

这里的*解引用*意味着我们得到的不是一个指针的值(它存储一个地址),而是来自这个指针所在的内存地址……很好地指向:-)

让我们回到之前的例子:

```
...
fmt.Printf("The C var type: %T, default value: %v, string value: %s\n", c, c, *c)
... 
```

这里:

*   `default value: %v`与`с`一起–显示存储在 c 变量中的值–一个内存地址，c 指向该地址
*   `string value: %s`带`*с`–显示从`c`变量调用内存后得到的值

`&`操作符返回一个变量的内存地址。

例如，让我们在前面的例子中再增加一行，让我们使用`Printf()`和`%p`修饰符:
来显示地址

```
...
    var c *string  
    fmt.Printf("The C var type: %T, default value: %v\n", c, c)
    var d string = "This is a string"
    c = &d
    fmt.Printf("The C var type: %T, default value: %v, string value: %s\n", c, c, *c)
    fmt.Printf("The D adress: %p\nThe C address: %p\nThe C value: %v\n", &d, &c, c)
... 
```

检查:

```
$ go run pointers_example.go
The C var type: *string, default value: <nil>
The C var type: *string, default value: 0xc0000101e0, string value: This is a string
The D adress: 0xc0000101e0
The C address: 0xc00000e028
The C value: 0xc0000101e0 
```

这里我们得到了 *0xc0000101e0* 值作为`d`变量的地址， *0xc00000e028* 作为`c`变量的地址，但是`c`本身存储了`d`变量的地址-*0xc 0000101 e 0*。

实际上，`c`指针变量中的数据初始化是通过获取`d`变量的地址来完成的:

```
...
c = &d
... 
```

### `new()`功能

使用`var pointername *type`符号定义和初始化一个指针——你可以使用内置的 [`new()`](https://golang.org/pkg/builtin/#new) Go 函数，它接受一个数据类型作为第一个参数，并将返回一个指针指向为变量数据分配的内存:

```
...
    a := 1
    b := new(int)
    fmt.Printf("A: %d, B: %v, %v\n", a, b, *b)
    b = &a
    fmt.Printf("A: %d, B: %v, %v\n", a, b, *b) 
... 
```

这里:

1.  用值 *1* 创建`a`变量
2.  创建了`b`变量，它将保存一个指针，指向 new()函数返回的内存，它现在保持 0，因为已经分配的内存不能保存`nil`
3.  用`a`的地址更新`b`的值

检查:

```
$ go run pointers_example.go
A: 1, B: 0xc000014100, 0
A: 1, B: 0xc0000140e8, 1 
```

### 改变指针的值

嗯，说“改变指针的值”是不正确的，因为指针变量存储的是内存地址，而不是值本身。

但是使用一个指针，我们可以在这个指针指向的内存位置改变这个值。

例如:

```
...
    a := 1
    b := &a
    fmt.Println("A: ", a)
    fmt.Println("B: ", *b)
    *b = 2
    fmt.Println("B: ", *b)
... 
```

这里:

1.  `a`变量的值为 *1*
2.  `b`变量有`a`的地址
3.  显示的`a`变量值
4.  显示的值取自`b`所指向的地址
5.  我们正在将该存储器中的值更改为 *2*
6.  并显示新值

运行代码:

```
$ go run pointers_example.go
A:  1
B:  1
B:  2 
```

### 将指针作为函数的参数传递

你可以传递一个指向函数的指针作为它的参数。

例如:

```
package main
import "fmt"
func setVal(b *int) {
    *b = 2
}
func main() {
    a := 1
    b := &a
    fmt.Println("Init values")
    fmt.Println("A: ", a)
    fmt.Println("B: ", *b)
    setVal(b)
    fmt.Println("Changed values")
    fmt.Println("A: ", a)
    fmt.Println("B: ", *b)
} 
```

这里我们创建了一个`setVal()`函数，它接受一个整数指针作为参数，并将改变用这个指针传递的地址中的一个值。

检查一下:

```
$ go run pointers_example.go
Init values
A:  1
B:  1
Changed values
A:  2
B:  2 
```

调用`setVal()`后，`a`和`b`将显示一个新值。

甚至更多——我们可以只传递一个`a`变量的地址给`setVal()` :

```
...
func setVal(b *int) {
    *b = 2
}
func main() {
    a := 1
    fmt.Println("Init values")
    fmt.Println("A: ", a)
    setVal(&a)
    fmt.Println("Changed values")
    fmt.Println("A: ", a)
} 
```

结果是:

```
$ go run pointers_example.go
Init values
A:  1
Changed values
A:  2 
```

### 函数:通过值和引用传递参数

这里有点跑题，但是使用上面的例子，通过值传递参数和通过引用传递参数之间的差异也可以显示出来。

让我们更新这个例子:

```
...
func setVal(b *int, c int) {
    *b = 2
    c = 4
    fmt.Printf("B from setVal(). Poiner to: %p, val: %v\n", b, *b)
    fmt.Printf("C from setVal(). Addr: %p, val: %v\n", &c, c)
}
func main() {
    a := 1
    b := &a
    c := 3
    fmt.Println("Init values")
    fmt.Printf("A from main(). Addr: %p, val: %v\n", &a, a)
    fmt.Printf("B from main(). Poiner to: %p, val: %v\n", b, *b)
    fmt.Printf("C from main(). Addr: %p, val: %v\n", &c, c)
    fmt.Println("Changed values")
    setVal(b, c)

    fmt.Printf("A from main(). Addr: %p, val: %v\n", &a, a)
    fmt.Printf("B from main(). Poiner to: %p, val: %v\n", b, *b)
    fmt.Printf("C from main(). Addr: %p, val: %v\n", &c, c)
} 
```

这里:

1.  获取`a`变量地址及其值
2.  获取存储在`b`变量中的地址，然后获取存储在该地址上的数据
3.  获取`c`变量地址及其值
4.  调用`setVal()`函数，通过`b`和`c`中的引用将`a`的值作为一个值传递
5.  在`setVal()`中，获取存储在`b`变量中的地址和存储在那里的值
6.  在`setVal()`中获取`c`变量的地址和存储在该存储区中的值
7.  在`main()`中获取`a`的地址和存储在那里的值
8.  在`main()`中获取存储在`b`变量中的地址和值
9.  在`main()`中获取`c`变量地址和存储在那里的值

运行它:

```
$ go run pointers_example.go
Init values
A from main(). Addr: 0xc0000140e8, val: 1
B from main(). Poiner to: 0xc0000140e8, val: 1
C from main(). Addr: 0xc000014100, val: 3
Changed values
B from setVal(). Poiner to: 0xc0000140e8, val: 2
C from setVal(). Addr: 0xc000014130, val: 4
A from main(). Addr: 0xc0000140e8, val: 2
B from main(). Poiner to: 0xc0000140e8, val: 2
C from main(). Addr: 0xc000014100, val: 3 
```

这里:

1.  `a`存储在 *0xc0000140e8* 位置，其值为 *1*
2.  `b`指向相同的位置 *0xc0000140e8* 并返回相同的 *1* 值
3.  `c`与 *3* 值一起存储在 *0xc000014100* 位置
4.  `setVal()`被称为
5.  `setVal()`中的`b`仍然指向 *0xc0000140e8* 位置，其值为 *2*
6.  `setVal()`中的`c`得到了新的地址 *0xc000014130* ，其中存储了 *4*
7.  `main()`中的`a`现在保持来自同一 *0xc0000140e8* 位置的 *2* 值
8.  `main()`中的`the b`仍然与`setVal()`中的相同–指向相同的位置并返回相同的值
9.  在`c`变量的`main()`中没有任何改变，因为`setVal()`中的`c`有自己的地址 *0xc000014130* ，但是`main()`中的`c`使用了 *0xc000014100* 位置

实际上，为了更好地理解什么是指针以及如何使用它们，这些都是需要知道的。

### 类似的帖子

*   <small>04/13/2019</small> [Go:查看 Github 中的公共存储库列表。去切片比较。第一次 Golang 体验。](https://dev.to/setevoy/go-checking-public-repositories-list-in-github-go-slices-comparison-the-first-golang-experience-1ddm) <small>(0)</small>
*   <small>04/08/2019</small> [Atom:有用插件](https://rtfm.co.ua/en/atom-useful-plugins/) <small>(0)</small>
*   <small>2019 年 1 月 4 日</small>[【Redis:复制，第 3 部分──redis py 与 Python 的 redis 哨兵一起工作](https://dev.to/setevoy/redis-replication-part-3-redis-py-and-work-with-redis-sentinel-from-python-mb9)<small></small>
<small></small>

<small></small>