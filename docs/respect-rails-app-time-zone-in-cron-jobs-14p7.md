# 在 cron 作业中遵守 Rails 应用程序时区

> 原文：<https://dev.to/tetiross/respect-rails-app-time-zone-in-cron-jobs-14p7>

假设您需要在 Rails 应用程序中设置默认时区。你去`application.rb`写:

```
config.time_zone = 'Central Time (US & Canada)' 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。现在，当你根据时间执行一些操作时，你的应用程序会考虑这些信息。

现在，假设您需要添加一些重复性工作。很可能你会使用 [`whenever`](https://github.com/javan/whenever) 宝石，并且会在宝石中生成这种代码`schedule.rb` :

```
every :monday, at: '9am' do
  rake 'send_weekly_reports'
end 
```

Enter fullscreen mode Exit fullscreen mode

这段代码会做什么？它将在每周一上午 9 点运行`send_weekly_reports`耙任务。酪什么时区上午 9 点？可能是我们之前设置为应用默认时区的时区？不。它将使用服务器时区。

但是如果你想使用你的应用时区呢？我们有两个选择:
a)我们可以将我们的服务器时区更改为我们在应用程序中使用的时区；
b)我们可以在自己的应用程序中处理。

让我们看看如何在应用程序中处理它。

您需要将这段代码添加到您的`schedule.rb`文件:

```
require_relative './environment'

def timezoned time
  Time.zone.parse(time).utc
end

every :monday, at: timezoned('9am') do
  rake 'send_weekly_reports'
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，您需要使用方法`timezoned`,向它传递所需的时间，并将您的时间解析为服务器通常使用的 UTC。
`require_relative './environment'`就是在这里能够调用`timezoned`的方法代码。

因此，现在如果我们设置某个作业在上午 9 点运行，它将在您在`application.rb`定义的时区的上午 9 点运行。

*封面图片由[史丹利戴](https://unsplash.com/@stanleydai)上[Unsplash](https://unsplash.com/photos/SEB_I5Cl_sw)T5】*