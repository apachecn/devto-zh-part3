# 用不到 5 分钟的时间理解本征类

> 原文：<https://dev.to/chenge/understanding-the-eigenclass-in-less-than-5-minutes-2l24>

今天我读了[在不到 5 分钟的时间里理解本征类](https://medium.com/rubycademy/understanding-the-eigenclass-in-less-than-5-minutes-dcb8ca223eb4)。它解释得很清楚。

```
class Greeting
  def self.hello
    'hello world!'
  end

  def self.eigenclass
    class << self
      self
    end
  end
end

Greeting.eigenclass      # => #<Class:Greeting>
Greeting.eigenclass.name # => nil

Greeting.singleton_methods                  # => [:hello, :eigenclass]
Greeting.eigenclass.instance_methods(false) # => [:hello, :eigenclass] 
```

```
class << self 
```

这种奇怪的语法意味着特征类上下文。