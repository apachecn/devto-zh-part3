# 意外的提交后行为

> 原文：<https://dev.to/jkostolansky/the-unexpected-aftercommit-behaviour-4igf>

今天我学到了一些有趣的东西。新的 Rails 回调`after_create_commit`、`after_update_commit`和`after_destroy_commit`可以以我没想到的方式运行。

`after_commit`回调是 Ruby on Rails 框架中众所周知的一部分。在创建、更新或销毁记录之后，并且在提交相应的数据库事务之后，调用该函数。如果我们想要触发与记录相关联的后台作业，这是要使用的主要方法。

```
class User < ActiveRecord::Base
  after_commit :schedule_welcome_email, on: :create  

  def schedule_welcome_email
    WelcomeEmailJob.perform_later(id)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Ruby on Rails 5 带来了一些新的`after_*_commit`回调。之前是这样的:

```
after_commit :action1, on: :create
after_commit :action2, on: :update
after_commit :action3, on: :destroy 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用:

```
after_create_commit  :action1
after_update_commit  :action2
after_destroy_commit :action3 
```

Enter fullscreen mode Exit fullscreen mode

## 问题

假设我们想在记录被创建或销毁后触发`broadcast`方法。我们可以尝试使用新的回调:

```
class Comment < ActiveRecord::Base
  after_create_commit  :broadcast
  after_destroy_commit :broadcast

  def broadcast
    BroadcastJob.perform_later(id)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错！`after_destroy_commit`按预期工作。然而，由于某种原因，第一个`after_create_commit`从未被触发。但是为什么呢？

## 原因

我们来看看`after_create_commit`方法的源代码:

```
def after_create_commit(*args, &block)
  set_options_for_callbacks!(args, on: :create)
  set_callback(:commit, :after, *args, &block)
end 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这些方法实际上是指定了`:on`选项的旧`after_commit`回调的别名。并且后续的`after_commit`声明会覆盖相同方法的先前声明。这可能会非常令人惊讶！

## 解

要解决这个问题，我们可以使用旧的回调。`:on`选项支持多个生命周期事件的数组，因此解决方案很简单，如下所示:

```
after_commit :broadcast, on: [:create, :destroy] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

原文:[意外的 after_commit 行为](https://www.kostolansky.sk/posts/unexpected-after-commit-behaviour/)