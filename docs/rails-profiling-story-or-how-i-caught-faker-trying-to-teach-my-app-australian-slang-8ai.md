# Rails 剖析故事，或者我如何抓住 Faker 试图教我的应用澳大利亚俚语

> 原文：<https://dev.to/evilmartians/rails-profiling-story-or-how-i-caught-faker-trying-to-teach-my-app-australian-slang-8ai>

很久以前(实际上是几天前)我一直在做一个小的重构任务:将 [`factory_bot`](https://github.com/thoughtbot/factory_bot) 集成到邮件预览中。

看起来是一个非常简单的任务:加载`factory_bot_rails`并包含语法方法:

```
# spec/mailers/previews/application_preview.rb

require "factory_bot_rails"

class ApplicationPreview < ActionMailer::Preview
  include FactoryBot::Syntax::Methods

  private

  # user record for previews
  def user
    @user ||= build_stubbed :user
  end
end 
```

之后，我检查了本地的一切正常，发现了一些奇怪的事情:

[![mailer preview](img/19acef67da9dff65724e6ad00604848a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vN-falof--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ocu4gtknx72rm2xypl1.png)

哇！我们有很多地方！在我们的纯英文应用程序中！太棒了。
*不，不是*。

我很快找到了它的来源:我们工厂使用的 [`faker`](https://github.com/stympy/faker) 宝石。

我检查了回购协议，有点惊讶:

```
require 'i18n'

#...

I18n.load_path += Dir[File.join(mydir, 'locales', '**/*.yml')]
I18n.reload! if I18n.backend.initialized? 
```

Faker 会加载它所有的语言环境文件，并且不允许你选择你唯一想要的语言环境。对我来说，那是一种意想不到的行为。

**注意:**您可以通过设置 [`config.i18n.available_locales`](https://guides.rubyonrails.org/i18n.html#configure-the-i18n-module) 来明确指定您要在应用程序中使用哪些语言环境；如果没有设置，所有加载的语言环境都是可用的。

我决定检查加载一堆不必要的 YML 文件对应用程序启动时间的影响。

由于`i18n`会延迟加载语言环境，所以我在我的`rspec_helper.rb`中添加了下面一行来强制加载:

```
# add to rails_helper.rb
I18n.backend.load_translations 
```

注意:由于我们在工厂中使用`faker`,我们几乎在每个测试中都需要区域设置。

我的包中已经有了 [`test-prof`](https://test-prof.evilmartians.io/#/) ，所以分析引导时间就像运行这个命令一样简单:

```
$ SAMPLE=1 TEST_STACK_PROF=boot TEST_STACK_PROF_FORMAT=json bundle exec rspec 
```

让我解释一下这里发生的事情。

我们“告诉”测试教授使用[堆栈教授](https://github.com/tmm1/stackprof)和[引导模式](https://test-prof.evilmartians.io/#/stack_prof?id=profiling-application-boot)来分析我们的测试运行。

在这种模式下，`stackprof`在加载之后立即开始收集样本，并在第一次测试运行之前停止(更准确地说，在 RSpec `before(:suite)`钩子中)。

我们还传递`SAMPLE=1`来只执行一个随机测试(这个特性也是由测试教授提供的[):我们不关心一个特定的例子，因为我们在分析引导时间。](https://test-prof.evilmartians.io/#/tests_sampling)

最后，`TEST_STACK_PROF_FORMAT`用于生成 JSON 格式的剖析报告(特性[已经添加](https://github.com/tmm1/stackprof/pull/103)到`stackprof`本身，但是还没有发布)。

如何处理这个 JSON 报告？让我们把它载入[速度范围](https://www.speedscope.app)！

[Speedscope](https://www.speedscope.app) 是一个火焰图查看器，它可视化了由流行的分析器(包括`stackprof`)生成的 JSON 报告。

注:什么是火焰图，如何阅读？查看[“官方文件”](http://www.brendangregg.com/flamegraphs.html)以及[米哈·雷卡尔](https://www.youtube.com/watch?v=6uKZXIwd6M0)的这篇演讲。

这就是我的发现:

<figure>

[![flame graph with default faker](img/635aa4f6e69e7cecd4b30640ac1eb77e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j0jo2iv0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7m0izg2gzfo7ec0a84kg.png)

<figcaption>Default Faker behaviour (loading all translations)</figcaption>

</figure>

我们花了大约 1.1 秒来加载区域设置。

如果我们只加载英文文件(用[这个补丁](https://gist.github.com/palkan/79fc88207dc856532fb777b8204f7131))会怎么样？

<figure>

[![flame graph with slim faker](img/8ff3cab8ea9de424e3e8a8257c466f40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DqIDSDIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9i3hzx4r32f4xjewx9rl.png)

<figcaption>Patched Faker (loading only English translations)</figcaption>

</figure>

加载语言环境只花了大约 0.4 秒。

看起来没什么大不了的😕

如果我们添加 [`bootsnap`](https://github.com/Shopify/bootsnap) ，我们可以在不“修补”`faker`的情况下获得相同的结果:

<figure>

[![flame graph with bootsnap](img/cee25ae9a250ba0f56550760be763a75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BFR7S7FH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dr450wbfa6z6h8sh6una.png)

<figcaption>Default Faker with Bootsnap</figcaption>

</figure>

也许，我们浪费了很多记忆？

让我们尝试使用 [`memory_profiler`](https://github.com/SamSaffron/memory_profiler) gem:
来测量内存占用

```
require "memory_profiler"

MemoryProfiler.report do
  I18n.backend.load_translations
end.yield_self(&:pretty_print) 
```

加载所有可用语言环境时:

```
retained objects by gem
-----------------------------------
    147586  psych
      5462  i18n-1.5.3
         2  activesupport-6.0.0.beta1

retained objects by class
-----------------------------------
    148628  String
      2550  Array
      1022  Symbol
       848  Hash
         2  Proc 
```

和英文版:

```
retained objects by gem
-----------------------------------
     44301  psych
      3418  i18n-1.5.3
         2  activesupport-6.0.0.beta1

retained objects by class
-----------------------------------
     45240  String
      1202  Array
       910  Symbol
       367  Hash
         2  Proc 
```

因此，2.6MB 与 7.8MB 之间的差异可以忽略不计。又来了。

我们必须承认，在这个特殊的案例中，我们的侧写没有发现任何重大问题。

这也可以被认为是一个幸福的结局🙂)

另外，也可以看看这些关于测试教授简介的帖子:[“慢红宝石测试的好医生”](https://evilmartians.com/chronicles/testprof-a-good-doctor-for-slow-ruby-tests)和[“红宝石测试的工厂疗法”](https://evilmartians.com/chronicles/testprof-2-factory-therapy-for-your-ruby-tests-rspec-minitest)。

P.P.S .是的，Faker 真的很支持[澳洲俚语](https://github.com/stympy/faker/blob/e2081e7989344c7f11990d974266ea728531f42c/lib/locales/en-au-ocker.yml)。

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！