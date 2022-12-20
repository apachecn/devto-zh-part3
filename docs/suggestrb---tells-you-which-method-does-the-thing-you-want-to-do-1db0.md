# 告诉你哪个方法做你想做的事情

> 原文：<https://dev.to/philnash/suggestrb---tells-you-which-method-does-the-thing-you-want-to-do-1db0>

前几天我偶然发现了这个项目，并认为这是一种增强学习和使用 Ruby 的有趣方式。

Suggest 给了对象两个额外的方法；`what_returns?`和`what_mutates?`。这两种方法都接受您感兴趣的结果，并返回您可以在对象上调用以获得该结果的方法。

例如:

```
require 'suggest'

[1,2,3].what_returns? 1
=> [:first, :min] 
```

Enter fullscreen mode Exit fullscreen mode

当你在`irb`中工作时，我可以看到这是有用的，你确信有一个方法可以做你想要的事情，但是你不记得了。它可以节省一次搜索文档的旅程，甚至可以推荐比你想的更好的方法。

在 GitHub 上查看:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ jbodah ](https://github.com/jbodah) / [建议 _rb](https://github.com/jbodah/suggest_rb)

### ❓告诉你用哪种方法做你想做的事情

<article class="markdown-body entry-content container-lg" itemprop="text">

# 建议

告诉你用哪种方法做你想做的事情

## 放弃

我不建议你把它放在你的 gem 文件里。将它保存在您系统的 gems 中(例如`gem install`)，并在需要时加载(例如`irb -rsuggest`、`RUBY_OPT=-rsuggest irb`等)

## 装置

```
gem install suggest_rb 
```

## 使用

```
require 'suggest'
# Object#what_returns? tells you which method returns the value
[1,2,3].what_returns? 1
=> [:first, :min]

# You can also specify the args you want that method to take
[1,2,3].what_returns? [1], args: [1]
=> [:first, :take, :grep, :min]

# By default, it only returns methods that don't mutate the object
[1,2,3].what_returns? [1], args: [1], allow_mutation
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/jbodah/suggest_rb)