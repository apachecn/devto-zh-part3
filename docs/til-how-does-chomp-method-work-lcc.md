# 蒂尔:怎么会。chomp 方法管用？

> 原文：<https://dev.to/rnrnshn/til-how-does-chomp-method-work-lcc>

当我在 Codecademy 上学习 Ruby 的时候，我遇到了很多方法，也学到了很多东西。但是因为我想掌握这门语言，所以我每天都花一些时间去钻研一些新的东西。今天，我想深入探讨:

## `.chomp`方法是如何工作的？

首先，记住 Ruby 是一种面向对象的语言，Ruby 中的一切都是对象。因此，当我们在一个值/对象上调用`.chomp`方法时，它会移除换行符。

实际上，`.chomp`所做的是删除字符串末尾的回车符。当你输入 *d e v t o* 时，一次输入一个字符，然后按回车键`gets`输入所有的字母和回车键。记住 Enter 只是 Ruby 中的另一个字符。

例如:

```
input = gets # get a string
Bem Vindo
=> "Bem Vindo\n" # when you hit the enter keyboard it adds a character 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们删除输入字符

```
puts input.chomp 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以在输入时立即删除它

```
input = gets.chomp # gets require a string as input and chomp will delete the line break that comes with that string 
```

Enter fullscreen mode Exit fullscreen mode

`gets`是你的用户输入。同样，很高兴知道 get 或 puts，意思是*获取字符串*或*将字符串*放入`puts`。这意味着这些方法只处理字符串。

我们甚至可以使用`.chomp`方法以那种方式删除一个字符串的子串。

```
str = "Hello"

str.chomp("lo") 
```

Enter fullscreen mode Exit fullscreen mode

### 资源:

什么是 Ruby
[Ruby 编程语言的帮助和文档。](http://ruby-doc.com/docs/ProgrammingRuby/)
[Ruby 中的一切都是对象](https://medium.com/@pk60905/everything-is-object-in-ruby-559475ce71dd)
[Chomp 方法- Ruby Docs](https://ruby-doc.org/core-2.2.0/String.html#method-i-chomp)

谢了。