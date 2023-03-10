# 通过编写简单的数学库介绍测试

> 原文：<https://dev.to/abdurrahmaanj/introduction-to-testing-by-writing-a-simple-maths-lib-10in>

总有一天，测试成为一种需要。你不能运输不能正常工作的零件...您测试以确保一切都如预期的那样。你不仅要测试代码，还要测试 GUI、API 和数据库功能。在本帖中，我们将在浅水区航行，让初学者能够开始测试，一劳永逸。

## 我们的数学图书馆

让我们写一个数学库来解决常见的数学问题

*   正方形的面积
*   三角形的面积
*   斜边长度

我选择的名字是 complemaths，因为它补充了标准库

让我们写我们的类

```
class Complemaths:

    def area_square(length_, width_):
        return width_ * length_

    def area_triangle(base_, height_):
        return base_ * height_ * 0.5

    def hypotenuse(side1_, side2_):
        return sqrt(side1_**2 + side2_**2) 
```

*   1 我们没有在这里添加构造函数，因为不需要它。
*   2 此外，side1_**2 也可能是 side1_*side1_
*   3 面积 _ 三角形和斜边返回浮动

让我们利用我们的自由

## 用法

```
print(Complemaths.area_square(2, 2))
print(Complemaths.area_triangle(5, 2))
print(Complemaths.hypotenuse(5, 5)) 
```

发出

```
4
5.0
7.0710678118654755 
```

## 测试概述

没有必要有一个框架。你可以这样做:

```
def testing():
    if(Complemaths.area_square(2, 2) == 4):
        print('test1 ok')
    else:
        print('test1 fail')

testing() 
```

或者使用断言

```
def test_area_square():
    assert Complemaths.area_square(2, 2) == 4, 'should be 4'

test_area_square() 
```

如果一切正常，不显示任何消息，否则停止执行

但是，框架有一些高级功能。幸运的是，受 java 的 Junit 的启发，python 自带了名为 unittesting 的工具

## 我们用 Unittesting 进行测试

```
class ComplemathsTests(unittest.TestCase):
    def test_area_square(self):
        self.assertEqual(Complemaths.area_square(5, 2), 10)

    def test_area_triangle(self):
        self.assertEqual(Complemaths.area_triangle(5, 2), 5.0)

    def test_hypotenuse(self):
        self.assertEqual(Complemaths.hypotenuse(3, 4), 5.0)

if __name__ == '__main__':
    unittest.main() 
```

它给了我们

```
...
----------------------------------------------------------------------
Ran 3 tests in 0.001s

OK 
```

按部分测试被称为单元测试，在开发中使用测试被称为测试驱动开发，tdd。

* * *

马尔科姆·莱特博迪在 Unsplash 上拍摄的照片