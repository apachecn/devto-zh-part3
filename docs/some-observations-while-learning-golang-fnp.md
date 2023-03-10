# 学习戈兰语的几点体会

> 原文：<https://dev.to/buphmin/some-observations-while-learning-golang-fnp>

所以我最近在 golang 中做了一些编码，我想我会分享一些我的观察。

## 标准库非常健壮

到目前为止，我发现我需要做的所有事情至少可以用标准库很好地完成。这并不是说它是完美的，但没有什么是我做不到的。需要调用一个 API？只需导入 net/http。想把东西转换成 json？只需导入编码/json。Go 的标准库可以满足你所有的基本需求，包括:IO、文件系统、加密等。到目前为止，我唯一不喜欢的东西是标准记录器。为此，我用 [zap 记录器](https://github.com/uber-go/zap)替换了它。

## Go 的依赖管理可以做得更好

到目前为止，我一直在 2018 年 8 月发布的 go 1.11 中专门使用 Go 模块作为一项功能。这允许每个项目存在于文件系统中任何地方的单独目录中，并且具有版本控制的依赖关系。根据我的理解，这是第一个拥有项目特定依赖关系的官方方式。到目前为止，虽然我已经发现有些事情就是不能使用 go 模块。例如，我无法使用 go 模块来使用 [Revel](https://revel.github.io/) ，我曾计划在我的框架印象系列的第二篇文章中探讨这一点。与我所熟悉的两个包管理器 npm 和 composer 相比，Go 需要一些时间来使一切标准化，并让所有的库都上线。

## 围棋是万能的

具体来说，go 有三个独特的特性为开发人员提供了极大的灵活性:任何数据类型的方法、函数的多个返回值以及接口。

### 对任何数据类型的方法

所以在 go 中任何数据类型都可以有方法。比如说，我想要一个可以自我加倍的整型数。

```
type Doubler int

func (d *Doubler) Double() {
    (*d) = (*d)*2
}

func main() {
    var num Doubler
    num = 3
    num.Double()
    fmt.Println(num) //prints 6!
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能非常方便，尽管小心不要滥用它。我可以很容易地看到这个功能被滥用到一些奇怪的意大利面。

### 多个返回值

因此，在 go 函数中可以返回任意数据类型方差的多个值。这允许一些非常好的可能性，需要在其他语言中使用变通方法来实现。例如，你想比较两个人，看他们是否相同:

```
type Person struct {
    Name string
    Age int
}

//isSamePerson takes two Person structs and returns whether they are equal
//It also returns a list of differences
func isSamePerson(firstPerson Person, secondPerson Person) (bool, []string) {
    differences := []string{}
    isSame := true

    if firstPerson.Name!= secondPerson.Name{
        differences = append(differences, "Names do not match")
        isSame = false
    }

    if firstPerson.Age != secondPerson.Age {
        differences = append(differences, "Ages do not match")
        isSame = false
    }

    return isSame, differences
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个函数，检查两个人是否相同，我们可以得到我们需要的布尔值，但我们也可以看到有什么不同。

### 界面

Go 中的接口与其他语言有很大不同。接口代表了定义函数行为的两部分代码之间的一种契约。在大多数面向对象的语言中，接口是一个类的显式声明，声明它必须在接口中具有所定义的要求。Go 对它的接口采取了一种不同的方法，让它们被隐式定义。任何具有与接口签名匹配的方法的数据类型都可以使用。这在我看来有好有坏。结合 Golang 在任何数据类型上使用方法的能力，这允许非常灵活的使用。这意味着如果方法匹配，任何数据类型都可以满足接口。此外，一个数据类型可能满足多个接口而不会发生冲突。我发现在编码时，很难确定你正在实现一个接口。这很大程度上是由于 Go 接口的隐式特性。示例时间！

```
type Doubleable interface {
    Double()
}

type DoubleInt int 

func (d *DoubleInt) Double() {
    (*d) = (*d) * 2
}

type DoublePeople struct {
    NumPeople int
}

func (d *DoublePeople ) Double() {
    d.NumPeople *= 2
}

func main() {
    var numInt DoubleInt = 1
    people:= DoublePeople{NumPeople: 4}

    doDouble(&numInt)
    doDouble(&people)
    fmt.Println(numInt)//prints 2
    fmt.Println(people.NumPeople)//prints 8
}

//doDouble takes a doubler and doubles it
func doDouble(doubler Doubleable)  {
    doubler.Double()
} 
```

Enter fullscreen mode Exit fullscreen mode

int 类型和 struct 类型都可以是 Double 类型，并且都可以传递给 doDouble 函数，因为它们都隐式地满足 double 接口。

## Go 有一些不错的工具

与我使用过的其他语言不同，Go 有一些很好的内置工具。单元测试、基准测试和概要分析都包括在内，无需安装任何第三方工具。此外，调试也从一开始就起作用。这意味着开始编写 go 代码非常简单，至少在使用 go 模块时是这样。Go vet 可以检查你的代码中的常见问题，go fmt 可以按照 Go 标准格式化你的代码。总的来说，与 PHP 和 JS 相比，至少我*需要*安装更少的东西来开始编写和测试代码。这并不意味着所有的工具对每个人都是完美的，例如，你可能想用断言库或 BDD 框架来扩展测试套件。无论如何，Go 为你提供了编写经过良好测试的代码所需的工具。

Go 是一种简洁的语言，到目前为止，我发现它是一种非常简单的语言。只有很少的语法和相当少量的概念需要你知道才能有效。