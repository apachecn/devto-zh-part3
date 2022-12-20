# 如何中断 Rails 内置活动记录的验证

> 原文：<https://dev.to/arjunrajkumar/how-to-break-rails-built-in-active-records-validations-4p42>

这篇文章最初出现在 Arjun Rajkumar 的博客上。

在过去的一周里，我遇到了一个错误，这个错误是因为 Rails 的内置活动记录验证检查器失败了。

我已经在我的数据库里建立了这个 has_many 关联——一个`Website`有多个`pages`

```
 class Website < ApplicationRecord
  has_many :pages, -> { order("created_at DESC")} , dependent: :destroy
end

class Page < ActiveRecord::Base
  belongs_to :website, touch: true, counter_cache: true
end 
```

每个`page`都有一个网站范围内的唯一 URL。这意味着一个网站下的页面不应该有相同的网址。

我使用 Sidekiq workers 在后台抓取一个网站，获取所有的页面链接，并将这些链接作为页面添加到网站中。Sidekiq 是多线程的——可以同时运行数千个任务。这些作业中的每一个都在创建一个页面，将被爬网的链接作为 URL。因此，如果它在网站上发现两个相同的链接，可能会发生两个不同的作业同时创建两个具有相同 URL 值的页面。

这正是所发生的事情。

以下是我尝试过的几件事，并没有阻止 Sidekiq 在同一个网站下创建重复页面。

##### 这些都没用。

```
 #Active Record's built-in Validation checkers
class Page < ActiveRecord::Base
  validates_uniqueness_of :url, :scope => :website_id
  belongs_to :website, touch: true, counter_cache: true
end

#Transactions
ActiveRecord::Base.transaction do
  website.pages.create(url: new_url) 
end 
```

#### 这个管用！

取而代之的是非常简单的。
直接在数据库中添加唯一索引解决了这个错误。

```
class AddIndexToPageUrls < ActiveRecord::Migration[5.2]
  def change
    add_index :pages, [:website_id, :url], unique: true
  end
end 
```

这 100%的时候都有效。Rails 在他们的文档中对此做了适当的记录，一旦我知道是什么导致了这个错误，我很快就修复了它。但是最初诊断这个错误以理解为什么会发生这种情况需要时间。

希望这对以后的某个人有帮助。