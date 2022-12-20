# 这是我的博客

> 原文：<https://dev.to/saral/this-is-my-blog-lce>

[![alt text](img/0b6996824a201cc7a27234622a81f5bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TCnUq8kj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/1437904/pexels-photo-1437904.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D500%26w%3D500)

最后，经过几周的学习和在个人博客上的工作，我可以说它已经有了一个令我满意的形状。仍然有很多工作需要做，特别是博客的样式，但是，总的来说，我认为我已经得到了我想要添加的功能。那么，我的个人博客整合了哪些功能呢

1.  用户认证:
    对于用户认证，我使用了‘bcrypt’gem。虽然在我的其他项目中我使用了“设计宝石”，这是我的项目，我选择使用 bcrypt。此外，用户认证并不是我在这个项目中绝对需要的功能，因为这是一个个人博客，我猜唯一上传到这里的人将是我。
    以下是我如何让 bcrypt 为[博客](https://dev.to/saral/rails-here-i-come-day5-2a7l)工作的。

2.  对于我的博客，我希望能够像在 Dev 上一样输入文本。因此，我开始寻找如何在博客上实现降价。此外，我不知道有一种叫做“富文本编辑器”的东西存在。如果我知道这一点，我可能会尝试使用富文本编辑器。我的无知是塞翁失马焉知非福，因为现在我掌握了如何实现 markdown 和富文本编辑器的知识。
    下面是我如何实现[降价](https://dev.to/saral/implementing-markdown-on-my-blog-post-46jf)

3.  Bootstrap 和 Font-awesome:
    接下来，我想使用 bootstrap，为此，我遵循了 bootstrap gem 主页上提供的说明。我还使用了字体很棒的图标来设计图标。再次，字体很棒的宝石页面在实现足总图标时派上了用场。

4.  分页:
    为了分页，我使用了 [will_paginate](https://rubygems.org/gems/will_paginate) gem。下面是我在[博客](https://dev.to/saral/pagination-done-309l)上实现的 gem。

5.  最后，为了给博客文章添加图片，我使用了回形针宝石。我能够让 gem 在本地主机上工作，但是，我很快在 Heroku 上遇到了一个问题。这些图像看起来存储在我的数据库中，但每次我刷新图像就会消失。那时我才知道，Heroku 不会存储我的图像，因此我必须将我的图像存储在其他地方。推荐的平台是 AWS。

现在我开始我的下一个目标，在 AWS 上获取图像。