# 使用 Postgresql 在用户的本地时区中执行任务

> 原文：<https://dev.to/scottw/using-postgresql-to-perform-tasks-in-a-users-local-time-zone-30jk>

兼职项目的好处之一是给自己一个机会，去发现你已经解决的问题的新的解决方案。

近七年来， [KickoffLabs](https://kickofflabs.com) 可以选择每周或每天向我们的客户发送关于前一天进展的电子邮件。为了保持它的相关性，我们总是尽最大努力在当地时间早上 8 点左右发送。

为此，我们在用户注册时获取他们的时区，然后设置一个 cron 作业每小时执行一次。

我大约七年前编写的实现是这样工作的:

1.  为我们的客户获得一个明确的已知时区列表
2.  循环浏览它们，看看现在的时间是 8 点
3.  在这些时区内查找用户

查看实时代码，我可以看到 95%的代码是在 2011 年 9 月编写的(唯一真正的变化是连接了优秀的 DeadManSnitch 服务，以确保它们被发送)

对于 [IndieTriage](https://indietriage.com) ，我希望有相同的时区感知电子邮件，但有一个额外的转折。我希望用户(最终)能够选择他们希望收到电子邮件的时间。也许他们在一天的早些时候有时间，或者也许在午餐时收到这封邮件是有意义的。

我之前的方法在最后一个需求上失败了，因为现在所有的时区都是有效的。我必须遍历每个订阅者实例，或者至少遍历所有小时(并生成有效时区的列表)。

现在是 2019 年。我们可以做得更好。

ORM 就像 ActiveRecord 一样，让 99%的繁琐工作烟消云散。不幸的是，它们也掩盖了一些出色的内置数据库功能。

我们可以做的是使用 Postgresql 的`In Time Zone`函数编写一些 SQL，并与 date_part 函数配对。

我们最终得到一个类似于这样的查询:

```
select subscribers.id from subscribers
where date_part('hour', (now() AT TIME ZONE subscribers.time_zone)) = subscribers.hour; 
```

然后，我们可以像这样创建一个范围(假设列名为 time_zone 和 hour)

```
scope :current_hour_by_time_zone, -> {
  where(Arel.sql("date_part('hour', now() AT TIME ZONE #{table_name}.time_zone) = #{table_name}.hour"))
} 
```

最后，我们可以把它们放在一个 cron Sidekiq worker:

```
class EnqueueDailyEmailsForSubscribersWorker
  include Sidekiq::Worker

  def perform
    Subscriber.
      select(:id).
      current_hour_by_time_zone.
      confirmed.
      last_emailed(22.hours.ago).
      find_in_batches(batch_size: 1000) do |subscribers|

      Sidekiq::Client.push_bulk(
        "class" => "SendDailyEmailToSubscriberWorker",
        "args" => subscribers.pluck(:id).map {|id| [id]}
      )
    end
  end
end 
```

这个工人每小时执行一次。每次，它都会获取满足必要条件的订户列表，包括他们所在时区的正确时间。