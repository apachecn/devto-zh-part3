# 用建议的 Gem 寻找 Ruby 方法的有趣方法

> 原文：<https://dev.to/chenge/a-fun-way-to-find-a-ruby-method-with-suggest-gem-3ik7>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ jbodah ](https://github.com/jbodah) / [建议 _rb](https://github.com/jbodah/suggest_rb)

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

…</article>

[View on GitHub](https://github.com/jbodah/suggest_rb)

```
require 'suggest'

# Object#what_returns? tells you which method returns the value
[1,2,3].what_returns? 1
=> [:first, :min] 
```

真好玩。