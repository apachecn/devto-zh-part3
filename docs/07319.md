# OO Ruby 概念第 1 部分，对象行为

> 原文：<https://dev.to/donnacamos/oo-ruby-concepts-i-object-behavior-308a>

Ruby 的面向对象编程对我来说很难理解。为了帮助我更好地理解 OO Ruby，我决定写一个迷你系列，专门讨论编程背后的概念。

我发现在学习任何东西的时候，最好是查阅单词本身的定义。什么是 Ruby 编程，它有什么用途？
我们来问问谷歌:

Ruby 是一种动态的、解释的、反射的、面向对象的通用编程语言。它是由日本的松本幸宏(Yukihiro“Matz ”)在 20 世纪 90 年代中期设计和开发的。根据创建者的说法，Ruby 受到了 Perl、Smalltalk、Eiffel、Ada 和 Lisp 的影响。

[Ruby(编程语言)-维基百科](https://en.wikipedia.org/wiki/Ruby_(programming_language))

Ruby 可以用来做什么？

它被广泛用于各种领域，但由于 Ruby on Rails 框架，它作为一种 Web 应用程序语言而广为人知。Ruby 的通用性质使它适合于各种编程任务，就像 Perl、Python 和其他通用语言一样。

[Ruby 语言是什么，用在哪个领域？- Quora](https://www.quora.com/What-is-the-Ruby-language-and-in-which-field-is-it-used)

太好了！现在我们知道了 Ruby 是什么以及它的用途，我们准备问下一个问题。什么是面向对象编程？请谷歌一下:

面向对象编程(OOP)是指一种计算机编程(软件设计)，其中程序员不仅定义了数据结构的数据类型，还定义了可应用于数据结构的操作(函数)类型。

什么是面向对象编程？网络百科定义

你可能会想，我只是在谷歌上查找资料。你是绝对正确的。知道如何使用谷歌是程序员可以发展的最好技能之一。通过经常使用它来真正精通它是一个好主意。

现在我们知道了 Ruby 是什么，它是用来做什么的，什么是面向对象编程，我们已经准备好开始编码了。我喜欢做的是从 Github、Codepen 或任何产生代码的地方取出一个代码块，并查看代码本身来分解它。有点像逆向工程，只有代码，你可以用注释来解释。

我不打算花时间解释 OO Ruby 的每个概念。仅仅是对象的行为以及如何在编程中使用它。让我们用一个带有对象`Cat`的代码块直接进入它，并让`Cat`做一些事情。让我们给它起个名字吧。然后我将开始分解对象行为的概念。

```
class Cat # Cat is the object 
   def name=(cat_name) # the 'name' variable is created here 
       @this_cats_name = cat_name # Setter: sets up the variable for the object 
   end 
   def name # this is now an instance variable. It can be called to create new Cats. 
       @this_cats_name # Getter: gets the variable for the object 
   end 
   def meow # this is a local variable and can only be used make the Cat object meow. 
       puts "Meow!" # no new cats can meow here 
   end 
   def come # no new cats can come. Don't worry they won't come anyway. 
       puts "I'll come when I'm ready!" 
   end 
end 

garfield = Cat.new
Cat.name = "Garfield" 

puts garfield.name # "Garfield" Congrats! You've made a new cat! 

garfield.meow # Garfield can't meow. Why? meow isn't an instance variable like 'name'.
cat.meow # "Meow!" Cat can. Why? meow is a local variable. Only Cat can use it. 
```

Enter fullscreen mode Exit fullscreen mode

这是很多代码的开始，所以让我们一次构建一个方法。首先是类方法，它作为构建对象本身的蓝图。

```
class Cat 
# method body where stuff is built for the Cat 
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的类方法中有了对象 Cat，让我们用实例方法写一个局部变量，让猫喵喵叫。

```
class Cat 
   def meow 
   puts "Meow!" 
   end 
end 

cat.meow # "Meow!" 
```

Enter fullscreen mode Exit fullscreen mode

我们通过在类方法中创建一个实例方法让猫喵了一声。实例方法让对象做一些事情。除非使用点符号调用该方法，否则猫不会喵喵叫。因此:

```
cat.meow # puts "Meow!" 
```

Enter fullscreen mode Exit fullscreen mode

这太棒了！只是现在我们需要给猫起个名字。因为局部变量只能作用于对象 Cat，所以我们需要找到一种方法，使用所有相同的实例方法来创建新的 Cat。否则，我们必须为每个新的 cat 重复所有的实例方法。因为我们是懒惰的程序员，所以对于我们想要创建的每一个新的 cat，最好是重用 Cat 中的所有方法。这是怎么做到的？输入实例变量:

```
class Cat 
def name=(cat_name) # the 'name' variable is created here 
       @this_cats_name = cat_name # Setter: sets up the variable for the object 
   end 
   def name # this is now an instance variable. It can be called to create new Cats. 
       @this_cats_name # Getter: gets the variable for the object 
   end 
end 

garfield = Cat.new
Cat.name = "Garfield" 

puts garfield.name # "Garfield" Congrats! You've made a new cat! 
```

Enter fullscreen mode Exit fullscreen mode

实例变量有两种方法。第一种方法接受一个参数，并将该参数设置为一个变量。第二种方法负责读取或获取名称。使用`@`符号调用实例变量。这保证了属性，或者在这种情况下，`name`可以用来给其他猫命名，而不仅仅是对象`Cat`。
实例变量基本上是对象属性的容器，这是[面向对象的 Ruby 概念第二部分](https://dev.to/donnacamos88/oo-ruby-concepts-ii-object-attributes-5708)的主题。