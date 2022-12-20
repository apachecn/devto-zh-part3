# 如何通过 Heroku Review Apps (Rails)对每个拉请求使用不同的 MySQL 数据库

> 原文：<https://dev.to/mmyoji/how-to-use-different-mysql-database-per-pull-request-with-heroku-review-apps-rails-41li>

## 先决条件

*   Rails v5.2.x
*   Ruby v2.6.x
*   Heroku
*   MySQL (ClearDB)

## 总结

使用`CLEARDB_<colorname>_URL`查看每个 Heroku 应用程序

示例代码在这里:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【mmyo Ji】](https://github.com/mmyoji)/[【hreviewapp】](https://github.com/mmyoji/hreviewapp)

### 使用 ClearDB(MySQL)在 Heroku 上运行的示例 Rails 应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# Heroku 上运行的示例 Rails 应用程序

*   Rails v5.2.3
*   Ruby v2.6.3
*   MySQL (ClearDB)
*   Heroku

## Heroku 设置

1.  在 Heroku 上创建应用程序
2.  连接 GitHub 和资源库
3.  添加 ClearDB 插件
4.  添加`DATABASE_URL`环境变量

*   参见`config/database.yml`
*   复制`CLEARDB_DATABASE_URL`，但是将第一个`mysql://`替换为`mysql2://`
*   参考:[https://devcenter.heroku.com/articles/cleardb](https://devcenter.heroku.com/articles/cleardb)

1.  手动部署
2.  运行数据库迁移等。带 heroku CLI
    *   例如)`$ heroku run rails db:create -a <app_name>`

## Heroku 评论应用程序

1.  创建一条管线(如果没有)
2.  按照说明:[https://dev center . heroku . com/articles/github-integration-review-apps](https://devcenter.heroku.com/articles/github-integration-review-apps)
3.  如果你想对每个拉请求使用不同的数据库，请看[这个提交](https://github.com/mmyoji/hreviewapp/commit/7d727740cff709d194d76368642711f4017d8e62)
    *   ClearDB 为像`CLEARDB_PURPLE_URL`或`CLEARDB_NAVY_URL`这样的审查应用程序发布唯一的数据库 URL
    *   如果存在，请使用查看应用程序的 URL。

在 PostgreSQL 中，不知道这种数据库 URL 是否下发。

</article>

[View on GitHub](https://github.com/mmyoji/hreviewapp)

## 背景

我的一些开发团队希望在他们的开发过程中有一个不同的应用环境。一些人使用他们自己的暂存环境，另一些人没有，只是使用本地环境。

在这种情况下，Heroku Review Apps 是一个非常好的选择，但我不知道它是否可以根据每个拉取请求使用不同的数据库。您可能知道 Rails 应用程序开发经常会有数据库迁移，并且根据拉取请求分离数据库会让开发人员更开心:)

如果你使用 MySQL(对不起，我没有研究 PostgreSQL 的情况)，你可以做到这一点，我在这篇文章中解释了这一点。

## 设置

我在这里不讨论设置 heroku 应用程序、创建管道和设置审核应用程序。

它们并不难，只谈 ClearDB 有用的特性。

## CLEARDB_XXX_URL

我发现 ClearDB 为每个评论应用程序发布唯一的数据库 URL。

就像`CLEARDB_NAVY_URL`、`CLEARDB_PURPLE_URL`等。

我想我可以使用它，如果它设置在环境中，并且脏脚本在这里:

[https://github . com/mmyo Ji/hreviewapp/blob/master/config/database . yml # l54](https://github.com/mmyoji/hreviewapp/blob/master/config/database.yml#L54)

```
production:
  # ...
  url: <%= ((key = ENV.keys.find { |k| k =~ /^CLEARDB_([A-Z]+)_URL$/ && k != "CLEARDB_DATABASE_URL" }) && ENV[key].sub("mysql://", "mysql2://")) || ENV.fetch('DATABASE_URL') %> 
```

Enter fullscreen mode Exit fullscreen mode

这段代码很烂，但是你知道这里做了什么。

如果`CLEARDB_XXX_URL`存在，将字符串[替换成正确的格式](https://devcenter.heroku.com/articles/cleardb)，或者像普通 app 一样使用`DATABASE_URL`。

检查应用部署后，不要忘记运行`rails db:schema:load`或`db:migrate`。

参见:[https://github . com/mm yoji/hreview app/blob/master/app . JSON # L44](https://github.com/mmyoji/hreviewapp/blob/master/app.json#L44)

好了👋