# 带有 Postgresql 的 Rails

> 原文：<https://dev.to/saral/rails-with-postgresql-p0l>

[![Postgresrails](img/8fa47498774dd5a18ec4c08368ff3d94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XU8Z2WZ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.plataformatec.com.br/wp-content/uploads/2014/07/postgresql-ruby-on-rails.png) 
上次[项目](https://threeirblog.herokuapp.com)的时候，我在给 heroku 部署 app 的时候才知道 heroku 支持 postgresql。然后，我将数据库从 rails 附带的默认 sqlite 更改为 postgresql。

对于我正在着手的项目，我决定从使用 postgresql 开始。

我用通常的方式`rails new (appname)`创建了这个应用程序，然后继续修改数据库细节。在`database.yml`文件中，我将默认细节改为

```
default: &default
  adapter: postgresql
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  timeout: 5000

development:
  <<: *default
  database: app_development

test:
  <<: *default
  database: app_test

production:
  <<: *default
  database: app_production 
```

我还将`gem pg`添加到`gemfile`中，并运行`bundle install`来安装 postgresql。

完成后，我运行创建数据库的`rails db:create`。然后我创建了我的用户模型。这一次我将使用 [`devise` gem](https://rubygems.org/gems/devise) 进行用户认证过程。最后，我跑完了`rails db:migrate`，我的模型准备好了。

所有这些都完成后，我发现如果我在用`rails new app --database postgresql`生成我的应用程序时提到了我将使用的数据库类型，我可以跳过更改`database.yml`文件的过程。

我今天学到了什么吗？是的，我做了，尽管我今天没有登录很多编码小时。我打算明天开始使用 device，所以我明天的帖子将会是关于如何开始使用 devise gem 的。