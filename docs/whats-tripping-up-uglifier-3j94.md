# 什么更丑？

> 原文：<https://dev.to/jsrn/whats-tripping-up-uglifier-3j94>

在生产模式下，Rails 4 使用`uglifier` gem 将您的 JavaScript 代码压缩成小而漂亮的东西，以便通过网络发送。然而，当你有一个语法错误，它可能会失败，相当…不体面。

您会得到一个很大的堆栈跟踪，告诉您有一个语法错误。它甚至可以告诉你错误是什么！意外的标点符号。某处有个逗号。在哪里？谁知道呢。它不会告诉你。

如果您有一个大型项目，或者有多人进行大的更改，检查最近提交的差异以找出错误是从哪里引入的可能会花费一些时间。下面是如何使用 rails 控制台至少找到有问题的文件:

由于丑丑器不能在开发中运行，让我们在生产模式下进入 rails 控制台:

```
RAILS_ENV=production rails c 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用下面的代码片段来查找我们的错误:

```
# Take each javascript file...
Dir["app/assets/javascripts/**/*.js"].each do |file_name|
  begin
    # ... run it through the uglifier on its own...
    print "."
    Uglifier.compile(File.read(file_name))
  rescue StandardError => e
    # ... and tell us when there's a problem!
    puts "\nError compiling #{file_name}: #{e}\n"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这为我最近节省了一点时间，所以我希望它对其他人也有用。