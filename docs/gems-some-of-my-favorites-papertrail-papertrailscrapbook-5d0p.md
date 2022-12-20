# 宝石，我的一些最爱

> 原文：<https://dev.to/hint/gems-some-of-my-favorites-papertrail-papertrailscrapbook-5d0p>

*最初发布于 [Hint 的博客](https://hint.io/blog/paper-trail-scrapbook)。*

这是一系列介绍各种 Ruby gemss 的博客文章中的第一篇，我们发现这些 Ruby gem 很有用，并且经常使用，或者已经在我们的项目中使用了很多年。

今天我将介绍两个相关的瑰宝:PaperTrail 和 PaperTrailScrapbook。这些 gem 有助于保持对 Rails 应用程序数据库中被跟踪表的行所做更改的可审计跟踪。

* * *

## 纸质文件

### 概述

[PaperTrail gem](https://github.com/airblade/paper_trail) 允许你跟踪你的 Rails 模型随时间的变化。每当数据库中被跟踪的记录被更改时，它都会在一个`PaperTrail::Version`表中创建一个相应的条目。它通过绑定到 ActiveRecord 回调链并在创建、更新或销毁记录时存储新版本来实现这一点。

### 设置

要开始使用 PaperTrail，您需要遵循项目 GitHub 页面上的自述文件中概述的步骤，这些步骤将指导您完成将 gem 添加到 gem 文件、捆绑，然后生成并运行创建`PaperTrail::Version`表的迁移的过程。这个表将被用来为您的模型的每个“版本”存储一个条目。

### 使用

一旦你安装好宝石，你就可以开始使用它了。要开始跟踪一个模型的版本，您只需要将`has_paper_trail`添加到您的模型文件中(通常在类中靠近顶部):

```
class MyModel < ApplicationRecord
  has_paper_trail
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

既然您的版本已经被跟踪，您将能够进入您的 Rails 控制台并使用 PaperTrail 的 API 查看您的模型的各种版本。

对于一个特定的对象`obj`，您可以使用`obj.versions`来访问其版本的集合。这将显示一个版本列表，每个版本都有这样的结构:

```
#<PaperTrail::Version:0x007fe111b03f70> {
                    :id => 314159,
             :item_type => "Invoice",
               :item_id => 555,
                 :event => "update",
             :whodunnit => "1234",
                :object => "---\nid: 5455\nactor_id: 2183\nsale_id: \namount: !ruby/object:BigDecimal 27:0.1276E3\nauthorization_id: \npaid_on: \ncreated_at: 2018-02-15 17:14:16.718224000 Z\nupdated_at: 2018-02-16 17:14:16.718224000 Z\nstatus: active\n",
            :created_at => Mon, 19 Feb 2019 11:59:49 PST -08:00,
        :object_changes => "---\nsale_id:\n- \n - 123\npaid_on:\n- \n- 2018-02-20 20:39:19.956256107 Z\nauthorization_id:\n- 412\nupdated_at:\n- 2018-02-19 20:39:19.956256107 Z\n- 2018-02-19 19:59:49.270910041 Z\n"
    } 
```

Enter fullscreen mode Exit fullscreen mode

版本的字段如下所示:

| 田 | 描述 |
| --- | --- |
| `id` | 此`PaperTrail::Version`的主键 |
| `item_type` | 被跟踪的记录类型 |
| `item_id` | 被跟踪记录的主键 |
| `event` | 发生的变化的类型 |
| `whodunnit` | 进行更改的人的`id`(您可以在 PaperTrail 配置中设置`whodunnit`的类别，即。`User`、`Actor`等。) |
| `object` | 在更新前状态下更新的对象的序列化版本 |
| `created_at` | 创建此版本的时间 |
| `object_changes` | 版本的`object`字段中显示的应用于对象的变更的序列化列表 |

PaperTrail 还提供了将对象恢复到以前状态的能力。要将一个对象恢复到以前的版本，可以在版本上调用`reify`(意思是让抽象的东西变得更具体或真实)。

例如，使用我们上一个例子中的版本:

```
last_version = object.versions.last  # gets PaperTrail::Version shown above
puts last_version.reify

Invoice 314159 {
                  :id => 314159,
            :actor_id => 2183,
             :sale_id => nil,
              :amount => 27.0,
    :authorization_id => nil,
             :paid_on => nil,
          :created_at => Mon, 19 Feb 2018 11:59:49 PST -08:00,
          :updated_at => Mon, 19 Feb 2018 11:59:49 PST -08:00,
              :status => 'active'
} 
```

Enter fullscreen mode Exit fullscreen mode

`reify`将给你一个对象的实例，它对应于在`object_changes`被应用之前的版本，它匹配版本的`object`字段中的内容。

调用`reify`不会影响对象在数据库中的当前状态，除非您在具体化的对象上调用`save`。这样做将把具体化的版本保存到数据库中，在这个过程中为对象创建一个新版本。

### 告诫

当跟踪模型关联的变化时，PaperTrail 有点不稳定。例如，考虑一个`Invoice`类，其中`has_many :line_items``LineItem``belongs_to :invoice`。如果您有一个有几个相关联的`line_items`的`invoice`，您可以通过调用类似
的代码来删除行项目

```
invoice.line_items = []
invoice.save! 
```

Enter fullscreen mode Exit fullscreen mode

如果您这样做了，并且查看了与`invoice`关联的`line_items`之一的跟踪版本，您会发现从`line_item`中移除`invoice_id`在版本中没有被捕获。

PaperTrail 在项目的 [README](https://github.com/airblade/paper_trail#4b1-known-issues) 中有一些信息提到了这一点，并建议了一些“实验性”的解决方法，但您的收获可能会有所不同。

在我看来，PaperTrail 的 API 有点麻烦，并且没有提供对模型随时间变化的简明摘要。信息肯定是存在的，但是把它提取到一个有意义的、完整的历史中可能是痛苦的。

这就是 PaperTrailScrapbook 的用武之地。

* * *

## PaperTrailScrapbook

### 概述

PaperTrailScrapbook 是由[蒂姆·钱伯斯](https://github.com/tjchambers)在 2017 年构思的。它的目的是为 PaperTrail 跟踪的模型提供一个可读的变更摘要。它通过提供一个简单的接口来获得一个对象的完整历史，或者一个特定的人所做的更改的列表。*免责声明:我是这个项目的贡献者:)*

### 设置

要设置 PaperTrailScrapbook，您只需将`gem 'paper_trail_scrapbook'`添加到您的 Gemfile 并捆绑您的应用程序。你需要做的另一件事是指定你的应用程序的类别。大多数情况下，这将是`User`。我建议为 PaperTrailScrapbook 添加一个初始化器(例如`config/initializers/paper_trail_scrapbook.rb`)。

如果您的 whodunnit 类是`User`，那么您的配置将类似于:

```
PaperTrailScrapbook.configure do |config|
  config.whodunnit_class = User
end 
```

Enter fullscreen mode Exit fullscreen mode

一旦配置就绪，重启应用服务器，就可以开始工作了。

### 使用

PaperTrailScrapbook 目前有两种使用模式，`LifeHistory`和`UserJournal`。每种模式为给定对象提供一个`story`或`whodunnit`。

#### 生活史

`LifeHistory`模块将一个模型实例作为它的参数，并生成该对象随时间变化的列表。

例如，下面对历史的查询:

```
widget = Widget.find(123)

puts PaperTrailScrapbook::LifeHistory.new(widget).story 
```

Enter fullscreen mode Exit fullscreen mode

可以输出下面的故事:

```
On Friday, 08 Dec 2017 at 8:01 AM PST, Fred Flintstone <fred@flinstone.com> created the following Widget info: • name: Tricky Spinner
 • description: • notes: Recommended for ages 3 an up
 • created by: Fred Flintstone[123]
 • pattern: Widget Template[386]
 • price: 12.34
 • status: inactive
 •: 123

On Wednesday, 20 Dec 2017 at 12:53 PM PST, Barney Rubble <barney@rubble.com> updated the following Widget info: • name: Recommended for ages 3 an up -> Recommended for ages 3 and up
 • description: -> A fun spinning widget to keep your active fingers busy!
 • price: 12.34 -> 12.99

On Thursday, 21 Dec 2017 at 12:34 PM PST, Wilma Flintstone <wilma@flinstone.com> updated the following Widget info: • price: 12.99 -> 11.99

On Wednesday, 03 Jan 2018 at 10:24 AM PST, Betty Rubble <betty@rubble.com> updated the following Widget info: • name: Tricky Spinner -> Spinning Trick Widget
 • price: 11.99 -> 9.99
 • status: inactive -> active

On Wednesday, 05 Jan 2018 at 3:24 PM PST, Fred Flintstone <fred@flinstone.com> updated the following Widget info: • name: Spinning Trick Widget -> Spinning Brontosaurus Widget
 • price: 11.99 -> 9.99
 • status: inactive -> active 
```

Enter fullscreen mode Exit fullscreen mode

#### 用户日志

`UserJournal`模块将一个`whodunnit`实例作为其主要参数，并生成该人随时间所做更改的摘要。它还提供了将摘要范围限定为特定类别和/或日期范围的选项。随着应用程序的老化，这可能会非常有用，因为随着时间的推移，一个人所做的更改数量可能会变得非常大。

例如，通过特定的`whodunnit` :
进行搜索

```
fred = Person.find(5)

puts PaperTrailScrapbook::UserJournal.new(fred).story 
```

Enter fullscreen mode Exit fullscreen mode

可以输出以下历史:

```
Between Friday, 08 Dec 2017 at 8:01 AM PST and Wednesday, 05 Jan 2018 at 3:24 PM PST, Fred Flintstone made the following changes: On Friday, 08 Dec 2017 at 8:01 AM PST, created Widget[123]:
 • name: Tricky Spinner
 • description: • notes: Recommended for ages 3 an up
 • created by: Fred Flintstone[123]
 • pattern: Widget Template[386]
 • price: 12.34
 • status: inactive
 •: 123

On Wednesday, 12 Dec 2017 at 2:31 PM PST, updated Wobble[538]:
 • on_hand: 5 -> 12

On Thursday, 21 Dec 2017 at 12:34 PM PST, updated User[5]:
 • favorite saying: Yabba dabba -> Yabba dabba doo!

On Wednesday, 05 Jan 2018 at 3:24 PM PST, updated the following Widget[123]:
 • name: Spinning Trick Widget -> Spinning Brontosaurus Widget
 • price: 11.99 -> 9.99
 • status: inactive -> active 
```

Enter fullscreen mode Exit fullscreen mode

您还可以将更改的范围扩大到特定的类，以专注于您所追求的更改。考虑下面的例子，我们寻找 Fred 对`Widget`类的实例所做的改变:

```
fred = Person.find(5)

puts PaperTrailScrapbook::UserJournal.new(fred, klass: Widget).story 
```

Enter fullscreen mode Exit fullscreen mode

它将输出以下内容:

```
Between Friday, 08 Dec 2017 at 8:01 AM PST and Wednesday, 05 Jan 2018 at 3:24 PM PST, Fred Flintstone made the following changes: On Friday, 08 Dec 2017 at 8:01 AM PST, created Widget[123]:
 • name: Tricky Spinner
 • description: • notes: Recommended for ages 3 an up
 • created by: Fred Flintstone[123]
 • pattern: Widget Template[386]
 • price: 12.34
 • status: inactive
 •: 123

On Wednesday, 05 Jan 2018 at 3:24 PM PST, updated the following Widget[123]:
 • name: Spinning Trick Widget -> Spinning Brontosaurus Widget
 • price: 11.99 -> 9.99
 • status: inactive -> active 
```

Enter fullscreen mode Exit fullscreen mode

使用`start`和`end`参数:
，历史也可以被限制在一个特定的时间段

```
fred = Person.find(5)

puts PaperTrailScrapbook::UserJournal.new(fred, klass: Widget, 
                                                start: Date.parse('2018-01-01'), 
                                                end: Time.zone.now).story 
```

Enter fullscreen mode Exit fullscreen mode

哪个会输出这个:

```
Between Monday, 01 Jan 2018 at 12:00 AM +00:00 and Friday, 23 Feb 2018 at 11:05 AM PST, Fred Flintstone made the following changes: On Wednesday, 05 Jan 2018 at 3:24 PM PST, updated the following Widget[123]:
 • name: Spinning Trick Widget -> Spinning Brontosaurus Widget
 • price: 11.99 -> 9.99
 • status: inactive -> active 
```

Enter fullscreen mode Exit fullscreen mode

### 定制

如果您的系统允许删除您的 whodunnit 类的实例，您可能会遇到某个版本的 whodunnit 不再存在于您的数据库中的情况。通过在配置文件中为无效的 whodunnits 提供一个过程，您可以为该场景定制 PaperTrailScrapbook 的行为，如下所示:

```
 config.invalid_whodunnit = proc { |id| "** DELETED: #{id}**"} 
```

Enter fullscreen mode Exit fullscreen mode

### 告诫

该项目仍处于起步阶段，因此您可能会偶尔遇到对您的应用程序不理想的怪癖或行为。如果你这样做，我会考虑提交一个问题，甚至拉请求。任何贡献肯定是欢迎和赞赏的。

也就是说，我想提到一些已知的行为。

如果您的项目有几个大表，并且一个人一直是活动的，那么查询这个人所做的更改会花费很长时间。如果可以，按类或时间段划分范围将极大地提高性能。

另一个需要改进的地方是序列化字段的表示。

假设您有一个带有序列化散列字段的`Container`类，称为`capacity`。如果您要用下面的散列填充`capacity`字段:

```
{
  "weight" => {
    "min" => "25",
    "max" => "50"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并在您的对象实例上运行历史，您会看到类似于`capacity` :
上的变化

```
• capacity: --- !ruby/hash:ActiveSupport::HashWithIndifferentAccess
weight: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
min: '25'
max: '50'
added 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果您更新该值以包括`height`限制，则散列为:

```
{
  "weight" => {
    "min" => "20",
    "max" => "40"
  },
  "height" => {
    "min" => "55",
    "max" => "65"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

重新运行历史，您会看到这样的变化:

```
• capacity: --- !ruby/hash-with-ivars:ActionController::Parameters
elements:
weight: !ruby/hash-with-ivars:ActionController::Parameters
elements:
min: '25'
max: '50'
ivars:
:@permitted: false
ivars:
:@permitted: false
-> --- !ruby/hash:ActiveSupport::HashWithIndifferentAccess
weight: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
min: '20'
max: '40'
height: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
min: '55'
max: '65' 
```

Enter fullscreen mode Exit fullscreen mode

所有的信息都在那里，但是正如您所看到的，随着哈希值的增长，将您的头脑包围起来会变得有点笨拙。序列化数组面临类似的挑战，但是显示的数据量远没有那么详细。

## 总结

这两个 gem 提供了一种跟踪应用程序数据变更的好方法。

`PaperTrail`是一个成熟的 gem，在生产环境中被许多应用程序使用。

`PaperTrailScrapbook`对于跟踪随着时间的推移所做的改变非常有帮助。它为观察人们如何使用您的应用程序以及他们对数据进行的各种更改提供了有用的工具。随着时间的推移，我很高兴看到这个项目继续发展壮大。

## 下次

在本系列的下一篇文章中，我们将看看便捷的[proctor gem](https://github.com/snusnu/procto)，它提供了一种将“您的 ruby 对象转变为方法对象”的绝佳方式。