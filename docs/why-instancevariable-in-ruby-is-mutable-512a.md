# 为什么 Ruby 中的类可以变异变量？

> 原文：<https://dev.to/awcodify/why-instancevariable-in-ruby-is-mutable-512a>

```
 class User
  def initialize(attr)
    @attr = attr
  end

  def add_name
    @attr[:name] = 'Foo Bar'
    @attr
  end
end

user = {}

User.new(user).add_name

user

=> {:name=>"Foo Bar"} 
```