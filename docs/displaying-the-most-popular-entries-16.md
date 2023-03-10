# 显示最受欢迎的条目

> 原文：<https://dev.to/eecms/displaying-the-most-popular-entries-16>

ExpressionEngine 提供了通道参数和变量，让您可以计算一个条目被查看的次数。

## 轨道 _ 视图参数

您应该只在单入口页面上启用此功能，如新闻报道或产品详细信息页面，否则它会增加计数并扭曲您的总数。

下面是我们新闻频道的频道条目示例标签:

```
{exp:channel:entries channel="news" limit="1" track_views="one"}
    <h3>{title}</h3>
    {body}
    <div class="date">Posted on {entry_date format="%M %d, %Y"}</div>
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

`track_views`参数将只接受数字作为单词，即一、二、三或四。您可以将这四个数字用于不同的目的，但是我发现在实践中，一个数字对于大多数用例来说已经足够了。

## 显示查看次数

当您添加了`track_views`参数后，您现在可以开始使用`view_count_xxx`变量在您的条目中显示视图计数。这里的不同之处在于，你可以在任何地方显示条目，而不仅仅是在单个条目的页面上。

这是我们新闻列表页面的频道条目标签:

```
{exp:channel:entries channel="news" limit="10"}
    <h3>{title}</h3>
    <p>This entry has been viewed {view_count_one} times.</p>
    <div class="date">Posted on {entry_date format="%M %d, %Y"}</div>
{/exp:channel:entries} 
```

Enter fullscreen mode Exit fullscreen mode

注意这里我们使用的是`{view_count_one}`，这对应于我们之前添加的`track_views="one"`参数。如果您使用`track_views="two"`作为参数，那么要显示计数变量，您可以使用`{view_count_two}`。

## 创建顶级列表

假设我们想展示 10 个有史以来阅读量最大的新闻故事。我们将使用 EE 的`orderby`和`sort`参数按照视图总数对条目进行排序，并以降序排列(最高数字在前)。

```
<h2>Most read news</h2>
<ul>
    {exp:channel:entries channel="news" limit="10" orderby="view_count_one" sort="desc" dynamic="no"}
    <li>{title}</li>
    {/exp:channel:entries}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

注意，这里我们还添加了一个`dynamic="no"`参数，这样无论在哪里使用，输出都是恒定的。

## 按日期限制热门名单

有时，您可能希望限制列表显示最近七天内发布的条目。我们可以使用相同的 channel entries 标记来实现这一点，但是添加了`start_on`参数来限制日期范围。

此示例将显示最近连续七天的条目:

```
<h2>Most read news</h2>
<ul>
    {exp:channel:entries channel="news" limit="10" orderby="view_count_one" sort="desc" dynamic="no" start_on="-7 days"}
    <li>{title}</li>
    {/exp:channel:entries}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果你在过去的七天里没有发布任何条目，列表将会是空的！

## 进一步提炼你的清单

ExpressionEngine 还有其他几个参数，可以用来控制列表中显示的内容，例如:

*   `display_by="month" limit="1"` -当前月份的条目
*   `display_by="week" limit="2` -最近两周的条目
*   `display_by="day" limit="3` -持续三天的参赛作品
*   `start_on="last Monday"` -从您指定的开始日期开始的条目
*   `stop_before="{current_time}"` -直到某个时间的条目

可能性是无限的，所以尝试不同的参数！

-
最初由[罗布·艾伦](https://u.expressionengine.com/author/rob-allen)在[u.expressionengine.com](https://u.expressionengine.com/article/email-form-setting-a-predefined-subject)发表