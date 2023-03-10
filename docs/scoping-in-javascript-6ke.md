# Javascript 中的作用域

> 原文：<https://dev.to/coderoo7/scoping-in-javascript-6ke>

嗨，我写这第一篇文章是为了提高我的 Javascript 知识。如果我有什么地方错了，请帮助我改正我的错误...要了解 javascript 如何在幕后工作，范围界定是必要的。

### 范围界定

它决定了代码中变量、函数和对象可以被访问的区域或范围，决定了代码中变量的可见性。

### 需要界定范围

作用域使您的代码安全，因为用户可以访问当时需要的数据。这也有助于减少程序中错误，并解决当我们有两个同名但作用域不同的变量时的变量名解析问题。

### 范围类型

**全局范围**
全局意味着程序中的任何地方都可以访问。写在函数之外的代码在全局范围之下意味着函数之外定义的变量可以在程序的任何地方被访问，这个变量在整个程序中都是可见的。

```
//GLOBAL SCOPE
var x = 10;  
console.log(x); // log '10'

function a(){
    //LOCAL SCOPE
   return x;  // 'x' is accessible here also and everywhere else in code
}
console.log(a()); 
```

注意:全局作用域是 javascript 引擎默认创建的唯一一个作用域，在 javascript 中我们不能有多个全局作用域。

**局部作用域**
函数内部的变量或代码在局部作用域内。也称为函数作用域，因为作用域是该函数的局部作用域，局部作用域仅在调用函数时创建，并且每个函数调用都为该函数创建新的局部作用域。这为我们在不同的函数中定义同名变量提供了便利。

```
//GLOBAL SCOPE
var x = 10;  
console.log(x); // log '10'

function a(){
   //LOCAL SCOPE
   var x = 23 ;   // new local varaible 'x' with same name
   var y = 40 ;   // new local variable 'y' 
   console.log(x);  // log '23' 

}

a(); // function a() scope is created when it's call.

console.log(x); //log '10'
console.log(y); // Reference Error Unexpected identifier; 
```

请记住，我们不能在外部作用域中访问函数中定义变量，因为函数作用域是在调用时创建的，一旦函数返回就被销毁。

如果我这样做呢

```
var x = 10;  
console.log(x); // log '10'

function a(){
   //LOCAL SCOPE
   x = 23;      // Now we are changing the value of global variable 'x' 
   y = 40;      // new global variable 'y'
   console.log(x);  // log '23' 
}

a(); 

console.log(x); //log '23'
console.log(y); //log '43' 
```

每当我们在没有使用' var '关键字的情况下给变量赋值或创建新变量时，我们都在改变全局变量的值或创建一个新的全局变量。

### 封锁范围

在 Javascript 中，花括号之间的任何内容都称为块。下面是块范围语句，在块内包含零个或多个语句。
例如:

```
{
  // new block scope
}

if(true){
  // new block scope
}

while(true){
  //  new block scope 
}

for(;;){
    // new block scope
}

function(){
    //new block scope
} 
```

在没有定义任何块的情况下, **var** 花括号意味着在块中使用 var 关键字声明的变量是全局的。

```
var num = 1;

{//#1 block scope
    {//2 block scope 
        var num = 2; 
    }
}
console.log(num); //log '2' b/c var num=1 and  var num=2 are in same scope.

if(true){
    var num =3;
}
console.log(num); //log '3'

for(var num=4; num<5;){

}
console.log(num); //log '4' 
```

但是函数作用域变量对于函数来说是局部的，这意味着在函数内部创建的变量被绑定到函数上，在函数作用域之外不能被访问。怎么会？我已经在上面的局部作用域部分解释过了。

不管 ES6 **中的**变量**让**和**常量**允许在块范围内创建局部范围变量。意味着现在变量被绑定到定义它们的作用域，在这个作用域之外不能被访问。

```
const num = 1;

{//#1 block scope
    console.log(num); log '1' b/c num is global

    {//#2 block scope 
        let name = "Shubham";  
    }

    console.log(name) // ReferenceError: name is not defined
} 
```

记住不像 **var** **let** 不能重新声明但是可以重新分配，而 **const** 只能声明一次并且不能重新分配。

这是 Javascript 中关于作用域的所有基础知识。但是你应该知道 javascript 是词法范围的语言。然后这里出现了另一个术语词法范围。

**词法范围**
词法范围定义在嵌套块或嵌套函数的情况下如何解析变量名，或者词法范围意味着在嵌套块组中——范围(嵌套函数)内部块可以访问其父块的变量和其他资源。

```
 //Global Scope
    let i=1;

    {//#1 local scope

        let i=2;

        {//#2 local scope

            console.log(i); //log '2' not '1'?

        }
    } 
```

现在读这一切，我们都知道“我”将是 2，但你为什么想知道呢？这是因为在词法范围的情况下，内部块将首先在其块范围内搜索变量，然后在外部块范围内搜索，直到到达全局范围。
由于这种从内部块到外部块搜索变量的层次结构，创建了一个作用域链，称为**作用域链**，我们得到 I 为‘2’。

在功能
的情况下

```
//Global Scope
var x = 10;

function foo2(){
//Local Scope
    return x; // return '10' not '20'
}

function foo1(){
//Local Scope
    var x = 20;
    return foo2();
}

console.log(foo1()); //log '10' 
```

问题是哪个“x”值 foo2()将返回它被调用或定义位置。你知道答案，你只需要遵循词法作用域的作用域规则。

通过查看代码 foo2()被 foo1()调用，然后 javascript 引擎查看 foo2()的定义，其中它返回“x”。首先，js 引擎在 fun2()范围内搜索 foo2()值，然后在外部范围内搜索，外部范围是全局范围，其中 x 是 10。这就是为什么输出是 10。

记住在词法作用域中，作用域层次结构是从上到下的，这意味着最上面的外部块变量可用于内部块，反之则不成立。

这都是关于范围的。我希望我能很好地解释它，也许我在某些地方错了，那么请告诉我我的错误，这样我就能从中吸取教训。谢谢你....