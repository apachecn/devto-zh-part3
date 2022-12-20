# 编写更好的代码:第 4 天-排序分数

> 原文：<https://dev.to/arjunrajkumar/write-better-code-day-4---sorted-scores-2226>

这篇文章最初出现在 Arjun Rajkumar 的博客上。Arjun 是印度班加罗尔的一名 web 开发人员。

-

### 第四天:问题 2

编写一个方法，该方法采用:

游戏
中未排序分数和最高可能分数的数组，并在不到 O(nlgn)时间内返回排序分数数组。

```
For example:
unsorted_scores = [37, 89, 41, 65, 91, 53]
HIGHEST_POSSIBLE_SCORE = 100

sort_scores(unsorted_scores, HIGHEST_POSSIBLE_SCORE)
# returns [91, 89, 65, 53, 41, 37] 
```

Enter fullscreen mode Exit fullscreen mode

我们将 nn 定义为未排序分数的数量，因为我们预计玩家数量会继续攀升。

而且，我们将把最高分视为一个常数，而不是把它纳入我们的时间和空间成本，因为最高分不会改变。即使我们稍微重新设计一下游戏，分数也会保持在相同的数量级。

-

如果你想继续下去，请在评论中发表你的答案。

我的答案在评论里。
[这个问题来自 InterviewCake。](https://www.interviewcake.com/question/ruby/top-scores?course=fc1&section=sorting-searching-logarithms)