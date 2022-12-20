# 使用 Que 而不是 Sidekiq

> 原文：<https://dev.to/zrail/using-que-instead-of-sidekiq-19gi>

一个我搁置了很久的项目是我自己的营销自动化工具。当然，这并不是说像 Drip 或 ConvertKit 这样的现有工具不够用。
他们做工作，而且做得很好。

然而，我喜欢拥有自己的基础设施，在滴滴改变方向并提高价格后，我发现自己的邮件列表没有了归宿。我想，为什么不是现在呢？

任何广播电子邮件系统的一个重要组成部分是**扇出**，在这里你把你想要发送的信息和应该接收它的人的列表合并在一起。
扇出最简单的方法是循环遍历接收者列表，并为每个接收者排队一个作业:

```
Contact.not_opted_out.each do |contact|
  BroadcastMessageDeliver.perform_async(contact.id, the_message.id)
end 
```

这很简单，而且效果很好。但是，并不是超级高效。我们可以做得更好。

如果我们使用 Sidekiq，我们可以使用`push_bulk` :

```
Contact.not_opted_out.find_in_batches do |batch|
  Sidekiq::Client.push_bulk(
    class: 'BroadcastMessageDeliver', 
    args: batch.map { |c| [c.id, the_message.id] }
  )
end 
```

`find_in_batches`调用是一个内置的 ActiveRecord 方法，它将批量提供范围内的所有记录，这只是一个 ActiveRecord 对象的数组。
`Sidekiq::Client.push_bulk`消除了原始版本所做的绝大多数 Redis 往返，因为它在一个 Redis 调用中推送整个批处理。

不过，我们还可以做得更好。我们可以使用 [Que](https://github.com/chanks/que) 来代替 Sidekiq。
Que 是一个类似 Sidekiq 的后台处理系统，它将作业保存在 PostgreSQL 表中，而不是 Redis 列表中。
它使用 PostgreSQL 的原生`listen/notify`系统使作业基本上即时启动，而不是像`DelayedJob`那样轮询。

与使用两个数据存储的系统相比，使用数据库作为队列有很多优点。特别是，ACID 保证和原子备份对我来说非常重要，因为我自己在运行这些。运动部件越少越好。

你可以做的另一件事是将**直接插入到`que_jobs`表中** :

```
ActiveRecord::Base.connection.execute(%Q{
  INSERT INTO que_jobs (job_class, args)
  SELECT
    'BroadcastMessageDeliver' as job_class,
    jsonb_build_array(#{the_message.id}, x.id) as args
  FROM
    (#{Contact.not_opted_out.select(:id).to_sql}) x
}) 
```

`que_jobs`表只是一个数据库表，这意味着您可以随意插入。
例如，`Que::Job.enqueue`只是创建一个记录并保存它，它根本没有使用任何 ActiveRecord 钩子。

通过让数据库完成所有工作，我们可以消除几乎所有的往返和应用程序级循环。

基准(本地 Redis 和本地 PostgreSQL，5000 条记录):

*   Sidekiq 循环:1.9 秒
*   Sidekiq 批次:0.3 秒
*   **Que 直接插入:0.7 秒**

等待...那是...慢一点？

我和你一样惊讶，但事实证明有一个很好的理由。Que 对传入的数据执行一系列检查约束，以确保它是一致的并准备好运行。这里是它检查的所有东西:

```
Check constraints:
    "error_length"
    "job_class_length"
    "queue_length"
    "valid_args"
    "valid_data" 
```

`valid_data`特别是对输入的`json`数据进行一些代价昂贵的操作。

所以我想这里的教训是永远验证你的假设。我认为取消往返会使事情变得更快，但是由于其他的限制和验证，它实际上会更慢。

尽管如此，它还是比简单的版本快得多(老实说，简单的版本还是不错的)，我的营销系统获得了所有数据库内队列的好处，我觉得它很有美感。我想我会留着它。