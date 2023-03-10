# 从 PHP 到 isset 函数

> 原文：<https://dev.to/asanchez/from-php-to-go-the-isset-function-3oc2>

这是 PHP to Go 系列的第二部分，是所有 PHP 初学者和初学者的快速入门指南。第一部分是关于[数组、切片和贴图](https://dev.to/asanchez/from-php-to-go-arrays-3cfg)。

很多转投 Go 的 PHP 开发人员的一个常见问题是，Go 中是否有类似于 PHP isset()函数的函数。

回答:不，没有。但是在 Go 中你不需要一个函数来做到这一点。有不同的方法来检查一个变量是否被设置以及它是否不为空。

## 如何检查 Go 中是否存在变量？

有两件事你必须知道:

**首先**，在大多数情况下，如果变量根本不存在，你的代码甚至不会编译，所以你没有必要额外检查。您将看到问题“未定义的变量”以及调用该变量的确切代码部分。甚至在试图编译之前，您的 IDE 也可能会警告您这个错误。

请注意，我说的是“在大多数情况下”，因为在一些情况下，我们将在后面看到，编译器不知道变量是否存在。

这个不会编译:

```
package main

import (
    "fmt"
)

func main() {
    fmt.Println(foo)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
Compiler output: "test.go:8:17: undefined: foo" 
```

Enter fullscreen mode Exit fullscreen mode

**第二个**，记住非初始化变量或者空变量默认不是`nil`(nil 是 NULL 的 Go 等价物)而是根据变量的类型设置为“零值”。例如，如果你声明了一个整数类型的变量，但是你没有给它赋值，那么它的值将被默认设置为 0。对于字符串，默认值为“”，对于布尔值，默认值为 false。然而，如果没有初始化，映射是零。

```
var i int       // 0
var f float64   // 0
var b bool      // false
var s string    // "" 
```

Enter fullscreen mode Exit fullscreen mode

有些情况下，编译器不知道变量是否存在。这就是数组、切片和映射的情况，其中这些变量的元素可以动态创建。

## 检查地图中是否存在索引:

如果您试图检查一个键是否存在于一个映射中，您可以执行以下操作:

```
foo := map[string]string{"first": "Sunday", "second": "Monday", "third": "Tuesday"}

// We want to check if the key "first" exists: 
if _, ok := foo["first"]; ok {
    fmt.Println(foo["first"])
} 
```

Enter fullscreen mode Exit fullscreen mode

通过键调用一个映射项会返回两个值:第一个是这个键的值，第二个是一个布尔值，表示这个键是否存在:

```
value, exists = foo["first"] // value = "Monday", exists = true
value, exists = foo["nine"] // value = "", exists = false 
```

Enter fullscreen mode Exit fullscreen mode

如果您想通过值来检查某个特定元素是否存在，您将不得不遍历 map:

```
foo := map[string]string{"first": "Sunday", "second": "Monday", "third": "Tuesday"}

for key, val := range foo {
    if val == "Monday" {
        fmt.Printf("Yeah, it is %s, and its key is: %s", val, key)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在切片范围内，返回键作为第一个元素，返回值作为第二个元素。

## 检查切片中是否存在项目

正如我们在本系列的第一部分中看到的，[片在 Go](https://dev.to/asanchez/from-php-to-go-arrays-3cfg) 中没有键/值对，所以您不能寻找它们的键。

根据您具体需要做什么，这里有一些解决方案。

最常见的是完全按照上面在 maps:
中提到的方法迭代切片

```
var slice = []string{"Monday", "Tuesday", "Wednesday"}
for key, val := range slice {
    if val == "Monday" {
        fmt.Printf("Yeah, it is %s, and its key is: %d", val, key)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你只需要检查这个切片上是否有 X 个元素或者元素号 X 是否存在，你可以只使用`len()`

```
var slice = []string{"Monday", "Tuesday", "Wednesday"}
if len(slice) == 7 {
    fmt.Println("This week has 7 days")
} else {
    fmt.Println("This is a very short week")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 检查数组中是否存在某项

像切片一样，数组没有键/值对，所以你不能寻找它们的键。

您也不能使用`len()`方法，因为声明的元素数量将一直存在，如果已经给它们赋值，它们的值将被设置为零:

```
week := [7]string{}
if len(week) == 7 {
    // This is true, the week array has 7 elements, no matter if they are empty! 
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，对于数组，您可以使用上面提到的`range`迭代(我在这里不再重复)。如果这符合您的要求，您还可以检查项目是否有零值，因为这可能意味着项目“尚未设置”:

```
week := [7]string{}
if week[2] == "" {
    // The second element on week has its zero value, so maybe "it wasn't set" yet
} 
```

Enter fullscreen mode Exit fullscreen mode

那都是乡亲们！；)