# 在 Crystal 中返回类型安全和非零失败？

> 原文：<https://dev.to/mjb2kmn/returning-type-safe--non-nil-failure-in-crystal-n01>

我对 Crystal 的设计有疑问，想听听其他人是如何实现类似功能的。

`User`类有一个基于某个属性值获取数据并返回实例的方法。

```
class User
  def self.get_by_login(login : String)
    obj = DB.irrelevant_method(login)
    return obj
  end
end 
```

*(我使用了多余的 return 语句来更好地说明意图)*

当数据库找不到要返回的记录时，问题就出现了。我不想返回联合类型，如`(User|Nil)`或`(User|Bool)`。我的想法是返回一个用户实例来满足返回类型，将特定的错误添加到一个现有的`errors`数组中，我已经在创建端使用该数组进行验证。

```
class User
  def self.get_by_login(login : String)
    user = User.new
    begin
      user = DB.irrelevant_method(login)
    rescue ex
      user.errors << ex.to_s
    end
    return user
  end
end 
```

这就让调用者检查返回的对象是有效的还是有错误，然后才知道如何处理它。

对 Crystal 中的这种方法有什么想法或担心吗？我以前在其他语言中使用过这种模式，它确实有效，但是我从来没有真正觉得它很棒。我很好奇其他人是如何在 Crystal 或其他强类型语言中解决这个或类似问题的。