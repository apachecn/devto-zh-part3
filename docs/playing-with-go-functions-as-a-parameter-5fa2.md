# 玩 Go:作为一个参数

> 原文：<https://dev.to/flrnd/playing-with-go-functions-as-a-parameter-5fa2>

梦幻封面图片感谢[*golang.org*T3】](https://golang.org/doc/gopher/)

当我们学习编码时，有几件事对初学者来说非常可怕。其中之一是将函数作为参数传递给其他函数。

> 函数是 Go 中的*第一类值*:和其他值一样，函数值也有类型，可以赋给变量，也可以传递给函数或者从函数返回。函数值可以像其他函数一样被调用。- [围棋编程语言，艾伦 A. A .多诺万，布莱恩 w .克尼根](https://www.goodreads.com/book/show/25080953-the-go-programming-language)

你有没有想过为什么我们首先要传递函数？如果你是一个有经验的工程师，你可能已经知道答案。关于*如何*将函数作为参数传递给其他函数，有很多好的读物和教程，但是如果将`why`加入公式，结果就有点匮乏了。

最完美的例子是 Map()函数

```
func Map(n []int, f func(x int) int) []int {
   result := make([]int, len(n))
    for i, v := range n {
        result[i] = f(v)
    }
    return result
} 
```

Enter fullscreen mode Exit fullscreen mode

同一个函数我们可以有**不同的行为**只需改变回调函数！让我们看另一个例子: