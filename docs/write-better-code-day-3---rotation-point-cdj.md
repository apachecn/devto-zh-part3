# 编写更好的代码:第三天-旋转点

> 原文：<https://dev.to/arjunrajkumar/write-better-code-day-3---rotation-point-cdj>

这篇文章最初出现在 Arjun Rajkumar 的博客上。Arjun 是印度班加罗尔的一名 web 开发人员。

-

### 第三天:问题 2

我翻开字典，翻到中间一页，开始翻看，寻找我不认识的单词。我把每个我不认识的单词放在一个我在内存中创建的大数组中，索引递增。当我到达词典的末尾时，我从头开始做同样的事情，直到我到达我开始的那一页。

现在我有一个单词数组，大部分是按字母顺序排列的，只是它们从字母表中间的某个地方开始，到达末尾，然后从字母表的开头开始。换句话说，这是一个已经被“旋转”的按字母顺序排序的数组例如:

```
words = [
    'ptolemaic',
    'retrograde',
    'supplant',
    'undulate',
    'xenoepist',
    'asymptote',  # <-- rotates here!
    'babka',
    'banoffee',
    'engender',
    'karpatka',
    'othellolagkage',
] 
```

Enter fullscreen mode Exit fullscreen mode

编写一个方法来寻找“旋转点”的索引，这是我从字典的开头开始工作的地方。这个数组很大(有很多单词我不认识)，所以我们想在这里提高效率。

-

如果你想继续下去，请在评论中发表你的答案。

我的答案在评论里。
[这个问题来自 InterviewCake。](https://www.interviewcake.com/question/ruby/shuffle?course=fc1&section=greedy)