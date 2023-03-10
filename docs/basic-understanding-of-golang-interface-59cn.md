# 对 Golang 界面的基本了解

> 原文：<https://dev.to/rapulu/basic-understanding-of-golang-interface-59cn>

作为一个 Go 初学者和一个来自显式实现接口的语言的人，我花了一些时间去理解，我也会解释为什么说它是隐式实现的。

从 golang 的角度来看，什么是接口？

接口是对象(例如结构或非结构类型)可以隐式实现的方法签名的集合。因此，接口允许指定只需要某些行为，这些行为是由一组方法定义的。

**理解接口方法签名**

第一句话是"*接口是方法签名的集合*"如果你是编程新手，你可能会觉得有点困惑，因为我以前也是这样，所以让我用图形解释一下这是什么意思。

```
package main

import "fmt"

type Walker interface{// Interface type to be implemented

    Walk() string // This is a method signature inside an interface
    Bark() string // Another method signature
}

func main(){
    fmt.Println("Interface method signatures")
} 
```

上面的代码声明了一个带有两个方法签名的接口类型，要隐式实现。

**显式实现**

为了充分理解一个对象如何隐式实现一个接口，让我们从另一种像 PHP 这样显式实现接口的语言来看一下。

[![PHP Interface](img/214b4a94763c7fe79c0ab6fe64f163ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lcVX5eRI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/poi0dcgqb9dy951mif8b.png) 
*一个 PHP 接口*

现在让我们构建一个类来实现它。

[![PHP Class Implements Interface](img/e9167a9f7e060c37f2bd9863ce6fc778.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x4_X3oBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sbhi24y9k1wz5jee0lrr.png)

接口中的每个方法都必须由该类实现。该类必须完全尊重接口，就像它是一个严格的契约一样。这是一个显式接口的例子，其中说明了类要实现什么接口。

**隐式实现**

我们已经看到了 PHP 是如何显式实现接口的，让我们来看看 Go 隐式的方式。你没有明确提到一个类型是否实现了一个接口。如果一个类型实现了一个接口中定义的签名方法，那么这个类型就实现了这个接口。

```
package main

import "fmt"

type Walker interface{
    Walk() string// This is a method signature inside an interface
}

//Non-struct Type to implement the walker interface
type Dog string

//create the method signature Walk
func (dog Dog) Walk() string{
    return "Dog can walk"
}

func main(){
    var dog Dog
    callWalker(dog)
}

func callWalker(w Walker){
    fmt.Println(w.Walk())
} 
```

[在这里运行这段代码](https://play.golang.org/p/_nHflMDe0fi)

注意，我没有在 Dog 类型中指定任何关于 Walker 的内容。

这是一个接口的隐式实现，重要的是类型 Dog 有一个 Walk 方法，该方法带有 Walker 接口中的签名。

当我在 Go 上学习界面时，这让我有点困惑，但最终，这可能是我爱上这门语言的主要因素。

我就讲到这里，尽量让它简短，这样你就不会觉得无聊了。

页（page 的缩写）任何人都非常欢迎美国的贡献。