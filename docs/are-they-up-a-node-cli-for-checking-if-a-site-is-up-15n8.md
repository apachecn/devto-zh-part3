# 他们起来了吗？用于检查站点是否启动的节点 cli

> 原文：<https://dev.to/perkinsjr/are-they-up-a-node-cli-for-checking-if-a-site-is-up-15n8>

是一个可以全局安装的 npm 软件包，它可以检查是否有任何域启动。

## 我为什么要创建这个节点 cli。

我过去一直在开发一个特定领域的 api 框架，遇到了一个问题，就是他们宕机了，或者我不知道是我的互联网宕机了还是一个网站宕机了。

## 如何安装？

```
npm install aretheyup -g

//or this for single project

npm install aretheyup 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
如何使用

好消息是它超级简单，如下所示。回报要么是一张漂亮的绿色支票，上面写着肯定的是！或者一个写着“不”的红十字！如果你输入了错误的域名，你会得到一个错误提示，请检查域名，然后再试一次。简单但功能强大！

[![example](img/0fda0594a738276fe05194120a4974ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZRIcIn---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9qq47yg3zwrrbo96cq1.png)

## 他们的下一步是什么？

我计划添加响应时间，返回的 ip 和返回代码。因为它们在功能上是有用的。

我希望你喜欢它，如果你有任何问题，请通过 [github](https://github.com/perkinsjr/AreTheyUp/) 报告