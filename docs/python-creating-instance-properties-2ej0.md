# Python:创建实例属性

> 原文：<https://dev.to/mattconway1984/python-creating-instance-properties-2ej0>

开发人员使用 Python @property 描述符作为创建特殊类属性的简洁方法。如果你不知道 Python 描述符协议，你应该仔细阅读它，有很多很好的资源可以描述它是如何工作的(尤其是官方 Python 文档)。本文的目标是介绍@property 描述符的一种稍微“高级”的用法，它允许开发人员创建从 Python 属性类派生的实例属性。

请记住，Python 描述符作用于类，而不是实例！因此，包含“Python 描述符属性”的特定 Python 类的所有实例将包含共享相同值的相同类属性(也称为属性)。换句话说，当您为一个衍生自@property 描述符的类定义属性时，您就是在定义一个类变量。

下面的代码片段定义了一个(最简单的)Python 描述符类:

```
class MyDescriptor:

    def __init__(self, value):
        self._value = value

    def __get__(self, owner_instance, owner_type):
        return self._value

    def __set__(self, owner_instance, value):
        self._value = value 
```

您可能认为这个简单描述符的用法类似于:

```
class MyClass:

    bar = MyDescriptor("bar")

    def __init__(self):
        self.foo = MyDescriptor("foo") 
```

让我们从类变量`MyClass.bar` :
开始

```
first_instance = MyClass()
second_instance = MyClass()

print(f"first_instance.bar = {first_instance.bar}")
# prints "bar" 
first_instance.bar = "bar new"

print(f"second_instance.bar = {first_instance.bar}")
# prints "bar new" 
```

这正是我们所期望的——考虑到`MyClass.bar`是在类上定义的(所以所有实例共享该属性的相同值)。

但是，查看实例属性`MyClass.foo`可能不会产生预期的结果，例如:

```
first_instance = MyClass()
second_instance = MyClass()

print(f"first_instance.foo = {first_instance.foo}")
# prints "<__main__.MyDescriptor object at 0x7f87492899e8>" 
print(f"second_instance.foo = {second_instance.foo}")
# prints "<__main__.MyDescriptor object at 0x7f87492897a9>" 
```

注意:您可能希望看到描述符`MyClass.foo`保存的值是打印的值。然而——正如我之前提到的——Python 描述符作用于类而不是实例！那么描述符协议是如何获取/设置这个值的呢？嗯，那很简单...它调用描述符类中定义的`__get__`和`__set__`方法！有了这些知识，您可以简单地自己“手动”实现这个功能。怎么会？通过创建自定义基类:

```
class Base:
    """
    A base class
    """

    def __setattr__(self, attr, value):
        try:
            # Try invoking the descriptor protocol __set__ "manually"
            got_attr = super().__getattribute__(attr)
            got_attr.__set__(self, value)
        except AttributeError:
            # Attribute is not a descriptor, just set it:
            super().__setattr__(attr, value)

    def __getattribute__(self, attr):
        # If the attribute does not exist, super().__getattribute__()
        # will raise an AttributeError
        got_attr = super().__getattribute__(attr)
        try:
            # Try "manually" invoking the descriptor protocol __get__()
            return got_attr.__get__(self, type(self))
        except AttributeError:
            # Attribute is not a descriptor, just return it:
            return got_attr 
```

该基类将覆盖该类的标准`__setattr__`和`__getattribute__`方法，并尝试手动调用描述符协议来获取/设置属性值，如果失败，它将通过让超类(在本例中为`object`)处理请求(正常)来将该属性视为“正常”属性。

最后，需要修改`MyClass`来继承`Base` :

```
class MyClass(Base):

    bar = MyDescriptor("bar")

    def __init__(self):
        self.foo = MyDescriptor("foo") 
```

并使用该类创建一些实例并获取实例所拥有的描述符属性:

```
first_instance = MyClass()
second_instance = MyClass()

print(f"first_instance.foo = {first_instance.foo}")
# prints "foo" 
print(f"second_instance.foo = {second_instance.foo}")
# prints "foo" 
first_instance.foo = "foo new"

print(f"first_instance.foo = {first_instance.foo}")
# prints "foo new" 
print(f"second_instance.foo = {second_instance.foo}")
# prints "foo" 
```

现在你知道了！如果您希望 Python 描述符属性在 Python 实例上工作，而不仅仅是在类上工作，这是一个简洁而简单的解决方案。