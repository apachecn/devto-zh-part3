# 用 Ruby + HTML/CSS API 创建图像

> 原文：<https://dev.to/mscccc/creating-images-with-ruby--htmlcss-api-16g4>

有没有想过像 [Product Hunt](https://producthunt.com) 或 [Medium](https://medium.com) 这样的时尚网站是如何生成那些花哨的 Twitter 截图的？

> ![unknown tweet media content](img/e5f80eb06b9e9b4e6a2acd2f639ebad0.png)![Steven 🖇 profile image](img/004a9573fa99d58554a1b57d03ac87d4.png)史蒂文·🖇@史蒂夫西![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)“@ textio 与麦当劳公司签约，在他们的招聘邮件中增加文字内容…”作者:[@马里斯萨克](https://twitter.com/marissac)[link.medium.com/XBc3nTBkUU](https://t.co/7VZ4CgpMVE)04:37am-09 mar 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1104239882879156224)

不要再想了！

本质上，他们在一个无头的 Chrome 实例中呈现 HTML/CSS 并截图。听起来很复杂？是的，它是。尤其是“大规模”

好事情是有 API 来做这个。

我将向您展示如何使用我专门为此目的构建的 API 来实现这一点: [HTML/CSS to Image API](https://htmlcsstoimage.com) 。

```
require "httparty"

auth = { username: 'user_id', password: 'api_key' }

html = "<div class=\"box\"><h3>Hello, world 😍</h3></div>"

css = ".box {
  border: 4px solid #8FB3E7;
  padding: 20px;
  color: white;
  font-size: 100px;
  width: 800px;
  height: 400px;
  font-family: 'Roboto';
  background-color: #8BC6EC;
  background-image: linear-gradient(135deg, #8BC6EC 0%, #9599E2 100%);
}"

image = HTTParty.post("https://hcti.io/v1/image",
                      body: { html: html, css: css },
                      basic_auth: auth)

# { url: https://hcti.io/v1/image/bfae7d68-86cc-4934-83ac-af3ba75a0d34 } 
```

[![Hello, world.](img/2d8c50d88a4315cdfa0f6d183d504065.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DDNlDTo6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hcti.io/v1/image/bfae7d68-86cc-4934-83ac-af3ba75a0d34)

完成了。我们有一幅✨.的图像

## Rails +缓存

如果您从 Rails 创建它们，并且已经使用了 memcached，这里有一个很好的技巧可以确保您只创建它们一次。

```
cache_key = "image/#{html}/#{css}"

image = Rails.cache.fetch(cache_key) do
  HTTParty.post("https://hcti.io/v1/image",
                      body: { html: html, css: css },
                      basic_auth: auth)
end 
```

我们使用 html/css 来生成缓存键。不用担心时间太长。Rails/Dalli 自动为您散列这个密钥，保证它是惟一的并且长度正确。

这样，如果您再次发送完全相同的有效载荷，Rails 将从缓存中提取 URL，而不是重新生成它。

## 另一个铁轨提示

如果您为所有内容生成这些，那么将 API 调用放入一个`after_create`中可能是一种尝试。我反对这样做。当“有请求”时，最好将 I/O 保持在绝对最小值。尽管请求可能只需要 30 毫秒，但如果端点已经做了很多事情，这可能会增加。

解决方案是使用后台作业。

因此，与其在`after_create`中直接打电话。您可以使用`after_create`将后台作业排队。然后图像将在后台生成。保持你的 Rails 响应速度超快。

## 造点酷的？

如果你做了一些很酷的东西，让我知道，这样我就可以发微博了！！