# 写更好的代码:第 8 天-最短的路线

> 原文：<https://dev.to/arjunrajkumar/write-better-code-day-8---shortest-route-4ob4>

这篇文章最初出现在 Arjun Rajkumar 的博客上。Arjun 是印度班加罗尔的一名网络开发人员。

-

我发现这个很有趣...

问题:

给定网络上活跃用户的信息，找出消息从一个用户(发送者)到另一个用户(接收者)的最短路径。返回组成此路由的用户数组。

可能有几条最短的递送路线，都具有相同的长度。现在，让我们返回任何最短的路线。

您的网络信息采用散列的形式，将用户名字符串映射到附近的一组其他用户:

```
network = {
  'Min'     => ['William', 'Jayden', 'Omar'],
  'William' => ['Min', 'Noam'],
  'Jayden'  => ['Min', 'Amelia', 'Ren', 'Noam'],
  'Ren'     => ['Jayden', 'Omar'],
  'Amelia'  => ['Jayden', 'Adam', 'Miguel'],
  'Adam'    => ['Amelia', 'Miguel', 'Sofia', 'Lucas'],
  'Miguel'  => ['Amelia', 'Adam', 'Liam', 'Nathan'],
  'Noam'    => ['Nathan', 'Jayden', 'William'],
  'Omar'    => ['Ren', 'Min', 'Scott'],
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

对于上面的网络，一条从袁晓超到亚当的消息应该有这条路线:
`['Jayden', 'Amelia', 'Adam']`

-

如果你想继续下去，请在评论中发表你的答案。我的答案在评论里。[这个问题来自 InterviewCake。](https://www.interviewcake.com/question/ruby/second-largest-item-in-bst?section=trees-graphs&course=fc1)