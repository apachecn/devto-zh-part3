# rubo COP-弥生版 0.1.0リリース

> 原文：<https://dev.to/yutagoto/rubocop-yayoi-version-0-1-0-3hm5>

不久前推出了名为`rubocop-yayoi`的 gem。

[![yutagoto](img/afaacaa5142d6e96a198e6956841ba38.png)](/yutagoto) [## rubocop-yayoiを作った

### 森英士后藤 1915 年 5 月 1 日 11 分钟阅读

#ruby #rubygems #imas #rubocop](/yutagoto/rubocop-yayoi-45n6)

发布了改良后的东西！

## 完成的东西

[https://rubygems.org/gems/rubocop-yayoi](https://rubygems.org/gems/rubocop-yayoi)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[yuta goto](https://github.com/YutaGoto)/[rubo COP-弥生](https://github.com/YutaGoto/rubocop-yayoi)

<article class="markdown-body entry-content container-lg" itemprop="text">

# Rubocop::弥生

[![Build Status](img/d478a51e8a007ddd437f0c285063abf5.png)](https://travis-ci.org/YutaGoto/rubocop-yayoi)

`ζ*'ヮ')ζ＜うっうー！`

这个 gem 是执行[rubocop](https://github.com/rubocop-hq/rubocop) 后显示的文本会被偶像大师 765 专业所属的高槻和好风指出的 gem！

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'rubocop-yayoi'
```

Enter fullscreen mode Exit fullscreen mode

然后执行:

```
$ bundle 
```

或者自己安装为:

```
$ gem install rubocop-yayoi 
```

## 使用

### robocop 配置文件

把这个放进你的`.rubocop.yml`。

```
require: rubocop-yayoi 
```

现在你可以运行`rubocop`，它会自动加载 RuboCop RSpec cops 和标准的 cops。

### 命令行

```
rubocop --require rubocop-yayoi
```

Enter fullscreen mode Exit fullscreen mode

### 耙任务

```
RuboCop::RakeTask.new do |task|
  task.requires << 'rubocop-yayoi'
end
```

Enter fullscreen mode Exit fullscreen mode

## 发展

在签出 repo 之后，运行`bin/setup`来安装依赖项。然后，运行`rake spec`来运行测试。你也可以运行`bin/console`得到一个交互式提示，让你进行实验。

要将这个 gem 安装到您的本地机器上，运行`bundle exec rake install`。要发布新版本，更新`version.rb`中的版本号，然后运行`bundle exec rake release`

</article>

[View on GitHub](https://github.com/YutaGoto/rubocop-yayoi)

使用方法请参阅 GitHub repository，或参阅上面的`dev.to`文章。

## 更新的点

### 不再显示 waning 消息。

[![rubocop-yayoi](img/732b2e72a0f1a73a5ffaa7d6d6955100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eQdDG9Qw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5k1o7sg52d7qizhkmph6.png)

重新代入`rubocop-performance`以外的常数的 waning 消失了！

这样 warning 就不会再显示满屏了。

接下来又会慢慢增加 cop 的翻译！

如果你愿意的话，请用着做。