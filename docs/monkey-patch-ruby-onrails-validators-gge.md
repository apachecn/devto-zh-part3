# Monkey Patch Rails 验证器

> 原文：<https://dev.to/pablomarti/monkey-patch-ruby-onrails-validators-gge>

Rails 的认证解决方案提供了一些模块，为模型添加了一些很酷的功能，其中之一是 [Validatable](https://github.com/plataformatec/devise/blob/master/lib/devise/models/validatable.rb) ，它添加了电子邮件和密码所需的验证。其中一个验证是的*validates _ unique _ of*，它负责检查电子邮件地址是否唯一。

我从 2016 年开始做一个 Rails API，它用的是 Devise 用户需要给出独特的电子邮件。但有些事情改变了:现在 API 支持一个聊天框，它将被添加到许多网站上，我们希望获得这些访问者，我们必须支持重复的电子邮件。

最好的方法是为这些用户创建一个新的模型，但是由于对应用程序(这是一个聊天应用程序)的影响，我们没有时间来实现它。

另一个选择可能是这个 [Validatable 应该允许电子邮件的唯一性被限定范围#4767](https://github.com/plataformatec/devise/issues/4767) 但是讨论仍然是开放的。也许移除*可验证的*模块并实现一切，但这将产生额外的工作，并将增加失败的概率(永远记住 TDD)。相反，我采用了不同的方法。

利用 Ruby 的优势，我获得了针对 *email* 属性的模型验证器，并找到了 *UniquenessValidator* 。然后我重新定义了验证器的*验证*方法。

在 Rails 模型中: