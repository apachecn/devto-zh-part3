# Rails:悲观锁定

> 原文：<https://dev.to/nodefiend/rails-pessimistic-locking-45ak>

图片信条:[本杰明·博特尔](https://unsplash.com/@ben182)

## 问题

当不止一个用户试图同时更新数据库中的一个表时会发生什么？你猜对了！竞态条件——作为一个程序员，你一定讨厌这些，也许读完这篇文章后，我们至少可以避免一件导致它们的事情。

## 定义

#### 悲观数据库锁定:

*   当其他人正在阅读时，多个用户将无法阅读

#### optimistc 数据库锁定:

*   多个用户可以同时读取同一个资源，但是如果不止一个用户试图修改数据库，那么我们会阻止它

在乐观锁定中，我们只在更新数据时锁定它。其他请求仍然可以读取主题数据。另一方面，悲观锁定会锁定对记录的所有其他访问。甚至不允许读访问。使用这种类型的锁定，当对对象的第一个请求正在更新时，所有其他请求都必须等待轮到它们。

在本文中，我们将介绍如何在 rails 中处理悲观锁定，这更容易，对于乐观锁定，请查看:

[乐观锁定](https://api.rubyonrails.org/classes/ActiveRecord/Locking/Optimistic.)

## 想象:

想象一个类似的按钮-

```
 def like(id)
    message = Message.find(id)
    message.like_count += 1
    message.save!
  end 
```

如果一个用户按下 like 按钮，而另一个用户同时按下 like 按钮，那么该消息的`like_count`不会上升到 2，只会增加到 1，因为两个用户同时按下从 0 增加到 1。

## `with_lock`

[正式文件](https://api.rubyonrails.org/classes/ActiveRecord/Locking/Pessimistic.html)

with_lock 做了几件事。首先，它启动一个数据库事务。其次，它获得了一个悲观的数据库锁。一旦获得锁，记录就被重新加载到内存中，这样记录上的值就与锁定的数据库行中的值相匹配。该锁将阻止其他人读取或写入该行，任何试图获取锁的人都必须等待锁被释放。

```
 def like(id)
    message = Message.find(id)
    message.with_lock do
      message.like_count += 1
      message.save!
    end
  end 
```

现在，假设我们希望在锁事务发生时锁定这个用户的帐户，这样就不能在这个事务的范围内对用户模型进行任何更新。

我们能做的是调用`account.lock!`

```
 def like(id)
    message = Message.find(id)
    message.with_lock do
      account.lock!
      message.like_count += 1
      message.save!
    end
  end 
```

由于我们已经在一个数据库事务中(第一个锁)，我们不能使用另一个`with_lock`块，因为它本身就是一个事务。我们可以在这个`with_lock`块中调用`.lock!`。

这与 with_lock 方法非常相似，但有两个例外。首先，它只是在调用时获取锁，然后在周围的事务完成时释放锁，而不是在内部管理自己的事务。第二，如果不在事务内部调用，它什么也不做。**重复一遍，不要用锁！在交易之外！除此之外，它将确保与 with_lock 方法相同类型的数据完整性。**

感谢阅读！锁定愉快！

来源:(谢谢！)

[https://www . peterbelak . com/blog/pessimistic-lock-in-rails-by-example/](https://www.peterdebelak.com/blog/pessimistic-locking-in-rails-by-example/)

[http://blog.katpadi.ph/race-condition-and-rails-with_lock/](http://blog.katpadi.ph/race-condition-and-rails-with_lock/)