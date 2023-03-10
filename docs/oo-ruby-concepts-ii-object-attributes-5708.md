# OO Ruby 概念第 2 部分，对象属性

> 原文：<https://dev.to/donnacamos/oo-ruby-concepts-ii-object-attributes-5708>

上一篇[帖子](https://dev.to/donnacamos88/oo-ruby-concepts-i-object-behavior-308a)我讨论了对象及其行为。这篇文章将介绍一个对象的属性，以及如何操作它们来产生某种行为。
每个物体都有属性。为了弄清楚这一点，我在谷歌上查找了“属性”的定义，下面是它的说法:

at·trib·ute

动词 1。
认为某事是由(某人或某物)引起的。
“他把公司的成功归功于总经理的努力”

名词
1。被认为是某人或某物的特性或固有部分的品质或特征。
“灵活性和机动性是我们军队的关键属性”

让我们继续使用上一篇文章中的 Cat 类，使用“setter”和“getter”实例变量方法给它两个属性，name 和 breed。

```
class Cat # Object 
  def name=(garfield) #setter 
  @name = garfield 
  end 
  def name #getter 
      @name 
  end 
  def breed=(tabby) #setter 
     @breed = tabby 
  end 
  def breed #getter 
     @breed 
  end 
 end 

garfield = Cat.new("Garfield") 
garfield.name # "Garfield" 
```

Enter fullscreen mode Exit fullscreen mode

这个代码块包含“setter”和“getter”方法，这两个方法写入或设置属性，然后读取或获取对象要使用的属性。这段代码很长，很快就会变得重复，除非您需要定制它，否则有一种更好的方法来设置和获取属性。我们使用宏程序来完成这个任务。宏是一种允许程序员重用代码的工具。属性读取器和编写器是宏，它们只用两行代码就实现了上面的代码。

```
class Cat 
   attr_writer :name, :breed  #setter
   attr_reader :name, :breed  #getter 
end 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，效率更高。威廉·斯特伦克(William Strunk)在他的经典著作《风格的要素》(The Elements of Style)中说，**“省略不必要的词语”**。编程中的宏**省略了不必要的代码**，允许更加无缝地添加属性。属性 writer 和 reader 可以用属性访问器进一步浓缩，如下所示:

```
class Cat 
    attr_accessor :name, :breed # setter and getter all in one line
end 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们做一只新的猫，有名字和品种，让它“喵！”使用属性访问器。

```
class Cat 
    attr_accessor :name, :breed 
    def meow 
    puts "Meow!" 
    end 
end 

garfield = Cat.new 
Cat.name = "Garfield" 
garfield.name # Garfield 
garfield.breed = "Tabby" 
garfield.breed # Tabby 
garfield.meow # Meow! 
```

Enter fullscreen mode Exit fullscreen mode