# 递归算法练习:斐波那契数列生成器

> 原文：<https://dev.to/kaelscion/recursion-algorithm-practice-fibonacci-sequence-generator-3fa0>

> 更多精彩内容，请访问编码鸭博客:[ccstechme.com/coding-duck-blog](https://www.ccstechme.com/coding-duck-blog)

嘿，开发者社区！所以，我周四要去我家附近的一家公司面试。他们告诉我，递归算法的效率对他们来说很重要，我将接受这方面的测试。因此，我将发布一些我想出的解决 HackerRank 递归练习问题的方法，以供参考！下面是我在 Python3 中对斐波那契数列生成器的解决方案。我认为这很好，但我愿意接受改进的建议。我的测试标准是我的序列能多快找到第 200，000 个斐波那契数。这个解决方案在我的本地机器上用了大约 13 秒(用 cProfiles 测量)就完成了。我在 HackerRank 上找到的第二个最接近的 Python 解决方案在相同的设置下平均耗时约 30 秒。你们都是怎么想的？让我知道你的问题、批评和/或评论是什么！我期待着阅读它们！

```
def fibonacci(n):
    iterator = 1
    first_fib_num = 0
    second_fib_num = 1
    while iterator < n:
        added_to = first_fib_num + second_fib_num
        first_fib_num = second_fib_num
        second_fib_num = added_to
        iterator+=1
    return(added_to)

print(fibonacci(200000)) 
```

Enter fullscreen mode Exit fullscreen mode