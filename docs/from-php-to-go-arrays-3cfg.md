# 从 PHP 到 Go:数组

> 原文：<https://dev.to/asanchez/from-php-to-go-arrays-3cfg>

这是一篇关于 Go 中数组、切片和映射的*快速而肮脏的*文章，面向所有来自 PHP 并开始使用这种奇妙语言的人。

目录

1.  [基础知识](#basics)
2.  [Go 中的数组](#arrays)
3.  [切片在走](#slices)
4.  [Go 中的地图](#maps)
5.  [在 Go 中迭代数组、切片和地图](#iterate)
6.  [不同类型的子阵列](#different)

## 1。基础知识

有两件事你必须知道:

1.  在 Go 中，你有三种类似 PHP 数组的结构:[数组](https://gobyexample.com/arrays)、[切片](https://gobyexample.com/slices)、[地图](https://gobyexample.com/maps)。我们稍后会看到不同之处。
2.  在 Go 中，数组的元素具有相同的[类型](https://tour.golang.org/basics/11)。当您为此数组/切片/映射声明变量时，必须定义此类型。

这两种结构的主要区别在于数组的长度是否固定，以及它们是否有键/值对。

|  | 排列 | 薄片 | 地图 |
| --- | --- | --- | --- |
| 长度 | 固定的；不变的 | 可变的 | 可变的 |
| 键/值 | 不 | 不 | 是 |

## 2。围棋中的数组

假设我们想要声明一个 string 类型的数组来保存一周中的每一天:

```
week := [7]string{"Sunday", "Monday", "Tuesday", .., "Saturday"} 
```

Enter fullscreen mode Exit fullscreen mode

看看括号之间的七个是如何表示它将包含的元素数量的。一旦声明了它，就不能改变它上面的项数，但是可以改变值。

```
week[1] = "New Monday"  // This will work. "Monday" is now "New Monday"
week[8] = "New Day"     // This will fail, we are out of bounds.
week[1] = 1             // This will fail, cannot use type int as type string 
```

Enter fullscreen mode Exit fullscreen mode

由于长度固定，这样的数组很少在 Go 中使用。

## 3。围棋中的切片

如上所述，切片的工作方式类似于数组，但是它们的长度是可变的。

为了声明与上面相同的数组，但是作为一个片，我们简单地省略了括号之间的数字:

```
week := []string{"Sunday", "Monday", "Tuesday", .., "Saturday"} 
```

Enter fullscreen mode Exit fullscreen mode

由于没有固定的长度，我们可以向切片添加新的条目:

```
week[8] = "New Invented Day" // This will work, since there are no bounds
weeks = append(weeks, "New Invented Day") // We can also use the method append() 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以从切片中删除项目。如果我们想删除索引 1 的项目(“新星期一”):

```
weeks = append(week[:1], week[2:]...) 
```

Enter fullscreen mode Exit fullscreen mode

注意，和上面的代码一样，我们可以使用变量:
后面的括号获得任何片(和数组)的一些项

```
week[4:6] // This will return ["Friday", "Saturday"] 
```

Enter fullscreen mode Exit fullscreen mode

要知道任何切片中元素的数量，请使用函数 len()

```
numberOfDays := len(week) 
```

Enter fullscreen mode Exit fullscreen mode

## 4。Go 中的地图

如果您需要使用键/值对，那么您需要使用映射。

例如，如果您需要在 Go 中复制这个 PHP 数组:

```
<?php 
    $products = [
    "Cap" => "Red", 
    "T-Shirt" => "Blue"
    ]; 
?> 
```

Enter fullscreen mode Exit fullscreen mode

您需要定义一个键类型字符串和值类型字符串的映射。您可以使用其中一种方式:

```
products := make(map[string]string) 
products := map[string]string{}
products := map[string]string{ 
    "Cap":     "Red", 
    "T-Shirt": "Red",
} 
```

Enter fullscreen mode Exit fullscreen mode

因为现在我们有了键/值对，所以您可以在不使用 append:
的情况下追加元素

```
products["Pants"] = "Blue" 
```

Enter fullscreen mode Exit fullscreen mode

## 5。在 Go 中迭代数组、切片和地图

最常见的方式是使用`range`

```
for key,value := range products {
    fmt.Printf("Key: %s Value: %s\n", key, value)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 6。不同类型的子阵列

如果我需要一个数组的数组，也有不同类型的变量，就像我在 PHP 上做的那样，该怎么办？

你可以创建“地图的地图”。例如，假设您需要在 Go:
中有这个 PHP 数组

```
<?php 
    $products = [
    "Cap" => [
        "Color" => "Red",
        "Size" => "XL"
    ],
    "T-Shirt" => [
            "Color" => "Blue",
            "Size" => "XS"
        ]
    ];
?> 
```

Enter fullscreen mode Exit fullscreen mode

您可以这样创建这样一个地图:

```
products := make(map[string]map[string]string) 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要在你的子数组中有不同类型的变量，你需要使用[结构](https://gobyexample.com/structs) :
来定义新的变量类型

```
type Property struct {
  Color string
  Size string
  Price int
}

products := make(map[string]Property)

products["Cap"] = Property{
        Color: "Red",
        Size: "XL", 
        Price: 30,
} 
```

Enter fullscreen mode Exit fullscreen mode

那都是乡亲们！；)