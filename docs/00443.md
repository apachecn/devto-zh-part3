# 我更新了 Rails。现在一切都着火了。

> 原文：<https://dev.to/hint/i-updated-rails-now-everything-s-on-fire-245f>

*原贴于* [*提示的博客*](https://hint.io/blog/updated-rails-everything-is-on-fire) 。

在我的[最佳实践文章](https://hint.io/blog/rails-6-upgrade-best-practices)之后，我有一个升级 Rails 应用时出现的常见问题的主要例子。

我决定把一个小的内部应用升级到 Rails `6.0.0.rc1`。测试套件在 Rails `5.2.3`和 Ruby `2.6.2`上是绿色的，所以我把 Rails 版本放到了 Gemfile 中。

```
source '<https://rubygems.org>'
git_source(:github) { |repo| "<https://github.com/#{repo}.git>" }

ruby '2.6.2'

gem 'rails', '6.0.0.rc1' 
```

Enter fullscreen mode Exit fullscreen mode

在终端里运行`bundle update rails`，它更新了没有问题(我提过这是个小 app 吧？).即使在小应用上，这也应该让你惊讶。

我怀疑地运行了测试套件。

一路出错。

```
An error occurred while loading ./spec/services/step_processor_spec.rb.
Failure/Error: require File.expand_path('../../config/environment', __FILE__)

TypeError:
  Rails::SourceAnnotationExtractor is not a class/module
# /gems/gems/haml-rails-1.0.0/lib/haml-rails.rb:49:in `block in <class:Railtie>'
# ...more output...

Finished in 0.00027 seconds (files took 1.41 seconds to load)
0 examples, 0 failures, 17 errors occurred outside of examples 
```

Enter fullscreen mode Exit fullscreen mode

根据错误，让我们看看 Rails 中的`haml-rails`和`SourceAnnotationExtractor`，看看有什么变化，以及是否有更新版本的`haml-rails`。

但是等等。你可能会问:“为什么不尝试升级创业板，然后继续前进？”。花时间去理解问题的根本原因，可以为解决方案提供信心，并为解决整个代码库中更广泛的症状提供明确的方向。

花时间去理解问题的根本原因，可以为解决方案提供信心，并为解决整个代码库中更广泛的症状提供明确的方向。

这里是`lib/haml-rails.rb` :
中的第 49 行

```
SourceAnnotationExtractor::Annotation.register_extensions('haml') do |tag| 
```

Enter fullscreen mode Exit fullscreen mode

果不其然，第 6 轨发生了变化，将`SourceAnnotationExtractor`移动到`Rails::SourceAnnotationExtractor` ( [PR #32065](https://github.com/rails/rails/pull/32065) )。顶级类已被否决，但否决警告不适用于引发`TypeError`的子类。

知道了错误的来源，我们就可以检查是否有与 Rails 6 兼容的更新版本的`haml-rails`。同样，你会发现价值不仅仅在于升级 gem，还在于理解新版本中的变化。在`haml-rails`中，有一个更新的版本解决了这个问题。通过升级到那个版本，我能够运行测试套件而不出错，并回到手边的升级。

[安排一次与我的快速通话](https://calendly.com/natevick/upgrade-intro-call),看看我们的专业知识如何让您的团队在 Rails 升级期间提供新功能。