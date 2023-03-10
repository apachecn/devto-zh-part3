# Go 中的数组和切片

> 原文：<https://dev.to/simplytunde/array-and-slice-in-go-1g8g>

# 阵列

数组是一个复合对象，具有相同类型对象的固定长度。比如围棋；

##### 去吧

```
package main

import "fmt"

func main() {
    //This defines a variable that holds 4 integers
    var a [4]int = [4]int{1,2,3,3}
    //go array index value can be mutated unlike rust
    a[2] = 10
    b := [5]int{1,2,3,4,5}
    fmt.Println(a)
    fmt.Println(b)
    //If you try to assign array of length 5 to a, it errors
    //a := [5]int{7,8,9,10,11} //uncomment this
} 
```

# 切片

切片是一种数据类型，由数组支持，但大小可变。它由基本指向数组特定部分的类型和大小、数据段拥有的数组长度以及从片头开始的底层数组的最大容量组成。

```
package main

import "fmt"

func main() {
    arr := [4]int{1,2,3,4}
    s := arr[1:] //The defaults to arr[1:4] with last one excluded
    //You can also make slice using `make`
    m := make([]int,1,2) //The creates a slice with length 1 but capacity 2.
    fmt.Println(m)
    //Ok, lets just copy one slice to another. This only copies one element because of m length
    copy(m, arr[1:])
    fmt.Println(m)
    fmt.Println(s)
} 
```

你可以定义一个切片文字或者使用 make 函数来指定长度和容量，就像我们在上面看到的那样。

### 空切片

当您定义一个切片而不设置值时，您得到的是一个空切片。没有支持该切片的基础数组。空切片最突出的一点是，它们可以像零长度切片一样工作，这意味着可以像用零长度定义的切片一样对待它们。

```
package main

import "fmt"

func main() {
   //This slice defined below will be treated like an empty slice of type int
   var s []int
   fmt.Println(s)
   //Lets add some int to this null slice; Null because it does not have underlying array
   s = append(s,1,2,3,4)
   fmt.Println(s)
} 
```

```
[]
[1 2 3 4] 
```

您必须小心使用 slice，因为它们可以将内存中的大对象作为人质，即使它们只引用数据的一小部分。