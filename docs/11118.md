# 在 Python 中显示按关键字排序的嵌套字典内容

> 原文：<https://dev.to/flaviabastos/display-nested-dictionary-content-sorted-by-key-in-python-314l>

给定一个这样的嵌套字典:

```
dog\_breeds = { 'Labrador Retriever': {'life\_span': 14, 'male\_weight': '36 Kg', 'female\_weight': '32 Kg'}, 'Beagle': {'life\_span': 15, 'male\_weight': '11 Kg', 'female\_weight': '10 Kg'}, 'German Shepherd': {'life\_span': 13, 'male\_weight': '40 Kg', 'female\_weight': '32 Kg'}, 'Jack Russell Terrier': {'life\_span': 16, 'male\_weight': '8 Kg', 'female\_weight': '8 Kg'}, 'Rottweiler': {'life\_span': 10, 'male\_weight': '60 Kg', 'female\_weight': '48 Kg'}} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一种按键排序显示其内容的方法:

```
[print(f'A {key} can live up to {value["life\_span"]} years. A male can weight up to {value["male\_weight"]} and a female up to {value["female\_weight"]}.') for (key, value) in sorted(dog\_breeds.items()) ] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

让我们来分解一下上面的块中发生了什么:

```
print(f'Hello, {name}!') 
```

Enter fullscreen mode Exit fullscreen mode

*print(f)*正在使用[格式的字符串](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)，所以你可以在 print 语句中使用变量名。如果 name = '玛茜'，它会打印'你好，玛茜！'。Python 3.6 及更高版本中提供了这种格式化字符串。我使用这个 *all_the_time* ，因为它看起来非常类似于 JavaScript 的[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)(注意 JS 中的反勾号而不是单引号):

```
Console.log(`Hello, {name}`) 
```

Enter fullscreen mode Exit fullscreen mode

所以在上面的例子中，我们使用字典中的*键*和*值*作为变量。

接下来，循环的*:* 

```
for (key, value) in sorted(dog\_breeds.items()) 
```

Enter fullscreen mode Exit fullscreen mode

这是我们对字典进行排序并从*开始解构它的键和值的地方。物品()*。

我们还使用了[列表理解](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)(这就是为什么所有的东西都被方括号括起来，for 循环放在最后)但是这个代码块也可以这样写:

```
for (key, value) in sorted(dog\_breeds.items()): print(f'A {key} can live up to {value["life\_span"]} years. A male can weight up to {value["male\_weight"]} and a female up to {value["female\_weight"]}.') 
```

Enter fullscreen mode Exit fullscreen mode

最终的输出将是这样的:

```
A Beagle can live up to 15 years. A male can weight up to 11 Kg and a female up to 10 Kg.A German Shepherd can live up to 13 years. A male can weight up to 40 Kg and a female up to 32 Kg.A Jack Russell Terrier can live up to 16 years. A male can weight up to 8 Kg and a female up to 8 Kg.A Labrador Retriever can live up to 14 years. A male can weight up to 36 Kg and a female up to 32 Kg.A Rottweiler can live up to 10 years. A male can weight up to 60 Kg and a female up to 48 Kg. 
```

Enter fullscreen mode Exit fullscreen mode

*排序后的*还可以带一个‘反向’的自变量:

```
for (key, value) in sorted(dog\_breeds.items(), reverse=True) 
```

Enter fullscreen mode Exit fullscreen mode

这将反转输出:

```
A Rottweiler can live up to 10 years. A male can weight up to 60 Kg and a female up to 48 Kg.A Labrador Retriever can live up to 14 years. A male can weight up to 36 Kg and a female up to 32 Kg.A Jack Russell Terrier can live up to 16 years. A male can weight up to 8 Kg and a female up to 8 Kg.A German Shepherd can live up to 13 years. A male can weight up to 40 Kg and a female up to 32 Kg.A Beagle can live up to 15 years. A male can weight up to 11 Kg and a female up to 10 Kg. 
```

Enter fullscreen mode Exit fullscreen mode

> *帖子* [显示 Python 中按关键字排序的嵌套字典内容](https://wp.me/pa0b0y-3a) _ 最初发布于 _ [flaviabastos.ca](https://flaviabastos.ca/)