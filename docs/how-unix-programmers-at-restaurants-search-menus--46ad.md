# 餐馆里的 Unix 程序员如何在菜单上搜索他们最喜欢的菜肴

> 原文：<https://dev.to/miguelmota/how-unix-programmers-at-restaurants-search-menus--46ad>

一个 Unix 程序员去当地的餐馆买午餐。他，或者他更喜欢的鲍勃，知道不要用他的眼球手动浏览整个菜单，因为这样效率很低。Bob 知道有一种更好的方法可以自动搜索他想吃的东西。

上次他在这里吃了一盘不到 10 块钱的虾通心粉。

鲍勃想知道这道菜是否还有。他打开运行 Linux 的笔记本电脑，将餐馆网站的菜单表抓取到一个纯文本文件中。

`menu.txt`

```
the menu

steak burrito $11.95
pasta and shrimp $9.75
caesar salad $6.50 
```

现在鲍勃有了菜单，他开始搜索意大利面和虾盘:

```
$ cat menu.txt | grep shrimp

pasta and shrimp $9.75 
```

到目前为止一切顺利。他通过使用`awk`和`NF`变量(这是可用字段的列数)来过滤价格列，以便获得包含价格的最后一列:

```
$ cat menu.txt | grep shrimp | awk '{print $NF}'

$9.75 
```

饥饿开始了，他现在正在拼命打字。Bob 通过替换除了美元符号和小数点之间的内容之外的所有内容，继续使用`sed`将美元金额转换为整数。他将使用一个捕获组并用第一个捕获组`\1`替换内容:

```
$ cat menu.txt | grep shrimp | awk '{print $NF}' | sed -E 's/\$([0-9]+)\..*/\1/g'

9 
```

最后，使用方便的`test`命令和小于标志`-lt`，Bob 可以在`xargs`的帮助下检查价格是否小于`10`，并将该值作为第一个参数传递给`test`:

```
$ cat menu.txt | grep shrimp | awk '{print $NF}' | sed -E 's/\$([0-9]+)\..*/\1/g' | xargs -I {} test {} -lt 10 
```

但是等等，没有输出！实际上，如果条件通过，`test`返回一个退出状态码`0`，如果不匹配，`1`。

如果前一个命令通过使用`&&`成功，Bob 简单地回显可用的*，否则如果不成功，他将通过使用双管`||`回显 *:(* ):*

```
$ cat menu.txt | grep shrimp | awk '{print $NF}' | sed -E 's/\$([0-9]+)\..*/\1/g' | xargs -I {} test {} -lt 10 && echo 'Available!' || echo ':('

Available! 
```

瞧啊。就是这样，想要的意大利面和虾盘仍然不到 10 美元。鲍勃很高兴，开始点他最喜欢的菜。

(希望你喜欢这篇介绍 unix 管道和 bash 命令的文章！)*