# 正则表达式解决方案

> 原文：<https://dev.to/adityavarma1234/regex-solution-7ab>

问题描述:

Regex 需要接受 abc.xyz.mno 类型的所有字符串，它应该由多个用“.”连接的字符串组成。连接的字符串只能包含[a-z][0-9]，甚至不允许包含大写字符。

有效案例

ABC . BCD . edsaf . asdfds
ABC . asdf 123.1234 ADF
ABC . ad

无效案例

ABC
ABC。
ABC . 132 a【ASD】T3
1234 ASF。

我想到的解决方案是这个

```
^[a-z0-9]+\.[a-z0-9]([a-z0-9]*|\.[a-z0-9])+$ 
```

我不确定这是否是最有效的解决方案？