# 增量开发的优点

> 原文：<https://dev.to/dmerejkowsky/the-virtue-of-incremental-development-1670>

*原载于[我的博客](https://dmerej.info/blog/post/the-virtue-of-incremental-development/)T3。*

今天的挑战是:你能写一个命令行工具来转换不同的度量单位吗？

例如，您可以输入“3 英里米”，得到“4828.03”。

上周末，我向我的 Python 学生提交了这个挑战，要求他们从头开始编写代码。 <sup id="fnref1">[1](#fn1)</sup>

一小时后，奇迹般的事情发生了，这是我从未预料到的。

但是让我告诉你全部的故事。

# 入门

我告诉学生们，他们可以从编写一些“探索性代码”开始。

“只要硬编码你需要的任何东西，并把所有东西都保存在`main()`函数中”，我说。

经过几次讨论，我们同意只编写将公里转换成英里的代码，并且我们将从命令行读取这些值。

下面是我们想出来的:

```
import sys

def main():
    kilometers = float(sys.argv)
    miles = kilometers / 1.609
    print(f"{.2f}", miles)

if __name__ == " __main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

然后我指出代码不是通用的。的确，“公里”、“英里”和“1.609”在那里是硬编码的。

# 命名新功能

学生们知道有一个三参数的函数等着他们去写。所以我们去了绘图板，过了一会儿，我们决定有一个叫做`convert(value, unit_in, unit_out)`的函数。

注意，我们没有*而不是*对函数的*主体*做任何假设。我们只是想看看`main()`如何变得更通用，我们仍然被允许硬编码部分代码:

```
def convert(value, unit_in, unit_out):
    coefficient = 1 / 1.609
    result = value * coefficient
    return result

def main():
    value = float(sys.argv[1])
    unit_in = sys.argv[2]
    unit_out = sys.argv[3]

    result = convert(value, unit_in, unit_out)
    print(f"{.2f}", result) 
```

Enter fullscreen mode Exit fullscreen mode

一些注意事项:

*   `main()`函数现在完全是*通用的*，我们可能不需要改变它。
*   `convert`函数的签名几乎决定了命令行语法:

```
def convert(value, unit_in, unit_out):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

```
# Usage: convert.py value unit_in unit_out
$ python3 convert.py 2 meters miles 
```

Enter fullscreen mode Exit fullscreen mode

# 计算系数

现在是时候去掉硬编码的系数了。这一次找到函数名更容易了:

```
def get_coefficient(unit_in, unit_out):
   ... 
```

Enter fullscreen mode Exit fullscreen mode

然后我们试图找出如何实现它。我们知道我们将需要一本字典，但是它的结构是未知的。

“从头开始”，我说。“让我们写下字典应该是什么样子”。

下面是我们的第一次尝试:

```
units = {
   "km": { "miles": 1/1.609, "meters": 1/1000, ....},
   "yards": { "miles": 1/1760, "meters": ..., "km": ...}
   ...
} 
```

Enter fullscreen mode Exit fullscreen mode

“这不行”，我说。“看看如果我们添加一个新的度量单位，比如`feet`，会发生什么”。

我们将不得不:

*   向`units`字典中添加一个新的“英尺”键，
*   计算从`feet`转换到所有其他单位的所有系数，
*   并为所有其他字典添加一个`feet`键

一定有更好的方法！

在一个短暂的头脑风暴会议后，我们决定将自己限制在*距离*测量，并且*总是首先转换成国际单位制*。

所以我们画出了`units`字典的新结构:

```
# Coefficients convert from "meters" distances = {
    "km": 1/1000,
    "yards": 1.094,
    "miles": 1/1609,
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们想到了算法。我们发现了三种可能性:

*   如果我们想将米转换成*，我们只需在字典中查找系数*
*   如果我们想将*转换成米*，我们可以在字典中查找系数并返回它的倒数
*   否则，我们结合上面的两个过程，返回两个系数的乘积。

“这看起来不错”，我说。“让我们尝试实现该算法，但只是针对第一种情况，看看会发生什么”。

# 测试算法

我向我的学生展示了他们如何使用 Python 的解释器来检查 get_coefficient()函数是否正常工作。

我们很快成功地启动了第一个案例:

```
def get_coefficient(unit_in, unit_out):
    # FIX ME: only works with distances for now
    # Coefficients to convert from "meters"
    distances = {
        "km": 1/1000,
        "yards": 1.094,
        "miles": 1/1609,
    }
    if unit_in == "m":
        return distances[unit_out]

>>> import conversion
>>> conversion.get_coefficient("m", "km")
0.001
>>> conversion.get_coefficient("m", "yards")
1.094 
```

Enter fullscreen mode Exit fullscreen mode

“酷，这很有效”，我说。"让我们看看当输入值不是以米为单位时会发生什么:"

```
def get_coefficient(unit_in, unit_out):
    # FIX ME: only works with distances for now
    # Coefficients to convert from "meters"
    distances = {
        "km": 1/1000,
        "yards": 1.094,
        "miles": 1/1609,
    }
    if unit_in == "m":
        return distances[unit_out]
    else:
        reciprocal_coefficient = 1 / distances[unit_in]
        return reciprocal_coefficient * distances[unit_out]

>>> import conversion
>>> conversion.get_coefficient("miles", "yards")
1760 
```

Enter fullscreen mode Exit fullscreen mode

“看看这些代码是多么易读”，我说。“我们有一个名为`reciprocal_coefficient`的值，我们通过调用 1 而得到它。这不是很好吗？”。

# 奇迹

然后我指出，return 后面的[‘else’是不必要的。](https://dmerej.info/blog/post/else-after-return-yea-or-nay/) 

```
def get_coefficient(unit_in, unit_out):
    # FIX ME: only works with distances for now
    # Coefficients to convert from "meters"
    distances = {
        "km": 1/1000,
        "yards": 1.094,
        "miles": 1/1609,
    }
    if unit_in == "m":
        return distances[unit_out]
    reciprocal_coefficient = 1 / distances[unit_in]
    return reciprocal_coefficient * distances[unit_out] 
```

Enter fullscreen mode Exit fullscreen mode

然后就发生了。“嘿”，一个学生说，“如果我们用`1`作为值在距离字典中添加米会怎么样？我们也可以去掉第一个`if`。

“我们开始吧”，我说:

```
def get_coefficient(unit_in, unit_out):
    # FIX ME: only works with distances for now
    distances = {
        "m": 1,
        "km": 1/1000,
        "yards": 1.094,
        "miles": 1/1609,
    }
    reciprocal_coefficient = 1 / distances[unit_in]
    return reciprocal_coefficient * distances[unit_out]

>>> import conversion
>>> conversion.get_coefficient("m", "m")
1
>>> conversion.get_coefficient("km", "m")
1000
>>> conversion.get_coefficient("m", "yards")
1760 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是可行的。当`meters`为`unit_in`或`unit_out`时，所有运算都将涉及到乘以或除以 1。

这真是一个惊喜，原因有几个:

*   第一，在开始研讨会之前，当我独自思考这个问题时，我非常确定我需要一个更复杂的数据结构。
*   第二，一个学生拒绝相信代码会工作，即使在解释器中看到了它的运行；)
*   三、我们杀了一个评论:)

# 吸取教训

我们发现了一个漂亮的算法和一个不错的数据结构，不是通过试图一次解决*所有的事情*，而是通过慢慢构建越来越多的通用代码，一个接一个地去除硬编码的值，并仔细考虑命名。

我希望您发现这种方法很有用，并且我强烈建议您下次实现新特性时尝试使用它。

干杯！

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  在我的 Python 课程中，我使用了 [mob 编程](https://en.wikipedia.org/wiki/Mob_programming)。效果真的很好。 [↩](#fnref1)