# 如果有人要看你的文件，那就不简单了

> 原文：<https://dev.to/has_many_books/if-someone-s-having-to-read-your-docs-it-s-not-simple-2gj>

你沉浸在一项新技术中——也许是调查一个图书馆，或者学习一门语言。你试图在头脑中构建新的概念，将你自己的数据和架构应用到文档中的通用说明中。很难！这就是为什么读到这样的内容会令人震惊:

> [这个库]使[做困难的事情]变得不费力。
> 
> 【复杂的事情】变得简单容易。
> 
> 你要做的只是【困难的事情】。

如果有人在谷歌上找到了你的作品，他们就被困住了。在某种程度上，被卡住是令人心烦意乱的。所以尽量不要让他们感觉更糟，告诉他们应该如何直截了当地找到它。

## 你渴望分享你的兴奋！

当我在写技术文档的时候，我发现自己总是在用“只是”，尽管我对这样的词有着强烈的信念。因为我太热衷于分享我的启示了。令人兴奋的是，我们编写的代码可能会有所帮助，我们自然渴望与他人交流。一点轻微的编辑会让你保持代码的清晰和热情，丢掉那些会被理解为居高临下的部分。

## 怎么办？

浏览你的技术写作，寻找诸如“容易”、“不痛苦”、“直截了当”、“简单”和“仅仅”这样的词语。去掉它们后，你的字迹可能会更清晰。这里有一个经过清理的真实生活的例子，表明代码的乐趣可以更好地解释没有所有的“只是”和“简单”的。

原件:

> 2.1.4 调用邮件程序
> 
> 邮件程序实际上只是呈现视图的另一种方式。他们不是渲染视图并通过 HTTP 协议发送，而是通过电子邮件协议发送。因此，让您的控制器告诉邮件程序在用户成功创建后发送一封电子邮件是有意义的。
> 
> 设置这个非常简单。
> 
> 首先，让我们创建一个简单的用户支架:
> 
> `$ bin/rails generate scaffold user name email login`
> 
> `$ bin/rails db:migrate`
> 
> 现在我们有了一个用户模型，我们只需编辑 app/controllers/users _ controller . Rb，通过编辑 create 动作并在成功保存用户后插入对`UserMailer.with(user: @user).welcome_email`的调用，让它指示用户邮件程序向新创建的用户发送电子邮件。

已编辑:

> 2.1.4 调用邮件程序
> 
> 邮件程序是呈现视图的另一种方式。他们不是渲染视图并通过 HTTP 协议发送，而是通过电子邮件协议发送。因此，让您的控制器告诉邮件程序在成功创建用户时发送电子邮件是有意义的。
> 
> 为此，首先，让我们使用 Rails scaffold 创建一个用户:
> 
> `$ bin/rails generate scaffold user name email login`
> 
> `$ bin/rails db:migrate`
> 
> 现在我们有了一个用户模型，我们可以编辑 app/controllers/users _ controller . Rb，并指示 UserMailer 向新创建的用户发送电子邮件。编辑创建动作，并在成功保存用户后插入对`UserMailer.with(user: @user).welcome_email`的调用。

这样更好:更干净，不那么杂乱，坦率地说，不那么令人恼火。没有一个编程是“简单得令人痛苦”的，所以让我们确保我们的文档不会妨碍人们使用我们辛辛苦苦创建的代码。

谢谢！

这句话出自一个阅读了大量技术文档，到处都能看到这些话，并且每次都感到有点难过的人。

最初发布于 [https://justsimply.dev](https://justsimply.dev)

更多事情在[https://consonance.app/blog](https://consonance.app/blog)