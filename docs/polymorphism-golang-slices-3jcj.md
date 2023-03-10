# Go 切片的多态性

> 原文：<https://dev.to/vicentdev/polymorphism-golang-slices-3jcj>

[![](img/bbeec856802514a4b5f29cffa4c1e46c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AYZk55Y7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3pmpi3c2hiegs70lcvct.jpg)

嗨 devs！

今天我将向您展示如何在 golang 上用切片实现多态性。

想象一下，你想在不同的结构上实现一个相似的函数。界面是你最好的朋友！比如经典函数`toString()`

```
//our interface
type Element interface {
    toString() string
}

//our first struct
type MyCustomStruct1 struct {
    prop1 string
    prop2 string
}

//our second struct
type MyCustomStruct2 struct {
    awesomeProp string
}

//abstract implementation of our interface for MyCustomStruct1
func (m MyCustomStruct1) toString() string {
    return m.prop1 + m.prop2
}

//abstract implementation of our interface for MyCustomStruct2
func (m MyCustomStruct2) toString() string {
    return m.awesomeProp
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，golang 中没有新的接口功能。嗯，现在，我们可以认为我们可以编写这样的代码:

```
 func printElementList(elements []Element) {
    for _, e := range elements {
        fmt.Println(e.toString())
    }

}

//code inside main or another function
ms1 := []MyCustomStruct1{
    MyCustomStruct1{prop1: "foo", prop2:"bar"},
    MyCustomStruct1{prop1: "foo1", prop2:"bar1"},
}

printElementList(ms1) 
```

Enter fullscreen mode Exit fullscreen mode

考虑到层次结构和多态性的共同功能，这可能在其他语言中也能工作。然而，编译器会抛出这个错误:

`cannot use ms1 (type []MyCustomStruct1) as type []Element in argument to printElementList`

好吧，我们怎么能接受那个问题呢？一开始我认为一个`map[int]Element`可以解决这个问题，但是我不想让我的切片在一个不使用整数的地图上排序。

我的解决方案是什么？我用这样的接口包围了 slice 的元素:

```
var elements []interface{Element}

for _, m:= range ms1 {
    elements = append(elements, m)
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，我改变了函数的声明和调用:

```
func printElementList(elements []interface{Element}) {
    for _, e := range elements {
        fmt.Println(e.toString())
    }
}

//code inside main or another function
printElementList(elements) 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们可以将`printElementList`函数与 MyCustomStruct2 的一个切片或实现元素接口的另一个结构体一起使用:

```
ms2 := []MyCustomStruct2{
     MyCustomStruct2{"foo"},
     MyCustomStruct2{"bar"},
}

var elements2 []interface{Element}

for _, m:= range ms2 {
    elements2 = append(elements2, m)
}

printElementList(elements2) 
```

Enter fullscreen mode Exit fullscreen mode

你对此有什么看法？是一个过于复杂的实现。

这里有 play 的完整代码链接。golang:

[https://play.golang.org/p/B88iV16nm4u](https://play.golang.org/p/B88iV16nm4u)