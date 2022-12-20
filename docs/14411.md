# 关于围棋中指针和切片的小课程

> 原文：<https://dev.to/taylorsilva/small-lesson-about-pointers--slices-in-go-2gd9>

今天我学到了更多关于指针在围棋中的工作原理。

我们希望其他函数直接修改这个对象。因此，我们将指向该对象的指针传递给各种函数，然后在测试中检查原始对象是否被正确修改。我们还有这些助手函数，它们应该返回一个指向埋在`struct`内部的对象的指针。

下面是我们的对象的一个例子:

```
type obj struct {
    num   int
    str   string
    slice []int
} 
```

然后我们有一个助手函数，它应该返回对`slice` 中的**元素的引用。**

在使用我们的示例对象时，我有一个如下所示的助手函数:

```
func FindNumber(object *obj, findInt int) *int {
    for _, singleInt := range object.slice {
        if singleInt == findInt {
            return &singleInt
        }
    }
    return nil
} 
```

它应该进入一个给定的对象，并返回一个指向`obj.slice`参数中匹配元素的指针。如果我们现在把这些放在一起，我们得到这个程序:

```
package main

import "fmt"

type obj struct {
    num   int
    str   string
    slice []int
}

func main() {
    myObject := obj{num: 1, str: "hello", slice: []int{1, 2, 3}}

    pointerToNumber := FindNumber(&myObject, 2)

    *pointerToNumber = 5 // change the value from 2 to 5
    fmt.Printf("%p - %v - from FindObject()\n", pointerToNumber, *pointerToNumber)
    fmt.Printf("%p - %v - Pointer address I actually wanted\n", &myObject.slice[1], myObject.slice[1])
}

func FindNumber(object *obj, findInt int) *int {
    for _, singleInt := range object.slice {
        if singleInt == findInt {
            return &singleInt
        }
    }
    return nil
} 
```

当我们运行它时，我们得到这个输出:

```
0xc00001a0b8 - 5 - from FindObject()
0xc0000182e8 - 2 - Pointer address I actually wanted 
```

在我的原始对象中，值没有变成 5，我们可以看到指针地址不匹配。我对这个指针值的任何改变都不会反映在`myObject`变量中。

让我们一行一行地想想为什么会这样。

首先我们制作我们的对象:

```
myObject := obj{num: 1, str: "hello", slice: []int{1, 2, 3}} 
```

我们将对象的指针传递给`FindNumber()`，告诉它返回一个指向片内元素的指针，该元素等于`2` :

```
pointerToNumber := FindNumber(&myObject, 2) 
```

在`FindNumber()`中，我们从指针中获取切片，并对其进行迭代:

```
for _, singleInt := range object.slice {
    ...
} 
```

让我们停下来想想这个`for`循环。我们有一个指向`object`的指针，我们用这个指针来访问`slice`参数。让我们记住，切片只是一个指向数组的**指针**，而不是指向数组中包含的每个**元素**的指针。

因此，当`range`从它所传递的任何切片中获取一个元素时，它**将该元素的值**传递给我们的`singleInt`变量。所以`singleInt`不是指向元素的指针，它只是原始元素的副本！如果我想返回一个指向切片中实际元素的指针，我必须使用从 range 返回的索引，并返回来自原始切片的指针:

```
func FindNumber(object *obj, findInt int) *int {
    for index, singleInt := range object.slice {
        if singleInt == findInt {
            return &object.slice[index]
        }
    }
    return nil
} 
```

现在我的输出是这样的:

```
0xc0000182e8 - 5 - from FindObject()
0xc0000182e8 - 5 - Pointer address I actually wanted 
```

地址匹配，值已更改！

还有另一种方法来解决这个问题，它显示了 Go 中按值传递的一些危险。

如果我们将`FindNumber()`改为不接受指针:

```
func FindNumber(object obj, findInt int) *int {
    fmt.Printf("%p - address of object in FindNumber\n", &object)
    ...
} 
```

而不是传入一个指向我们对象的指针:

```
fmt.Printf("%p - address of myObject\n", &myObject)
pointerToNumber := FindNumber(myObject, 2) 
```

现在运行我们的代码:

```
0xc000076180 - address of myObject
0xc0000761b0 - address of object in FindNumber
0xc000090028 - 5 - from FindObject()
0xc000090028 - 5 - Pointer address I actually wanted 
```

我们看到`FindNumber()`得到了原始对象的副本，但是它返回的指针仍然允许我们改变原始对象中的值。因此，当我们按值传递任何结构时，结构内部的片都指向同一个数组。这是有意义的，因为指针只是一个内存地址，复制地址不会改变地址的位置。如果一个指针指向`go to 0xc000076180`，当我复制它时，我的新指针也会指向`go to 0xc000076180`。

如果您想避免意外修改原始切片，那么您应该创建切片的`copy()`。

希望这让你对围棋中的切片和指针有了更多的了解。