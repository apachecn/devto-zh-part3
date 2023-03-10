# 用 web 驱动程序替换 chromedriver-helper

> 原文：<https://dev.to/ruralocity/replacing-chromedriver-helper-with-webdrivers-32bl>

> 原贴于[日常轨](https://everydayrails.com/2019/04/09/chromedriver-helper-webdrivers.html)。

不再支持 chromedriver-helper 。如果你试图安装它或升级到一个新版本，你会看到这样的通知:

```
+--------------------------------------------------------------------+
|                                                                    |
|  NOTICE: chromedriver-helper is deprecated after 2019-03-31\.       |
|                                                                    |
|  Please update to use the 'webdrivers' gem instead.                |
|  See https://github.com/flavorjones/chromedriver-helper/issues/83  |
|                                                                    |
+--------------------------------------------------------------------+ 
```

正如消息所暗示的，如果您需要在 Rails 应用的范围内测试 JavaScript，那么 *webdrivers* 就是您想要管理 Selenium 驱动程序的方式。如果你已经购买了我的 Rails 测试书， *[用 RSpec](https://leanpub.com/everydayrailsrspec)* 进行日常 Rails 测试，你可以从使用 webdrivers 的 Leanpub 下载更新版本。

这个切换总体来说是一个好的举措。webdrivers 提供了对非 Chrome 浏览器的支持，维护起来更加主动。而且，从我的经验来看，进行切换主要是一行代码的替换。打开你的*gem 文件*并进行交换:

```
 group :test do
-  gem 'selenium-webdriver'
-  gem 'chromedriver-helper' +  gem 'webdrivers'
   # other test-only dependencies ...
 end 
```

由于 webdrivers 引入了 *selenium-webdriver* 作为依赖项，我已经把它从我的应用程序自己的显式依赖项中移除了。如果您使用 Spring，您可能需要用`bin/spring stop`停止它，然后照常运行您的测试。假设之前 chromedriver-helper 可以正常工作，他们应该可以继续使用 webdrivers。

我发现的唯一例外是在一台安装了 Chrome dev channel 版本的电脑上。在升级我的书的源代码时，我遇到了以下故障:

```
Failures:

  1) Tasks user toggles a task
     Failure/Error: visit root_path

     Net::HTTPServerException:
       404 "Not Found"
     # /Users/asumner/.rvm/gems/ruby-2.4.1/gems/webdrivers-3.7.1/lib/webdrivers/common.rb:109:in `get'
     # /Users/asumner/.rvm/gems/ruby-2.4.1/gems/webdrivers-3.7.1/lib/webdrivers/chromedriver.rb:26:in `latest_version'
     # /Users/asumner/.rvm/gems/ruby-2.4.1/gems/webdrivers-3.7.1/lib/webdrivers/common.rb:37:in `desired_version'
<... more stack trace ...>
     # ./spec/features/tasks_spec.rb:11:in `block (2 levels) in <top (required)>'
     # /Users/asumner/.rvm/gems/ruby-2.4.1/gems/spring-commands-rspec-1.0.4/lib/spring/commands/rspec.rb:18:in `call'
     # -e:1:in `<main>'

Finished in 4.71 seconds (files took 0.59138 seconds to load)
46 examples, 1 failure

Failed examples:

rspec ./spec/features/tasks_spec.rb:4 # Tasks user toggles a task 
```

乍一看，我以为我的应用程序出于某种原因返回了 404，但这没有意义。在挖掘了 gem 的来源后，我发现 404 来自谷歌的服务器，试图从 dev 频道下载最新的 ChromeDriver 版本。我的时间很紧，用标准安装替换我的 dev channel Chrome 解决了这个问题。(这个现在可能已经解决了；随着我了解的越来越多，我会继续挖掘并更新这篇文章。)

我也读到过在 VCR 旁边使用网络驱动的问题。我自己并没有遇到这些问题，但是[的解决方法现在已经记录在案](https://github.com/titusfortner/webdrivers/wiki/Using-with-VCR-or-WebMock)。

感谢伊藤纯一[与我分享这个问题](https://github.com/everydayrails/everydayrails-rspec-2017/issues/100)，感谢[迈克·达莱西奥](https://github.com/flavorjones)为 chromedriver-helper 所做的工作，感谢你们的阅读。我希望这些信息对你有所帮助。