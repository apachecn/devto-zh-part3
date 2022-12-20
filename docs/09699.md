# 我刚从制作部删除了数千张唱片😬

> 原文：<https://dev.to/joemasilotti/i-just-deleted-thousands-of-records-from-production--4bh7>

> 这篇文章最初出现在 Masilotti.com。

我昨天醒来，渴望给我的副业项目 [weTabletop](https://www.wetabletop.com) 添加漂亮的社交分享。当我把几个网址插入 [Twitter 卡验证器](https://cards-dev.twitter.com/validator)时，有几个开始变成 404。奇怪的是，从上周晚些时候开始，在我运行生产转储的开发数据库中，记录似乎很好。

## 健全性检查的时间

发生了一些事情。我是不是从制作部删除了一堆记录？

为了验证，我远程连接到 Heroku Rails 控制台并开始调试。通过比较 dev 中的表计数，我发现`events`表是不同步的。差很多。

我通过将生产中所有记录的 id 与正常的数据库序列(1、2、3、4 等)进行比较，找出了哪些记录丢失了。).我很少真的删除事件，所以模式中的任何空隙都会显示出一些错误。

```
(irb)> ((1..Event.last.id).to_a - (Event.first.id..Event.last.id).to_a).count
=> 2526 
```

我不知何故丢失了 2500 多张唱片。😭

## 备份？什么备份？

理想情况下，可以通过将数据库备份合并到生产中的当前数据集中来解决这个问题。我可以下载一周前的转储文件，找到所有记录，然后上传回 prod。

然而，为了让 Heroku 的费用尽可能低，我没有为支持自动备份的最便宜的 [$50 数据库插件](https://elements.heroku.com/addons/heroku-postgresql)付费。所以我没有自动备份。🤠

幸运的是，我的数据集仍然很小(大约 30k 条记录)，所以我可以不时地将产品投入开发。我最近一次转储是在随机删除开始之前！虽然并不完美，但我想我可以编写一个脚本来手动导出和导入从开发到生产的记录。

## 导出/导入原始 JSON

1.  获取丢失记录的 id(从以前)
2.  将每个记录写入一个文件，如 JSON
3.  将文件和导入脚本上传到生产环境
4.  解析每一行并`#create!`一条新记录

将*整个*记录导出到 JSON(绕过任何定制的序列化器)确保了所有数据都被保留。这包括记录的 ID 和时间戳，这是移动数据时通常不希望携带的东西。

这是你真正想要这些数据的罕见时刻之一。我需要备份看起来就像他们正在恢复的记录。事件#412 应标记为创建于 2 月 14 日，而不是 3 月 18 日(今天)。

### 出口国，发展中运行

```
class EventJSONExporter
  def export
    ids = [1, 2, 3, ...].freeze # pasted in from before

    File.open("db/events.json", "wb") do |file|
      Event.where(id: ids).find_each do |event|
        file.write event.to_json
        file.write "\n"
      end
    end
  end
end 
```

### 导入器任务，在生产中运行

```
namespace :events do
  desc "Import deleted events from JSON file."
  task import_json: :environment do
    saved_event_ids = Set.new
    filename = Rails.root.join "db", "events.json"

    File.open(filename).each_line do |line|
      begin
        event = Event.new(JSON.parse line)
        event.save!
        saved_event_ids << event.id
      rescue JSON::ParserError
        puts "Couldn't parse JSON: #{line}"
      rescue StandardError => e
        puts "Couldn't save event #{line["id"]}: #{e}"
      end
    end

    puts "Imported #{saved_event_ids.count} events:"
    puts saved_event_ids
  end
end 
```

## 根本原因分析

如果记录继续神奇地自行删除，这些都无关紧要。通过对整个代码库的`destroy`和`delete`的严格搜索，我找到了一个罪魁祸首:Google 日历事件导入器。

```
class GoogleCalendarEventImporter
  # ...

  def create_or_update_event(google_calendar_event)
    event = Event.find_or_initialize_by i_cal_uid: e.i_cal_uid
    if google_calendar_event.cancelled? && event.persisted?
      event.delete
    end
    # ...
  end
end 
```

看起来很无辜，对吧？如果 Google 日历事件被取消，则将其从数据库中删除。

原来当事件取消时，`i_cal_uid`可以是`nil`。只有约 10%的事件来自谷歌日历，其他 90%从未获得过`i_cal_uid`！这导致`#find_or_initialize_by`找到*其他 90%事件中的任何一个*，并删除该事件。每次同步日历更新时，都会运行这段代码——很多次。

在他们的辩护中，[谷歌确实记录了](https://developers.google.com/calendar/v3/reference/events)取消事件的`iCalUID`可以是空白的。然而，它被记录在`status`部分，而不是我期望的靠近`iCalUID`参考的地方。

> **状态**:删除的事件只能保证填充 id 字段。

## 修正和期待

快速解决方法是当谷歌日历事件被取消时，*而不是*删除记录。我做了这一更改并进行部署，以确保不再丢失任何数据。

```
def create_or_update_event(google_calendar_event)
  if google_calendar_event.cancelled?
    return # TODO: Remove cancelled events by e.id, not iCalID
  end
  # ...
end 
```

但是这个活动还是被取消了。不应该给任何人看。该代码将需要额外跟踪每个记录的谷歌事件 ID，只有当有匹配时才删除。

> **`id`和`i_cal_uid`有什么区别？**每个事件每个日历都有一个唯一的`id`，重复的事件都共用同一个`i_cal_uid`。

## 如何防止这种情况

唷。最后，我恢复了除 17 条记录之外的所有记录；我必须自己手动重新创建它们。但是，我再也不想这样了。以下是一些可以避免这种情况的方法:

1.  更好地理解与 Google 的 API 合同
2.  当破坏性行为发生时，发出更积极的警报
3.  更好的单元测试，当`i_cal_uid`为`nil`时处理

这篇文章最多能帮助人们从类似的数据丢失中恢复过来。最糟糕的是，它显示了一个拥有近十年经验的全职开发人员是多么容易犯这样一个巨大的错误！

请欣赏我的谦逊，但请花 50 美元购买一个带有备份策略的数据库。🙏