# 编写更好的代码:第 6 天-合并会议时间

> 原文：<https://dev.to/arjunrajkumar/write-better-code-day-6---merge-meetings-times-1ak9>

这篇文章最初出现在 Arjun Rajkumar 的博客上。Arjun 是印度班加罗尔的一名网络开发人员。

-

### 第六天:问题 1

编写 merge_ranges()方法，该方法采用多个会议时间范围的数组，并返回压缩范围的数组。

```
For example, given:
[[0, 1], [3, 5], [4, 8], [10, 12], [9, 10]]
Output -> [[0, 1], [3, 8], [9, 12]] 
```

Enter fullscreen mode Exit fullscreen mode

不要假设会议是有序的。会议时间来自多个团队。

写一个有效的解决方案，即使我们不能给代表我们时间范围的数字一个好的上限。在这里，我们将时间简化为上午 9:00 后的 30 分钟时段。但是我们希望这种方法甚至可以用于非常大的数字，比如 Unix 时间戳。在任何情况下，挑战的精神是合并开始时间和结束时间没有上限的会议。

-

如果你想继续下去，请在评论中发表你的答案。

我的答案在评论里。
[这个问题来自 InterviewCake。](https://www.interviewcake.com/question/ruby/merging-ranges?section=sorting-searching-logarithms&course=fc1)