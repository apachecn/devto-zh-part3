# 制作获取 Splatoon2 的舞台信息的命令的后续

> 原文：<https://dev.to/yutagoto/splatoon2--3jlc>

你好，.这边。

[之前](https://medium.com/@gggooottto/splatoon2%E3%81%AE%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E6%83%85%E5%A0%B1%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%82%92%E4%BD%9C%E3%81%A3%E3%81%A6%E3%81%84%E3%82%8B-108c3f248c1e)做了这样的东西，接下来是。

## 做的东西

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[yuta goto](https://github.com/YutaGoto)/[ika2 CLI](https://github.com/YutaGoto/ika2cli)

<article class="markdown-body entry-content container-lg" itemprop="text">

# ika2cli

[![CircleCI](img/133bfe1d9433559edace007d7a7ca55a.png)](https://circleci.com/gh/YutaGoto/ika2cli)[![Codacy Badge](img/4d81956843bcc50fbb210295b5cd53b9.png)](https://www.codacy.com/app/YutaGoto/ika2cli?utm_source=github.com&utm_medium=referral&utm_content=YutaGoto/ika2cli&utm_campaign=Badge_Grade)[![Maintainability](img/c22d3397da8a6b7c3c178412a7afc729.png)](https://codeclimate.com/github/YutaGoto/ika2cli/maintainability)

## 使用

以下命令可用。

检查 splatoon2 战斗阶段和规则或鲑鱼运行阶段和武器。

```
ika2cli
```

Enter fullscreen mode Exit fullscreen mode

### 选择

#### `-n`，`--next`

显示下一个条款信息。

#### `--mode value`，`-m value`

如果 value=salmon，则显示 Salmon-Run 阶段和武器

#### `-s` `--search`

搜索指定规则下一次开始的时间。

##### `-g` `--gachi`

分级匹配

##### `-l` `--league`

联赛

## 例子

```
$ ./ika2cli
2019/03/10 15:00 ~ 2019/03/10 17:00
ナワバリバトル, ステージ:ハコフグ倉庫 マンタマリア号
ガチマッチ:ガチアサリ, ステージ:アロワナモール ショッツル鉱山
リーグマッチ:ガチエリア, ステージ:Ｂバスパーク ホッケふ頭
$ ./ika2cli -n
2019/03/10 17:00 ~ 2019/03/10 19:00
ナワバリバトル, ステージ:モズク農園 アロワナモール
ガチマッチ:ガチエリア, ステージ:チョウザメ造船 ハコフグ倉庫
リーグマッチ:ガチアサリ, ステージ:アジフライスタジアム ホテルニューオートロ
$ ./ika2cli -m salmon
サーモンラン
2019/03/09 09:00 ~ 2019/03/10 21:00 現在開催中!
ステージ:シェケナダム
ブキ: ？, ？, ？, ？
$ ./ika2cli -m salmon -n
サーモンラン
2019/03/11 09:00 ~ 2019/03/12 15:00
ステージ:海上集落シャケト場
ブキ: スプラローラー, パブロ, .96ガロン, バレルスピナー
$ ./ika2cli -s -g zone
2019/03/10 17:00 ~ 2019/03/10 19:00
ガチマッチ:ガチエリア, ステージ:チョウザメ造船 ハコフグ倉庫
$ ./ika2cli -s -l tower
2019/03/10 19:00 ~
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/YutaGoto/ika2cli)

[![command](img/b4b93c36bfb16ca52bf95276bd56811d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G2LaQydA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fnkx00pr3rba3x2w0rur.gif)

## 使用的东西

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [阿列克托马斯](https://github.com/alecthomas) / [中枢人物](https://github.com/alecthomas/kingpin)

### 仅贡献:Go (golang)命令行和标志解析器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 仅捐款

**这是什么意思？**我没有时间自己解决问题。添加修复或新功能的唯一方式是人们提交 PRs。如果你有兴趣接管维修，并有历史贡献的金并，请让我知道。

**当前状态。**金并在很大程度上保持稳定。已经有一段时间没有添加新功能的需求了，但还是有一些 bug 需要修复。

**为什么？**我个人不再用金并(我现在用[孔](https://github.com/alecthomas/kong))。我相信这个通知会更清楚地设定期望，而不是让项目处于人们提交问题并想知道为什么他们没有工作的状态。

# 金并-一个 Go (golang)命令行和标志解析器

[![](img/f5fbcb5f2631ca7b39993c2286a12d05.png)](http://godoc.org/github.com/alecthomas/kingpin)[![Build Status](img/f7b28f187021dd22616902155136141a.png)](https://travis-ci.org/alecthomas/kingpin)[![Gitter chat](img/b29b1cde4d7c2e804b8d01b6b04d6980.png)](https://gitter.im/alecthomas/Lobby)

*   [概述](https://github.com/alecthomas/kingpin#overview)
*   [特性](https://github.com/alecthomas/kingpin#features)
*   [v1 和 v2 之间用户可见的变化](https://github.com/alecthomas/kingpin#user-visible-changes-between-v1-and-v2)
    *   定义后，标志可以在任何时候使用。
    *   [短旗可以](https://github.com/alecthomas/kingpin#short-flags-can-be-combined-with-their-parameters) …

</article>

[View on GitHub](https://github.com/alecthomas/kingpin)

以前用的是`cli`，但我改变主意重新做了。

[参考 https://qi ita.com/nira San/items/169 b 5767032 a 6243282 b](https://qiita.com/nirasan/items/169b5767032a6243282b)
的文章进行了选定。

## 这次追加的东西

*   可以取得鲑鱼兰最近两次的阶段和马口铁编成了。
*   现在可以检查指定的规则下一次什么时候开始。

这样会更方便吗？ (方便吗？ ）

## Feeling

逻辑本身给人一种很强硬的感觉，但因为想不出比这更好的检索方法，所以暂时妥协了。

三文鱼籽的命令在[YutaGoto/salmon_run](https://github.com/YutaGoto/salmon_run) 中非常有用。

重构不久就会完成。