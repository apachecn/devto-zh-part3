# 使用多个语言环境文件组织翻译

> 原文：<https://dev.to/runtime-revolution/organize-your-translations-using-multiple-locales-files-2kg4>

当然，这包含了一个神奇宝贝的例子！

<figure>[![](img/6ceb09a71ce9caf1da5eb406d8d159d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sql4z1Hc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AlwT3xnIqSotzB1Yj) 

<figcaption>照片由[凯尔格伦](https://unsplash.com/@kylejglenn?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

网站内容的国际化总是需要考虑的事情。虽然许多国家都有第二语言，但并不是每个人都知道如何说或写。在我的国家葡萄牙，只有最近几代人懂英语，至少在日常生活中能够使用。一些老一辈的人甚至不知道如何阅读和书写葡萄牙语；我国的文盲是一个主要问题。

因为这些类型的事情，一个网站(在我看来)应该至少有 2 种语言可供用户选择，或者有足够多的语言让尽可能多的用户使用。

在应用程序中硬编码文本有点不好，但是在 Rails 应用程序中，我们可以在 configs/locales 中找到一个名为 en.yml 的文件，我们可以在这个文件中设置可能出现在不同位置的字符串，而不是硬编码它们。但是，他们不需要只有英语。您可以为不同的语言创建其他翻译文件，例如:fr.yml 和 es.yml(分别为法语和西班牙语)。

要更改您正在使用的语言环境，您可以用语言代码设置 I18n.locale，或者甚至用 I18n.default_locale 设置默认语言。

```
I18n.locale = :pt

I18n.deafaul\_locale = :es 
```

好吧，但是，现在想象一下这种情况:你和我一样是一个神奇宝贝粉丝，你想做一个网站，上面有关于他们的所有信息(名字、类型、动作……)。是的，你可以把所有东西保存在数据库里，但这不是重点，我不希望你那么聪明。

从在 en.yml 中添加前 151 个字符的信息开始……它已经包含了很多信息，所以你会有一个非常大的文件。好了，现在为葡萄牙语创建一个 locales 文件，pt.yml。现在你已经有了一个葡萄牙人可以阅读的所有信息。

我说从第一个 151 开始…现在加上第二代的，你会得到 251 个神奇宝贝的信息。哦，但是在第二代中，第一代神奇宝贝可以学习新的动作，所以你必须更新整个文件中的信息。

这是一个很大的优势，但我想你已经知道我想去哪里了…在未来，如果你不从一开始就对它进行结构化，更改一个区域设置文件可能会很难管理。

如果我们能更好地组织他们就好了。

你可以的！Rails 允许你以一种更适合你的项目的方式来组织你的翻译。您可以为同一种语言创建多个区域设置文件，并以对您或项目范围有意义的结构将它们分布到不同的文件夹中。这个例子摘自 Ruby on Rails [指南](https://guides.rubyonrails.org/i18n.html#organization-of-locale-files) :

```
|-defaults
|---es.rb
|---en.rb
|-models
|---book
|-----es.rb
|-----en.rb
|-views
|---defaults
|-----es.rb
|-----en.rb
|---books
|-----es.rb
|-----en.rb
|---users
|-----es.rb
|-----en.rb
|---navigation
|-----es.rb
|-----en.rb 
```

(文件的扩展名是在 *ruby* 中，但是你可以使用 _yml _inside。)

要使用这样的结构，您需要让 Rails 知道在哪里寻找文件:

```
# config/application.rb

config.i18n.load\_path += Dir[Rails.root.join(_'config'_, _'locales'_, _'\*\*'_, _'\*.{rb,yml}'_)] 
```

回到我们的例子，我们现在可以想到一种可能的结构来组织我们的神奇宝贝的信息。这里有一个例子:

```
|-pokemons
|--models
|---en.yml
|---pt.yml
|--views
|---en.yml
|---pt.yml
|-moves
|--models
|---en.yml
|---pt.yml
|--... 
```

或者你可以这样做:

```
|-models
|--pokemons
|---en.yml
|---pt.yml
|--moves
|---en.yml
|---pt.yml
|--...
|-views
|--homepage
|---en.yml
|---pt.yml
|--... 
```

然后，每个文件会是这样的:

```
# config/locales/models/en.yml

en:
 models:
  pokemon:
   name: Name
   type: Type
   moves: Moves
...

# config/locales/models/pt.yml

pt:
 models:
  pokemon:
   name: Nome
   type: Tipo
   moves: Ataques
...

# config/locales/views/homepage/pt.yml

pt:
 views:
  homepage:
   title: Pokémon e Companhia
   main\_section: Vou apanhá-los todos!
... 
```

始终按照文件夹名称来确定翻译的范围，以避免覆盖。

有了这样的结构，您或项目的任何开发人员都可以更容易地添加、更改或删除特定语言的翻译。如果您需要重构一些代码并删除项目中的一些遗留引用，这非常有用。相信我，重构一个庞大而独特的翻译文件根本不是一件愉快的任务。

> **神奇宝贝 2002–2018 神奇宝贝。1995–2018 Nintendo/Creatures Inc ./GAME FREAK Inc . TM 和神奇宝贝角色名称是任天堂的商标。**
> 
> 在这篇文章中使用神奇宝贝的内容没有侵犯版权或商标的意图。

我目前是 Runtime Revolution 的一名 Ruby-on-Rails 开发人员，仍然喜欢为 GameBoy 玩传统的口袋妖怪游戏。去查看我们的 [*网站*](https://www.runtime-revolution.com/) *了解我们更多。哦，我们在招人！*

* * *