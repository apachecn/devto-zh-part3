# Ruby/Rails:动态定义方法的记忆化

> 原文：<https://dev.to/ritikesh/rubyrails--memoization-with-dynamically-defined-methods-50p2>

Ruby(以及 Rails)非常神奇，我相信大多数从事 ruby 工作的人或者用这种语言编写过任何代码的人都会同意我的观点。

[![Mind=Blown!](img/288de40d7e711b7010ca696ef9838a23.png)](https://i.giphy.com/media/xULW8y5CDynhoSOwVy/giphy.gif)

Ruby 允许您做的最常见的技巧之一，也是几乎在 Rails 中随处使用的，就是动态定义方法的能力。你可以简单地这样做，你猜对了，在一个类中调用`define_method`。

```
class User < ActiveRecord::Base

    scope :active, -> { where(active: true) }
    scope :visible, -> { where(visible: true) }
    scope :deleted, -> { where(deleted: true) }

    FILTERS = %w[active visible deleted]

    FILTERS.each { |filter|
        define_method filter do
            self.class.send(filter)
        end 
    }
end 
```

既然我们已经看到了`define_method`能做什么，让我们继续看一些行话。[内存化](https://en.wikipedia.org/wiki/Memoization)一般指在内存中缓存复杂的计算或网络调用。记忆化在大多数现实世界的应用程序中是一种相当常见的做法，也是日常生活中如何在 rails 中使用它的一个简单例子:

```
 def get_users
        @users = User.where(conditions...).all
    end

    def get_users_memoized
        @users ||= User.where(conditions...).all
    end 
```

在一个理想的生产应用程序中，你也可以使用分页，但是现在我想保持简单。假设您多次调用方法`get_users`作为呈现页面的一部分，每次调用该方法时，它都会查询数据库并获取用户列表，而多次调用`memoized_results`时，只有第一次数据库会被命中，查询结果会缓存在实例变量`@users`中。

回到前面的 dynamic_method 定义，您需要一个 hack 来应用相同的记忆技术，因为您不能使用一个静态变量来记忆多个不同的方法。下面的片段演示了我们如何使用元编程(更多 ruby 魔法；))要克服这个限制:

```
# memoize db/network responses in instance variable dynamically
def memoize_results(key)
  return instance_variable_get(key) if instance_variable_defined?(key)
  instance_variable_set key, yield  
end

# usage
FILTERS.each { |filter|
    define_method filter do
        memoize_results("@#{filter}_users") do
            self.class.send(filter)
        end
    end 
} 
```