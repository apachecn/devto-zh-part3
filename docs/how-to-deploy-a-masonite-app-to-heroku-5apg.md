# 如何将 Masonite 应用程序部署到 Heroku

> 原文：<https://dev.to/kentaro0919/how-to-deploy-a-masonite-app-to-heroku-5apg>

# 1。添加 Procfile

将一个 [Procfile](https://devcenter.heroku.com/articles/procfile) 添加到 Masonite 项目的顶部。

我的档案是

```
web: gunicorn wsgi  --log-file - 
```

添加并提交

# 2。在 Heroku 制作新的应用程序

```
$ heroku create 
```

# 3。添加 git 远程

```
$ heroku git:remote -a {app-name} 
```

# 4。推上线

```
$ git push heroku master 
```

更多信息见[本](https://dev.to/masonite/deploying-a-masonite-application-to-heroku-45jp)