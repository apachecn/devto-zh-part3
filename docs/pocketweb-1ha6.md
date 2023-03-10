# 写了将 Pocket 上喜欢的页面添加到书签的网络 APP

> 原文：<https://dev.to/kimihito/pocketweb-1ha6>

# TL；速度三角形定位法(dead reckoning)

第一次尝试用 Go 语言( Echo )写代码。

[https://github.com/kimihito/tohatebu/](https://github.com/kimihito/tohatebu/)

# 想做的事

[使用 IFTTT 的邮件通知功能将 Pocket](https://getpocket.com) 中喜欢的报道投稿到[所有的书签](http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA%A5%D6%A5%C3%A5%AF%A5%DE%A1%BC%A5%AF)，但是通过邮件提供书签功能已经结束了。

[结束邮件书签投稿功能-书签开发博客](http://bookmark.hatenastaff.com/entry/2018/12/17/150243)

所以我也试着做了 Go 语言的学习。 因为不懂 Go 语言，所以迷上了很多…

# 在做的事

POST 被叫出来的话，只要敲[所有的书签](http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA%A5%D6%A5%C3%A5%AF%A5%DE%A1%BC%A5%AF)[API](http://d.hatena.ne.jp/keyword/API) 追加参数中的 URL 即可。

用 Pocket 发了收藏夹就叫 POST 的部分是从 IFTTT 的 webhook service 上进行的。

# 使用过的东西

*   golang 1 . 11 . 5(去 modulesを利用)
*   回声
*   [Heroku](https://heroku.com)
*   [IFTTT webhooks](https://ifttt.com/maker_webhooks)
*   [ngrok](https://ngrok.com/) (本地动作确认)

# 迷上了

[请求令牌的获取](http://developer.hatena.ne.jp/ja/documents/auth/apis/oauth/consumer)部分写不好，结果[写了所有博客 API 用的 gem 的- blog.kymmt.com](https://blog.kymmt.com/entry/hatenablog_gem) 中的获取[令牌的 Ruby 代码【tt2】](https://github.com/kymmt90/hatenablog/blob/61b1015073cba9a11dfb4fd35d34922757abd70b/exe/get_access_token)

我写了一下，但是还不习惯 Go 语言…

# 作为参考的东西

特别是关于 hatena-go，我参考了很多代码。 谢谢你。

[https://github.com/8pockets/hatena-go](https://github.com/8pockets/hatena-go)

[在 Go 语言/ golang 中，为了用 Oauth1.0a 访问所有的 API 而获得令牌- nasust life blog](http://nasust.hatenablog.com/entry/2016/12/14/194519)