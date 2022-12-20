# 一些小技巧

> 原文：<https://dev.to/lucifer1004/a-few-pip-tips-5328>

[![Photo by David Clode @ Unsplash](img/9e326aecb71def726c94d85f637fbe90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wEpK70y8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1546992772-3318f1f3a1be%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2010%26q%3D80)

我记录这些技巧主要是为了将来参考，但如果你也觉得这篇文章有帮助，我会很高兴。如果你有其他的点子，请评论，我会更新这篇文章。

## 1。安装特定版本或版本范围的软件包

具体版本:

```
pip install foo==1.2.0 
```

一个版本范围:

```
pip install 'bar>=1.3.2,<=1.5.4' 
```

如果已经安装了不同版本的相同软件包，则应使用`--force-reinstall`。

对于多个包，最好使用一个`requirements.txt`文件。语法和上面的单行版本一样。

```
foo==1.2.0
bar>=1.3.2,<=1.5.4
foobar 
```

然后就可以跑:

```
pip install -r requirements.txt 
```

## 2。下载软件包时使用镜像

一次性使用:

```
pip install -i <mirror-url> foo 
```

永久使用:

```
pip config set global.index-url <mirror-url> 
```

## 3。使用`python -m pip`代替普通`pip`

只要您使用正确的`python`，这将确保您使用正确的`pip`。

评论者:

[![k4ml image](img/a28a9a5b0832df414cc9078b7d577d59.png)](/k4ml)

## [卡迈勒·穆斯塔法](/k4ml)

[Python/Django Developer at xoxzo.com.](/k4ml)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)k4ml](https://twitter.com/k4ml)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)k4ml](https://github.com/k4ml)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)http://k4ml.me/](http://k4ml.me/)