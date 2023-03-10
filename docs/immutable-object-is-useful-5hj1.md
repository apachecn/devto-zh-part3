# 不可变对象是有用的

> 原文：<https://dev.to/chenge/immutable-object-is-useful-5hj1>

从我看过的一些谈话视频来看这种风格的代码，让我们从一个简单的例子开始:

```
class Fish
  def initialize(age)
    @age = age
  end

  def say()
    "My age is #{@age}"
  end
end

fish = Fish.new(3)
fish.say() #=> My age is 3 
```

这被称为不可变或功能对象。它是坚固的，一旦创造出来就不会被打破。

那么如何改变样本中的年龄呢？

```
class Fish
  #added
  def with_age(age)
    Fish.new(age)
  end
end

fish.with_age(5).say() #=>My age is 5 
```

这种代码风格既有趣又有用。它会减少对象的变化。

你有什么想法？