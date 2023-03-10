# OO Ruby 概念第 3 部分，对象关系

> 原文：<https://dev.to/donnacamos/oo-ruby-concepts-part-3-object-relationships-14po>

[![alt text](img/e95d5b31c35c84219d43199884afa33d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XrPscg1P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vignette.wikia.nocookie.net/playstationallstarsfanfictionroyaimg/9/90/BF7_garfieldoddie.jpg/revision/latest/scale-to-width-down/200%3Fcb%3D20130425191848)

上一篇[文章](https://dev.to/donnacamos88/oo-ruby-concepts-ii-object-attributes-5708)我们给了一个对象属性，这样它就可以成为自己的实体。但是我们做的这个物体，加菲猫，是不能和其他任何物体互动的。这就是对象关系的由来。如果对象正在建模真实世界的实体，它们需要有一个方法来知道它们属于谁。这种方法叫做“归”。让我们从构建“加菲猫”对象开始。

```
 class Cat #object 
     attr_accessor :name # gives the object a name

   def initialize(name) # Initialize method means the name attribute is called when #new is called 
     @name = name 
   end 
end 

garfield = Cat.new("Garfield") 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经做了一个猫对象，加菲猫，我们需要给他一个主人。还有谁会喂他吃千层面？

```
 class Owner #object 
     attr_accessor :name, :job # attributes about the object 

  def initialize(name, job) # Initialize method means the name and job attributes
    @name = name            # are called when #new is called. 
    @job = job 
  end 
end 

 jon = Owner.new("Jon", "cartoonist") # new method names the Owner, Jon, 
                                      # and gives him a job, cartoonist 

 garfield.owner = jon # Congrats! Now Garfield belongs to Jon. 
```

Enter fullscreen mode Exit fullscreen mode

现在加菲尔德知道他属于乔恩。然而，如果乔恩想要另一只宠物呢？在现实世界中，他可以想养多少宠物就养多少。他决定养一只狗。我们怎么再给乔恩一只宠物？

同一个方法叫做“有许多”关系。它允许一个对象拥有许多对象，并相互关联。在本例中，它允许所有者 Jon 拥有两只宠物。如果他想要的话还会有更多。

```
 Class Owner 
   attr_accessor :name, :job 

 def initialize(name, job) 
   @name = name 
   @job = job 
   @pets = [] # this empty array is where all Jon's pets will go 
 end 

 def add_pet(pet) # this method allows Jon to add as many pets as he wants
   @pets << pet # the shovels(<<) push the pet class we've created into the array
 end 

 def pets # this method simply returns the @pets array when it is called
   @pets 
 end 
end 

 jon.pets # => ["Garfield", "Odie"] this is the pets array when it is called 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要创建一个名为 Pet 的对象，当这个方法被调用时，我们可以用它给 Jon 更多的宠物。

```
 class Pet 
       attr_accessor :name, :species  

   def initialize(name, species)
    @name = name 
    @species = species 
   end 
end 

  odie = Pet.new("Odie", "dog") # "Odie" is the name, "dog" is the species
  garfield = Pet.new("Garfield", "cat") # Congrats! Jon can now adopt his two pets. 
```

Enter fullscreen mode Exit fullscreen mode

还有一种方法可以将所有这些结合在一起。self 关键字指的是我们正在访问的所有者。

```
 pet.owner = self 
```

Enter fullscreen mode Exit fullscreen mode

将这个添加到`add_pet`方法中，这样我们就可以调用`self`关键字来让宠物知道谁是它的主人。

```
 Class Owner 
   attr_accessor :name, :job 

 def initialize(name, job) 
   @name = name 
   @job = job 
   @pets = [] 
 end 

 def add_pet(pet) 
   @pets << pet 
   pet.owner = self # self keyword 
 end 

 def pets 
   @pets 
 end 
end 

 jon.add_pet(garfield) # calling the add_pet method 

 garfield.owner.name # => "Jon", Garfield knows Jon is his owner 

 jon.add_pet(odie) 

 odie.owner.name # => "Jon", Odie knows Jon is his owner 

 # Now Jon has two pets and they know they belong to him! 
```

Enter fullscreen mode Exit fullscreen mode