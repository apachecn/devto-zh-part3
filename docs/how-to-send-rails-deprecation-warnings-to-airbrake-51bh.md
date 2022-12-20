# 如何向 Airbrake 发送 Rails 弃用警告

> 原文：<https://dev.to/svyatov/how-to-send-rails-deprecation-warnings-to-airbrake-51bh>

本周，我们将把 Ruby on Rails 版本从 4.2 升级到 5.2。我们期待已久的改变。当然，在这个过程中，有很多反对的警告抛给了我们。Rubocop 和测试捕捉到了其中的绝大多数，但我们没有 100%的代码覆盖率，所以这些警告中的一些进入了生产。

在开发中，Rails 将这些警告发送到日志文件:

```
# Print deprecation notices to the Rails logger.
config.active_support.deprecation = :log 
```

合理的默认，但是我改成了`:raise`。在开发中以这种方式发现和处理警告要快得多。

对于生产环境 Rails 使用 [`ActiveSupport::Notifications`](https://api.rubyonrails.org/classes/ActiveSupport/Notifications.html) :

```
# Send deprecation notices to registered listeners.
config.active_support.deprecation = :notify 
```

问题是在生产中没有人听它。幸运的是，用空气制动很容易解决这个问题。起初我试着“谷歌”一下这个解决方案，但是没有找到任何有用的东西，所以我把我的解决方案分享给其他像我一样寻求帮助的人

```
# config/initializers/deprecations_notifier.rb

ActiveSupport::Notifications.subscribe('deprecation.rails') do |_name, _start, _finish, _id, payload|
  error = ActiveSupport::DeprecationException.new(payload[:message])
  error.set_backtrace(payload[:callstack].map(&:to_s))

  params = {
    gem_name: payload[:gem_name],
    deprecation_horizon: payload[:deprecation_horizon]
  }

  Airbrake.notify(error, params) do |notice|
    notice[:context][:severity] = 'warning'
  end
end 
```

如果您正在使用 [`CurrentAttributes`](https://api.rubyonrails.org/classes/ActiveSupport/CurrentAttributes.html) 并在那里存储`request.uuid`，那么将当前请求 UUID 添加到`params`中也是一个好主意。