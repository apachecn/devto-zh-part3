# 在 Python 中使用 Getters 和 Setters

> 原文：<https://dev.to/wrrnwng/using-getters-and-setters-in-python-5205>

在编写了面向对象的 Python 一段时间后，现在你可能需要编写一些 getters 和 setters。与 C++不同，Python 中的约定是，如果不需要编写 setter，就直接获取和设置类实例属性。

例如，如果我们有一个拥有`height`和`width`属性和实例方法`area`和`perimeter` :
的`Rectangle`类

```
class Rectangle:
  def __init__(self, height, width):
    self.height = height
    self.width = width

  def area(self):
    return self.height * self.width

  def.perimeter(self):
    return (self.height + self.width) * 2 
```

Enter fullscreen mode Exit fullscreen mode

如果我们从来不需要检查`height`和`width`的值，这就好了。对于像在 Jupyter 笔记本上写实验这样的事情来说，这可能很好，但是如果我们想让这些代码成为像库这样具有更长生命周期的东西的一部分，我们可能希望有一些更健壮的东西。

我们知道一些关于矩形的事情，也许我们应该检查一下。矩形可能只应该有正的高度和宽度，所以我们应该检查一下。它们可能也不应该是零，因为这将使它要么是一个点(高度和宽度都是零)，要么是一条线(高度或宽度都是零)。

例如，我们可以直接访问`height`和`width`，但是我们也可以直接将这些属性更改为一些我们知道无效的值:

```
rect = Rectangle(3, 4) # Rectangle(height=3, width=4) rect.height # 3 rect.width # 4 rect.height = 0 # Should be invalid rect.width = -5 # Should also be invalid 
```

Enter fullscreen mode Exit fullscreen mode

让我们修改代码，使用 Python getters 和 setter，而不改变我们的 API 接口，方法是更新`__init__`方法，用 Python 装饰器`@property`添加 getter 和 setter，用`@height.setter`和`@width.setter`分别设置高度和宽度:

```
class Rectangle:
  def __init__(self, height, width):
    self._height = height
    self._width = width

  @property
  def height(self):
    return self._height

  @property
  def width(self):
    return self._width

  @height.setter
  def height(self, new_height):
    self._height = new_height

  @width.setter
  def width(self, new_width):
    self.width = new_width

... 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止还没有新的功能。我们仍然可以将属性设置为一些无效值，但是我们已经可以看到在哪里可以检查有效值了——在标有`@___.setter`的方法中。

```
 @height.setter
  def height(self, new_height):
    if new_height <= 0:
      return
    self._height = new_height

  @width.setter
  def width(self, new_width):
    if new_width <= 0:
      return
    self.width = new_width 
```

Enter fullscreen mode Exit fullscreen mode

因此，在更新设置器之后，我们将无法将`_height`和`_width`属性设置为无效。但是还有一个问题。我们仍然可以将矩形的`_height`和`_width`初始化为无效值。我们想要做的是在我们的`__init__`方法中也使用 setters。让我们也提出异常，而不是默默地返回:

```
 def __init__(self, height, width):
    self.height = height
    self.width = width
...

  @height.setter
  def height(self, new_height):
    if new_height <= 0:
      raise Exception
    self._height = new_height

  @width.setter
  def width(self, new_width):
    if new_width <= 0:
      raise Exception
    self.width = new_width 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以通过创建一个静态方法来检查高度和宽度值的有效性，从而消除一些重复。使用`@staticmethod`装饰器:
的新静态方法`check_value`的最终代码应该是这样的

```
class Rectangle:
  def __init__(self, height, width):
    self.height = height
    self.width = width

  @property
  def height(self):
    return self._height

  @property
  def width(self):
    return self._width

  @height.setter
  def height(self, new_height):
    Rectangle.check_value(new_height)
    self._height = new_height

  @width.setter
  def width(self, new_width):
    Rectangle.check_value(new_width)
    self._width = new_width

  def area(self):
    return self.height * self.width

  def perimeter(self):
    return (self.height + self.width) * 2

  @staticmethod
  def check_value(value):
    if value <= 0:
      raise Exception 
```

Enter fullscreen mode Exit fullscreen mode

Python 使用 getter 和 setter 的方法是允许您继续编写简单的程序，并且只需在适当的时候使用 getter 和 setter，而无需更改接口。这是一个非常优雅的方法，特别是对于原型设计，不用担心以后会修改太多的代码，当你需要生产代码时，实验会证明自己。

*最初发布于[warrenwong.org](https://warrenwong.org/using-getters-and-setters-in-python/)2019 年 4 月 12 日。*