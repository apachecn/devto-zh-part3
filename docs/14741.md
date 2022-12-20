# 用 bash 将字符串分成两部分

> 原文：<https://dev.to/adzubla/split-string-in-two-parts-with-bash-2i66>

我们可以使用变量替换来拆分一个字符串，使用一个字符作为分隔符:

```
STRING="x@y"

FIRST="${STRING%@*}"
SECOND="${STRING#*@}"

echo $STRING
echo $FIRST
echo $SECOND 
```

结果:

```
x@y
x
y 
```