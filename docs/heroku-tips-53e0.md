# Heroku tips

> 原文：<https://dev.to/kentaro0919/heroku-tips-53e0>

# 我发现了将 [Heroku](//www.heroku.com) 与 Masonite 配合使用的技巧

显示应用程序版本。

```
$ heroku releases -a {appname} 
```

回滚到某个版本。

```
$ heroku rollback v53 -a {appname} 
```