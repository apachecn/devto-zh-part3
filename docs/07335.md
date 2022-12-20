# TIL-Unix shell 有一个 RANDOM env 变量

> 原文：<https://dev.to/parambirs/til-unix-shells-have-a-random-env-variable-14f2>

曾经想给你的 shell 脚本增加一些随机性吗？不，我不是说让他们变得古怪！

Unix shells 提供了一个`$RANDOM`环境变量，我们可以在 shell 命令或脚本中使用它:

```
% echo $RANDOM 
21290
% echo $RANDOM
8367 
```

随机数发生器的*种子*可以更改如下:

```
% RANDOM=12345
% echo $RANDOM
5758 
```

### 生成一个范围内的随机数

我们可以使用`awk`来生成给定范围内的随机整数。例如，要生成 1 到 10(包括 1 和 10)之间的随机数:

```
% awk -v seed=$RANDOM 'BEGIN{srand(seed); print int(rand()*10+1)}'
10
% awk -v seed=$RANDOM 'BEGIN{srand(seed); print int(rand()*10+1)}'
4
% awk -v seed=$RANDOM 'BEGIN{srand(seed); print int(rand()*10+1)}'
1 
```

#### 更多详情:

*   [http://sru faculty . sru . edu/David . dailey/UNIX/random _ numbers . htm](http://srufaculty.sru.edu/david.dailey/unix/random_numbers.htm)