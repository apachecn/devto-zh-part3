# 10 分钟火车酒吧/Sub

> 原文：<https://dev.to/zorbash/the-10-minute-rails-pubsub-22af>

这一次，我们将尝试一种快速构建 Rails
应用程序的方法，使用发布/订阅来代替模型回调。

## 回调怎么了

Rails 活动记录模型很容易变得臃肿，毕竟这是大多数业务逻辑倾向于存在的地方。Rails 应用中最常见的技术债务来源之一是[回调](https://guides.rubyonrails.org/active_record_callbacks.html)。模型变成了[神物](https://en.wikipedia.org/wiki/God_object)
，依赖于其他模型、邮件甚至第三方服务。

当谈到重构这种耦合时，我通常建议
提取无状态函数的所有回调，这些函数可以组成
形式的管道。人们可以使用[干交易](https://dry-rb.org/gems/dry-transaction/)来实现。
我对这种可组合建筑的热爱让我创作了《长生不老药》的[作品](https://github.com/zorbash/opus)。

我也很自豪回调在[部门](https://hexdocs.pm/ecto/1.1.0/Ecto.Model.Callbacks.html)被否决🎉。

## 关于发布/订阅

这篇文章重点讨论的解决方案是[发布/订阅](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)。模型将发布关于数据库更新的
事件。数据库记录被创建/
更新/销毁，然后订户做了一些事情，或者忽略了该事件。

## 输入 ActiveSupport::通知

我们将在
[活动支持::通知](https://api.rubyonrails.org/classes/ActiveSupport/Notifications.html)的基础上，为这十分钟的实施打下基础。最初
是作为 Rails 的工具 API 引入的，但是没有什么
阻止我们将它用于定制事件。

[![they_should](img/08dbe8405eace6b49bfeec0f023076ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BxzVmWeg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zorbash.cimg/posts/the_10_minute_rails_pubsub/they_should.jpg)

关于[active support::Notifications](https://api.rubyonrails.org/classes/ActiveSupport/Notifications.html)的一些事实。

*   它基本上是一个线程安全的队列
*   事件是同步的
*   事件是进程本地的
*   使用起来很简单😎

## 代码

在本实验中，我们将涵盖以下场景:

```
When a User "zorbash" is created
And "zorbash" had been invited by "gandalf"
Then the field signups_count for "gandalf" should increase by 1 
```

首先，我们将创建一个模型关注点，我们可以将它包含到我们的`User`
模型中，以便在每次创建记录时发布事件。

```
# frozen_string_literal: true

module Publishable
  extend ActiveSupport::Concern

  included do
    after_create_commit :publish_create
    after_update_commit :publish_update
    after_destroy_commit :publish_destroy
  end

  class_methods do
    def subscribe(event = :any)
      event_name = event == :any ? /#{table_name}/ : "#{table_name}.#{event}"

      ActiveSupport::Notifications.subscribe(event_name) do |_event_name, **payload|
        yield payload
      end

      self
    end
  end

  private

  def publish_create
    publish(:create)
  end

  def publish_update
    publish(:update)
  end

  def publish_destroy
    publish(:destroy)
  end

  def publish(event)
    event_name = "#{self.class.table_name}.#{event}"

    ActiveSupport::Notifications.publish(event_name, event: event, model: self)
  end
end 
```

那么我们必须将它包含在我们的模型中。

```
# frozen_string_literal: true

class User < ApplicationRecord
  include Publishable # 👈 Added here

  devise :invitable

  # other omitted code
end 
```

让我们实现一个订户。

```
module UserSubscriber
  extend self

  def subscribe
    User.subscribe(:create) do |event|
      event[:model].increment!(:signups_count)
    end
  end
end 
```

最后，我们必须初始化订阅。

```
# File: config/initializers/subscriptions.rb
Rails.application.config.after_initialize do
  UserSubscriber.subscribe
end 
```

### 告诫

添加的侦听器越多，事件在所有侦听器中按顺序处理的速度就越慢。这类似于一个
对象一个接一个地调用所有回调处理方法。

参见:[active _ support/notifications/fanout . Rb](https://github.com/rails/rails/blob/v5.2.2/activesupport/lib/active_support/notifications/fanout.rb#L51)

```
def publish(name, *args)
  listeners_for(name).each { |s| s.publish(name, *args) }
end 
```

它们也不适合用于改变记录的回调，如
`before_validation`或`after_initialize`。

此外，不能保证事件会被成功处理
。哪里会出错，哪里就会出错。更喜欢具有健壮恢复语义的
解决方案。

## 下一步

为了增强灵活性，我们可以将事件推送到 Redis 或 RabbitMQ 或 Kafka。如何根据你的需要选择一个不在本文讨论范围之内。然而，你可以认为自己是幸运的，因为那里有大量的资源
和成熟的库来构建你的事件驱动系统。

### 替代品

著名的公共/次级宝石:

*   [干赛事](https://dry-rb.org/gems/dry-events/)
*   [衣架](https://github.com/krisleech/wisper)

关于其他方便的库和文章，请订阅我的 te after[Ruby&Rails](https://www.tefter.io/zorbash/lists/ruby-rails)列表。