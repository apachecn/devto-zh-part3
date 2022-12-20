# 围棋循环

> 原文：<https://dev.to/stephenafamo/loops-in-go-2lp9>

[![Loops in Go](img/db9594e22103144d3f6df9cff1cf7c06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDG6F152--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/stephenafamo.com/blog/wp-content/uploads/2019/03/Loops-in-Go.jpg%3Ffit%3D1500%252C750%26ssl%3D1)

在 Go 中执行循环的关键字只有一个:for。实现非常灵活。在本文中，我们将考虑使用它的各种方法。如果你对细节感兴趣，你一定要去看看[官方规范](https://golang.org/ref/spec#For_statements)。

## 为围棋中的循环

首先，让我们看看如何复制常见的 C 风格 for 循环。在 Go 中，它的工作方式与其他语言类似。我们在关键字后提供 3 个语句。第一个是要运行的初始语句，第二个是在每次迭代之前评估的条件，第三个是在迭代结束时运行的条件。

```
package main

import "fmt"

func main() {
    for i := 1; i <= 10; i++ {
        fmt.Println(i)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
1
2
3
4
5
6
7
8
9
10 
```

Enter fullscreen mode Exit fullscreen mode

## 在围棋中循环往复

在大多数计算机编程语言中，while 循环是一种控制流语句，允许代码基于给定的布尔条件重复执行。while 循环可以被认为是一个重复的 if 语句。

在 Go 中，我们使用`for`关键字通过只提供一条语句来编写 while 循环。只要语句为真，循环就会继续运行。

```
package main

import "fmt"

func main() {
    var a = 1

    for a <= 5 {
        fmt.Printf("a is: %v \n", a)
        a++
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
a is: 1 
a is: 2 
a is: 3 
a is: 4 
a is: 5 
```

Enter fullscreen mode Exit fullscreen mode

## 范围子句

`range`子句允许我们迭代数组、切片、映射、字符串或通道。

### 使用范围在数组上循环

当我们使用`range`在一个数组上循环时，每次迭代我们将得到 2 个值。每个元素的索引和值的副本。接收第二个值是可选的。

```
package main

import "fmt"

func main() {
    var myArray = [...]int{1,2,3,4}

    for k, v := range myArray {
        fmt.Printf("index=%v, value=%v \n", k, v)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
index=0, value=1 
index=1, value=2 
index=2, value=3 
index=3, value=4 
```

Enter fullscreen mode Exit fullscreen mode

### 使用范围在切片上循环

就像使用数组一样，当我们使用`range`在一个片上循环时，每次迭代我们将得到 2 个值。每个元素的索引和值的副本。接收第二个值是可选的。

```
package main

import "fmt"

func main() {
    var mySlice = []int{1,2,3,4}

    for k, v := range mySlice {
        fmt.Printf("index=%v, value=%v \n", k, v)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
index=0, value=1 
index=1, value=2 
index=2, value=3 
index=3, value=4 
```

Enter fullscreen mode Exit fullscreen mode

### 使用范围在地图上循环

正如您所料，当我们使用`range`遍历一个地图时，我们也收到 2 个值。这一次，值是映射中元素的键，也是值的副本。接收第二个值是可选的。

```
package main

import "fmt"

func main() {
    var myMap = map[string]string{
        "foo": "bar",
        "boo": "baz",
    }

    for k, v := range myMap {
        fmt.Printf("key=%v, value=%v \n", k, v)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
key=foo, value=bar 
key=boo, value=baz 
```

Enter fullscreen mode Exit fullscreen mode

### 使用范围在字符串上循环

当我们使用`range`对一个字符串进行循环时，我们在每次迭代中也会收到 2 个值。第一个值是字符开始的字节位置，第二个值是代表单个 Unicode 字符的`rune`。通常，接收第二个值是可选的。

```
package main

import "fmt"

func main() {
    var myString = "Stephen"

    for index, runeValue := range myString {
        fmt.Printf("%#U starts at byte position %d\n", runeValue, index)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
U+0053 'S' starts at byte position 0
U+0074 't' starts at byte position 1
U+0065 'e' starts at byte position 2
U+0070 'p' starts at byte position 3
U+0068 'h' starts at byte position 4
U+0065 'e' starts at byte position 5
U+006E 'n' starts at byte position 6 
```

Enter fullscreen mode Exit fullscreen mode

### 使用范围在一个通道上循环

当我们使用`range`在一个通道上循环时，我们接收一个值，这是通道中的下一个元素。

```
package main

import "fmt"

func main() {
    queue := make(chan string, 2)
    queue <- "one"
    queue <- "two"
    close(queue)

    for elem := range queue {
        fmt.Println(elem)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
one
two 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们已经看到了围棋中的各种循环机制。现在前进，走向成功。 [![😎](img/6cf47269a87d637578782b4945735d2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5K4ICNjO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/1f60e.png) 。

Go 中的 [Loops 这个帖子首先出现在](https://stephenafamo.com/blog/loops-in-go/)[斯蒂芬·阿法莫的博客](https://stephenafamo.com/blog)上。