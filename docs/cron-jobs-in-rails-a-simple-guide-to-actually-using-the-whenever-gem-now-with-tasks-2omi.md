# Rails 中的 Cron 作业:实际使用 Whenever 的简单指南(现在有任务！)

> 原文：<https://dev.to/risafj/cron-jobs-in-rails-a-simple-guide-to-actually-using-the-whenever-gem-now-with-tasks-2omi>

我有机会使用`whenever`在 Rails 中安排任务，我写这篇文章是为了将来参考。我知道在 Rails 中还有其他方法来调度常规任务，但是这是一种——也许是最简单的方法。
注意:本指南适用于 Linux 或 MacOS 系统。

# 什么是 cron jobs？

Cron 是一个作业调度器，允许您在固定的时间(例如，每分钟、每小时、每天下午 3 点)执行重复任务。
作业被写入一个名为[crontab](https://www.adminschoice.com/crontab-quick-reference)(cron 表的简称)的文件中，通常保存在这里:`/usr/bin/`

# 在 Rails 中使用`whenever`进行 cron 作业

[`Whenever`](https://github.com/javan/whenever) 是帮助你设置 cron jobs 的宝石。您可以通过用 Ruby 代码编写您的作业，让`whenever`为您更新 crontab，而不必自己手动编辑 crontab。你将在`schedule.rb`进行编辑

**注意:**值得一提的是，实际的任务本身(在我的例子中，定期发送批处理消息)应该*而不是*写在 crontab 中，或者写在用于写入 crontab 的 Rails 文件`schedule.rb`中。任务本身应该作为 Rake 任务单独编写。然后，您可以将 Rake 任务作为 cron 作业定期调度。

## 步骤

### 1。安装`whenever`

将其包含在您的应用程序的`Gemfile`中，并运行`bundle`进行安装。

```
gem 'whenever', require: false 
```

Enter fullscreen mode Exit fullscreen mode

### 2。在 schedule.rb 中调度 cron 作业

`config/schedule.rb`是用于指定何时执行作业的文件，以及 Rake 任务的名称(同样，实际任务应该写在单独的文件中)。

从您的 Rails 应用程序根目录执行这个命令来创建`schedule.rb`文件:

```
bundle exec wheneverize . 
```

Enter fullscreen mode Exit fullscreen mode

现在，在`schedule.rb`中写下你的工作细节。比如你想每分钟执行一次耙子任务`batch:send_messages`:

```
every 1.minute do
  rake 'batch:send_messages'
end 
```

Enter fullscreen mode Exit fullscreen mode

还可以做`every 2.hours`和`every 1.day`等等。

### 3。将`whenever`任务写入您的 crontab

让`whenever`将你的工作写入 crontab。

```
bundle exec whenever --update-crontab 
```

Enter fullscreen mode Exit fullscreen mode

**重要提示**
`Whenever`在写入 crontab 时会考虑您的 Rails 应用环境。如果您使用的是`development`环境，那么使用下面的选项运行命令(默认为`production`)。

```
whenever --update-crontab --set environment='development' 
```

Enter fullscreen mode Exit fullscreen mode

作为参考，这是您的 crontab 在运行这个命令后的样子(`RAILS_ENV`是`development` ):

```
# Begin Whenever generated tasks for: /Users/<USER>/<APPNAME>/config/schedule.rb at: 2019-01-14 18:15:41 +0900
* * * * * /bin/bash -l -c 'cd /Users/<USER>/<APPNAME> && RAILS_ENV=development bundle exec rake batch:send_messages --silent'

# End Whenever generated tasks for: /Users/<USER>/<APPNAME>/config/schedule.rb at: 2019-01-14 18:15:41 +0900 
```

Enter fullscreen mode Exit fullscreen mode

**其他命令**

*   Display crontab: `crontab -l`
*   擦掉你的`whenever task` : `bundle exec whenever --clear-crontab`的 crontab

### 4。创建耙子任务

Rake 任务遵循特定的命名约定——名称空间和任务名称。名称空间用于将某些任务组合在一起。例如，在下面的例子中，如果您有多个批处理任务，您可以将它们都放在`batch`下。

在`lib/tasks/batch.rake`下为你的 Rake 任务生成一个文件。它们遵循以下公式:

`rails g task <NAMESPACE> <TASKNAME>`

因此，为了在`batch`名称空间中生成我们的`send_messages`任务，我们将在终端中输入以下内容:

```
rails g task batch send_messages 
```

Enter fullscreen mode Exit fullscreen mode

现在编写您的任务，它需要两个参数:`desc`，这是您的任务的任意描述，和`task`，这是要执行的代码。

例如:

```
namespace :batch do
  desc 'Send out batch messages'
  task send_messages: :environment do
    # The code to actually send our messages would go here
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过从命令行执行 Rake 任务来测试它，如下所示。

```
rails batch:send_messages 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！