# Git:简单统计，bash 风格

> 原文：<https://dev.to/rdeavila/git-estatisitcas-simples-bash-style-2p5l>

对你们项目的一些统计数据感兴趣吗？谁做了多少承诺？一周中的哪一天做了更多的承诺，一天中的什么时间？这里有一些统计数据。全部在 bash 脚本的一行(或两行或三行)中。

o 提交的等级，对于自动的，对于未提交的。

```
git shortlog  -ns 
```

Enter fullscreen mode Exit fullscreen mode

一个项目的承诺，一周中的每一天。

```
for i in Mon Tue Wed Thu Fri Sat Sun; do echo $( echo " $i: "; git shortlog  -n --format='%ad %s'| grep "$i " | wc -l);
done 
```

Enter fullscreen mode Exit fullscreen mode

专案的履约承诺(依每周日期)，从特定日期开始。有趣的是，比如说，当开发者组发生变化时，可以看到不同之处。如果要在某个日期前计算承付款项，请将“`--since`”改为“`--until`”。

```
for i in Mon Tue Wed Thu Fri Sat Sun; do echo $( echo " $i: "; git shortlog  -n --format='%ad %s' --since='2011-06-31'| grep "$i " | wc -l);
done 
```

Enter fullscreen mode Exit fullscreen mode

一个项目的承诺，每天的小时数。

```
for i in `seq -w 0 23`; do echo $( echo " $i:"; git shortlog  -n --format='%ad %s' | grep " $i:" | wc -l);
done 
```

Enter fullscreen mode Exit fullscreen mode

专案的履约承诺(依日期当日的时间)。这里也可以把`--until`换成`--until`。

```
for i in `seq -w 0 23`; do echo $( echo " $i:"; git shortlog  -n --format='%ad %s' --until='2011-06-31' | grep " $i:" | wc -l);
done 
```

Enter fullscreen mode Exit fullscreen mode