# 什么是利斯科夫替代原理？

> 原文：<https://dev.to/ellehallal/what-is-the-liskov-substitution-principle-31bj>

<center>

### 最初发布于 [ellehallal.dev](https://ellehallal.dev) 👩🏽‍💻

</center>

* * *

## 原理定义

> *设φ(x)是关于 t 类型的对象 x 的一个可证明的性质。那么φ(y)对于 S 类型的对象 y 应该是真的，其中 S 是 t 的子类型。*

太好了，但这意味着什么🤷🏽‍♀️?简单来说，子类应该能够替代父类，而不会有任何意外的行为。它确保正确使用继承。

* * *

## 坚持里斯科夫替代原理的一个例子

下面是班级`Animal`。当调用`speak`方法时，应该返回一个字符串。该方法没有任何参数。

```
class Animal
    def speak
    ""
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

下面是子类`Cat`和`Dog`。两者都继承自`Animal`，都有一个`speak`方法。

```
class Cat < Animal
    def speak
    "Meow!"
    end
end

class Dog < Animal
    def speak
    "Woof!"
    end
end

cat = Cat.new
dog = Dog.new

cat.speak  # "Meow!"
dog.speak  # "Woof!" 
```

Enter fullscreen mode Exit fullscreen mode

尽管调用`Cat`上的 speak 方法会返回‘喵！’“汪汪！”对于`Dog`，两种情况下都返回一个字符串。此外，不需要任何参数。因此，当使用`Animal`的实例时，这些子类的实例可以被替换。

* * *

## 违反利斯科夫替代原理

下面是类`Jellyfish`，是`Animal`的子类。它的 speak 方法有一个参数名，并返回一个字符串。

尽管该方法返回一个字符串，但调用时需要一个名称作为参数。因为父类的'`speak`方法不需要名称参数，所以`Animal`类的实例不能用`Jellyfish`类的实例替换。

```
class Jellyfish < Animal
    def speak(name)
    "#{name} cannot speak"
    end
end

jellyfish = Jellyfish.new
jellyfish.speak("Jelly")  # "Jelly cannot speak" 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果子类的 speak 方法返回字符串以外的任何内容，这也将违反该原则。

* * *

## 总之

为了遵守 Liskov 替换原则，子类应该能够替换父类，而不会有任何意外的行为。这是为了确保正确使用继承。

* * *

### 有用的资源

*   [利斯科夫替代原理-维基百科](https://en.wikipedia.org/wiki/Liskov_substitution_principle)
*   [回到基础:多态性和 Ruby](https://thoughtbot.com/blog/back-to-basics-polymorphism-and-ruby)