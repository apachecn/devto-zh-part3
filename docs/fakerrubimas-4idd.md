# 试着做了 Faker::Rubimas

> 原文：<https://dev.to/yutagoto/fakerrubimas-4idd>

你好，.这边。

因为没有做过 RubyGem，所以试着做了。 所需时间是 4 小时左右。

被称为[faker](https://rubygems.org/gems/faker) 的 gem 和称为[rubimas](https://rubygems.org/gems/rubimas) 、[faker-precure](https://rubygems.org/gems/faker-precure) 的 gem 激励。 。

## 完成的东西

[https://rubygems.org/gems/faker-rubimas](https://rubygems.org/gems/faker-rubimas)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [玉藤](https://github.com/YutaGoto) / [法克尔-鲁比马斯](https://github.com/YutaGoto/faker-rubimas)

### 使用 IMAS https://github.com/imas/rubimas 测试数据生成器

<article class="markdown-body entry-content container-lg" itemprop="text">

# Faker::Rubimas(魔术大师)

[![Build Status](img/e068e96e1468fe927023ad9487f6021b.png)](https://travis-ci.org/YutaGoto/faker-rubimas)

使用 765PRO 全明星(包括百万星)测试数据生成器。

这颗宝石的灵感来自于 [sue445/faker-precure](https://github.com/sue445/faker-precure) 和 [imas/rubimas](https://github.com/imas/rubimas) 。

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'faker-rubimas'
```

Enter fullscreen mode Exit fullscreen mode

然后执行:

```
$ bundle 
```

或者自己安装为:

```
$ gem install faker-rubimas 
```

## 使用

```
require 'faker/rubimas'

Faker::Rubimas.name
# => "望月杏奈"

Faker::Rubimas.favorite
# => "ハト"

Faker::Rubimas.hobby
# => "登山"

Faker::Rubimas.talent
# => "ポーカーフェイス"

Faker::Rubimas.idol_type
# => "Princess"

Faker::Rubimas.color
# => "#ffe43f"

Faker::Rubimas.cv
# => "長谷川明子"

Faker::Rubimas.unit_name
# => "クレシェンドブルー"

Faker::Rubimas.unit_member_names
# => ["天海春香", "周防桃子", "福田のり子", "松田亜利沙", "横山奈緒"]
```

Enter fullscreen mode Exit fullscreen mode

## 贡献的

在 https://github.com/YutaGoto/faker-rubimas 的 GitHub 上欢迎 Bug 报告和请求。这个项目旨在成为一个安全、友好的协作空间，贡献者…

</article>

[View on GitHub](https://github.com/YutaGoto/faker-rubimas)

## 使用方法

每次调用方法时，都会返回与该方法名称相关的字符串或数组。

```
require 'faker/rubimas'

Faker::Rubimas.name
# => "望月杏奈"

Faker::Rubimas.favorite
# => "ハト"

Faker::Rubimas.hobby
# => "登山"

Faker::Rubimas.talent
# => "ポーカーフェイス"

Faker::Rubimas.idol_type
# => "Princess"

Faker::Rubimas.color
# => "#ffe43f"

Faker::Rubimas.cv
# => "長谷川明子"

Faker::Rubimas.unit_name
# => "クレシェンドブルー"

Faker::Rubimas.unit_member_names
# => ["天海春香", "周防桃子", "福田のり子", "松田亜利沙", "横山奈緒"] 
```

Enter fullscreen mode Exit fullscreen mode

## 苦战之点

有没有 hobby 的偶像，如果像其他方法一样写的话偶尔会出错的现象。 我朴素地花了时间注意到。 。

```
requrie 'rubimas'

p Rubimas.find_by_name("ジュリア").hobbies
# => nil 
```

Enter fullscreen mode Exit fullscreen mode

所以，只有 hobby 的部分制作不同。 。 。

```
def name
  sample_idol.name.full
end

def hobby
  ::Rubimas.all.map(&:hobbies).flatten.sample
end 
```

Enter fullscreen mode Exit fullscreen mode

-

如果可以的话请试试。

## 再加上

液体错误:内部

我只报告了 Twitter 上做的事情，

[![faker-rubimas](img/939d89f08b963db4ba3eff384dbab1bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKbQyNwB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jnv5p7am2kjestvk04ea.png)

很快就下载了 16 个，吓了我一跳。