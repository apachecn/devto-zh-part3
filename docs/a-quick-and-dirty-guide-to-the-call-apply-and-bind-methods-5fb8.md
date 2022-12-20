# 调用、应用和绑定方法的快速指南

> 原文：<https://dev.to/seanpryan/a-quick-and-dirty-guide-to-the-call-apply-and-bind-methods-5fb8>

***对老板们的高度概括:*** `call`，`apply`和`bind`是我们可以调用函数的方法，以使我们不那么受`this`关键字的残酷影响。

* * *

如果你在英国，你告诉某人乘电梯到二楼，他们会知道你在谈论使用在建筑物的不同楼层之间运送人的机器。在美国，听到同样指令的人最初可能会认为有人要背着他们上一段楼梯。

编写代码时也是如此——保留关键字根据使用它们的范围或执行上下文有不同的含义。在 JavaScript 中，最棘手的改变关键字含义的例子之一是`this`。

**什么是`this`？**
`this`关键字是一个指针，指向程序中的作用域或执行上下文。它所指的范围取决于它用在什么地方。当在声明对象之外使用时，`this`指向全局对象。(在浏览器中，全局对象实际上是`window`对象。如果您在检查器中打开控制台并键入`console.log(this)`，您将看到`window`对象及其所有记录的属性和方法。

`this`用在声明的对象内部，指最近的父对象。比如:

```
 const jim = {
      name: "Jim",
      age: 24,
      printAge: function() {
        return this.age
      }
    }

    const bob = {
      name: "Bob",
      age: 35,
      printAge: function() {
         return this.age
      }
    }

    jim.printAge() // returns 24
    bob.printAge() // returns 35 
```

像我们其他人一样，吉姆也在变老。让我们向`jim`对象添加另一个方法，该方法将获取他已经变老的年数，并返回他的新年龄:

```
const jim = {
  name: "Jim",
  age: 24,
  printAge: function() {
    return this.age
  },
  increaseAge: function increaseAge(years){
        return this.name + " is now " + (this.age + years) + " years old."
    }
}

const bob = {
  name: "Bob",
  age: 35,
  printAge: function() {
     return this.age
  }
} 
```

当我们调用`jim.increaseAge(5)`时，它会返回`Jim is now 29 years old`。但是如果我们想在`bob`对象上使用同样的方法呢？我们可以在`bob`中再次把它写出来，但是那是多余的。相反，当我们调用`increaseAge`时，我们将使用一些技巧来重新定义`this`，以便它引用`bob`对象，以及其中的`name`和`age`属性。

**`call` to the rescue**
`call`是一种方法，我们可以用它来指定关键字`this`在我们要添加的函数中所指的内容。我们传递给`call`的第一个参数被称为`thisArg`。这是我们希望函数中的`this`引用的对象。后面的参数只是我们想要传递给函数的常规参数。

使用`call`，我们可以在调用`jim.increaseAge`时访问`bob`对象的属性和方法:

```
const jim = {
  name: "Jim",
  age: 24,
  printAge: function() {
    return this.age
  },
  increaseAge: function increaseAge(years){
        return this.name + " is now " + (this.age + years) + " years old."
    }
}

const bob = {
  name: "Bob",
  age: 35,
  printAge: function() {
     return this.age
  }
}

jim.increaseAge.call(bob, 5) // returns "Bob is now 40 years old." 
```

当我们从定义函数的上下文外部调用函数时，`apply`允许我们控制`this`在函数内部引用什么。这是一种允许我们编写更加通用和可重用的代码的魔法。

**`apply`是`call`的稍高一级的实现哥哥**
像`call`，`apply`指定了函数中的`this`将在其第一个参数`thisArg`中接受的上下文。然而，`call`只能将预定数量的参数传递给一个函数，而`apply`将一组参数传递给一个函数，然后将这些参数解包并作为参数传递给该函数。

让我们来看看这是如何工作的:

```
const obj1 = {
    num: 5
}

const obj2 = {
    num: 2
}

const addToObj = function(a, b, c) {
    return this.num + a + b + c
}

let arr1 = [2, 3, 5, 6]
let arr2 = [4, 6, 3, 9]

console.log(addToObj.apply(obj1, arr1)) // logs 15 to the console

console.log(addToObj.apply(obj1, arr2)) // logs 18 to the console

console.log(addToObj.apply(obj2, arr1)) // logs 12 to the console

console.log(addToObj.apply(obj2, arr2)) // logs 15 to the console 
```

上面，我们将数组声明为变量，然后使用这些变量作为`apply`的第二个参数，将它们作为单独的参数传递给函数。使用`call`，我们将无法做到这一点，因为`call`要求函数参数作为常规的逗号分隔参数进行传递。

**`bind`方法:延迟满足**
`bind`是这个方法家族中的第三个，它允许我们在调用函数时重新定义`this`的上下文。虽然`call`和`apply`之间的差别很小，但是`bind`和`call`和`apply`之间的差别更大。

`bind`没有立即调用调用它的函数，而是返回一个函数定义，其中关键字`this`被设置为传递给它的第一个参数的值(T2)。当我们知道我们想要在一个函数中重定义`this`，但是我们不知道我们想要把哪些参数传递给这个函数时，这是非常有用的。

让我们回到`bob`和`jim`来看看`bind`是如何工作的:

```
var bob = {
   firstName: "Bob",
   sayHi: function(){
      return "Hi " + this.firstName
   },
   addNumbers: function(a,b,c,d){
      return this.firstName + " just calculated " + (a+b+c+d)
   }
}

var jim = {
   firstName: "Jim"
}

var jimAdd = bob.addNumbers.bind(jim, 1, 2, 3, 4)

jimAdd() // Jimm just calculated 10 
```

这里，我们在`bob`对象上使用了方法`addNumbers`，并在其上调用了`bind`方法来创建另一个函数，其中`this`被重新定义为`jim`对象。我们将这个新函数存储在变量`jimAdd`中，随后称为`jimAdd`。这是可行的，因为与`call`和`apply`不同，`bind`不会立即调用它所使用的函数。

**还不知道你的论点吗？用`bind`摆脱束缚。**
当我们使用`bind`时，我们并不总是需要知道我们正在创建的函数的参数——我们需要传递给`bind`的唯一参数是`thisArg`。

让我们再次回到吉姆和鲍勃:

```
var bob = {
   firstName: "Bob",
   sayHi: function(){
      return "Hi " + this.firstName
   },
   addNumbers: function(a,b,c,d){
      return this.firstName + " just calculated " + (a+b+c+d)
   }
}

var jim = {
   firstName: "Jim"
}

var jimAdd2 = bob.addNumbers.bind(jim, 1,2)
jimAdd2(3,7) // returns 'Jim just added 13' 
```

当我们用`bind`创建`jimAdd2`函数时，我们只向函数传递两个参数。当我们在下面一行中调用它时，我们传递两个额外的参数，并且用所有四个参数调用该函数。

**使用`bind`配合异步代码:**
`bind`方法的另一个常见应用是异步代码。

```
var bob = {
   firstName: "Bob",
   sayHi: function(){
      setTimeout(function(){
        console.log("Hi " + this.firstName)
      },1000)
   }
}

bob.sayHi() // Hi undefined (1000 miliseconds later) 
```

因为最接近`sayHi`方法的父对象是`bob`，所以`sayHi`返回`Hi Bob`是有意义的。然而，因为`setTimeout`是在稍后的时间点被调用的，所以它在执行点被附加的对象不是`bob`对象，而是`window`对象。

如果我们仍然希望在调用`setTimeout`函数时`this`的上下文是`bob`对象，`bind`提供了一个完美的解决方案。它允许我们显式设置`this`的上下文，而无需立即调用该函数:

```
var bob = {
   firstName: "Bob",
   sayHi: function(){
      setTimeout(function(){
         console.log("Hi " + this.firstName)
     }.bind(this),1000)
   }
}

bob.sayHi() // "Hi Bob" 
```

在这个例子中，我们实际上可以在`setTimeout`函数上将`this`作为`bind`的`thisArg`传入，因为在这个例子中，`this`等于`bob`对象本身。(我们可以很容易地传入`bob`，但是在这种情况下，我们经常会看到使用`this`而不是对象名的代码。