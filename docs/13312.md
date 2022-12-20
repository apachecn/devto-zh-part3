# TIL:对 ActiveRecord 事务使用不同的数据库连接

> 原文：<https://dev.to/amrrbakry/til-using-different-database-connection-with-activerecord-transactions-345o>

几天前我在工作中发现了一个有趣的问题。我在一个[活动记录事务](https://api.rubyonrails.org/classes/ActiveRecord/Transactions/ClassMethods.html)中编写了一些代码，它更新了数据库中的一些记录，调用了一个外部服务，然后根据该外部服务的响应更新了另一个记录。事情是这样的:

```
def call
  ActiveRecord::Base.transaction do
    record1.update!(some_data)
    external_service_response = Communicator.call!(record1, save_error: true) # raises exception if response is not a success
    record2.update!(external_service_response)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

communicator 类使用`record1`数据构建请求并调用外部服务。它还处理身份验证和响应解析；如果响应成功，它解析并返回它。否则，如果`save_error`标志被设置为`true`，则*用错误响应*更新 db 中的记录，并最终引发异常，导致活动记录事务回滚所有内容。

## 问题

问题是错误响应没有保存到数据库。你知道为什么吗？！因为它在交易内部；因此，当 communicator 类引发异常时，事务回滚所有内容，包括将错误消息保存到数据库的操作。好吧，废话。

## 解

最明显的解决方案，也是您在大多数情况下应该采用的解决方案，是将通信器调用移出事务，并且保存错误操作不会回滚。然而，我做不到，所以我必须做更多的思考和谷歌搜索...

我遇到了另一个巧妙的解决方案，并且肯定学到了一些新东西。它利用了活动记录事务“作用于单个数据库连接”的事实；这基本上意味着，如果我们使用不同的连接——与事务正在使用的连接不同——来更新记录，事务将不会覆盖更新操作，并且在出现异常的情况下也不会回滚。

因此，为了解决我的问题，我需要创建一个新线程(因为每个线程将使用不同的数据库连接)，获得一个连接，并用错误响应更新记录。

## [T1】active record::base . connection _ pool](#activerecordbaseconnectionpool)

为了实现这一切，并正确管理数据库连接，确保它是线程安全的，我们有了 [`ActiveRecord::Base.connection_pool`](https://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/ConnectionPool.html)

从文档中:

> 连接池将线程访问同步到有限数量的数据库连接。基本思想是每个线程从池中检出一个数据库连接，使用该连接，然后再检入该连接。ConnectionPool 是完全线程安全的，只要正确遵循 ConnectionPool 的约定，它将确保一个连接不能被两个线程同时使用。

于是最终解变成:

```
# communicator class
# 
# in case of error response
def save_error_response(record)
  Thread.new do
    ActiveRecord::Base.connection_pool.with_connection do
      record.update(external_service_error_response)
    end
  end.join
end 
```

Enter fullscreen mode Exit fullscreen mode

[`with_connection`](https://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/ConnectionPool.html#method-i-with_connection) 方法将检查出一个连接，让位于块(更新记录)，并在完成后再次检查到池的连接。我们还需要`join`线程，以确保主线程在退出前会等待它完成。

就是这样！现在，错误响应将被保存到数据库中，并且不会在出现异常的情况下回滚，因为它位于不同的线程和与事务不同的连接中。

我希望我对问题和解决方案的解释是清楚的。

这个帖子也发表在[媒体](https://medium.com/@amrrbakry/til-using-different-database-connection-with-activerecord-transactions-adb3c25cc9f1)上。