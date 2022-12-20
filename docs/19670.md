# JavaScript:作用域介绍(函数作用域、块作用域)

> 原文：<https://dev.to/mingt/javascript-introduction-to-scope-function-scope-block-scope-d11>

### 什么是范围？

范围确定代码区域中变量或其他资源的可见性或可访问性。

### 全局范围

JavaScript 文档中只有一个全局范围。所有函数之外的区域被认为是全局范围，在全局范围内定义的变量可以在任何其他范围内被访问和修改。

```
//global scope
var fruit = 'apple'
console.log(fruit);        //apple

function getFruit(){
    console.log(fruit);    //fruit is accessible here
}

getFruit();                //apple 
```

Enter fullscreen mode Exit fullscreen mode

### 局部范围

在函数内部声明的变量成为函数的局部变量，并在相应的局部范围内被考虑。每个函数都有自己的范围。相同的变量可以在不同的函数中使用，因为它们被绑定到各自的函数，并且不相互可见。

```
//global scope
function foo1(){
    //local scope 1
    function foo2(){
        //local scope 2
    }
}

//global scope
function foo3(){
    //local scope 3
}

//global scope 
```

Enter fullscreen mode Exit fullscreen mode

局部作用域可分为**功能作用域**和**块作用域**。ECMA 脚本 6 (ES6)引入了块作用域的概念，以及声明变量的新方法- **const** 和 **let** 。

### 功能范围

每当在函数中声明变量时，该变量只在函数中可见。你不能在函数之外访问它。 **var** 是定义函数范围可访问性变量的关键字。

```
function foo(){
    var fruit ='apple';
    console.log('inside function: ',fruit);
}

foo();                    //inside function: apple
console.log(fruit);       //error: fruit is not defined 
```

Enter fullscreen mode Exit fullscreen mode

### 封锁范围

当循环时、**开关**满足条件或者、**满足**条件，则封锁范围为**内的区域。一般来说，无论什么时候看到**{花括号}** ，都是块。在 ES6 中， **const** 和 **let** 关键字允许开发者在块范围内声明变量，这意味着那些变量只存在于相应的块内。****** 

```
function foo(){
    if(true){
        var fruit1 = 'apple';        //exist in function scope
        const fruit2 = 'banana';     //exist in block scope
        let fruit3 = 'strawberry';   //exist in block scope

    }
    console.log(fruit1);
    console.log(fruit2);
    console.log(fruit3);
}

foo();
//result:
//apple
//error: fruit2 is not defined
//error: fruit3 is not defined 
```

Enter fullscreen mode Exit fullscreen mode

### 词法范围

另一点要提到的是词法范围。词法作用域意味着子作用域可以访问父作用域中定义的变量。子函数在词汇上与其父函数的执行上下文绑定在一起。

```
function foo1(){
    var fruit1 = 'apple';        
    const fruit2 = 'banana';     
    let fruit3 = 'strawberry';
    function foo2(){
        console.log(fruit1);
        console.log(fruit2);
        console.log(fruit3);
    }
    foo2();
}

foo1();

//result:
//apple
//banana
//strawberry 
```

Enter fullscreen mode Exit fullscreen mode

关于 **var** 、 **let** 和 **const** 的详细对比，看一下 [JavaScript: var，let，const](https://dev.to/sandy8111112004/javascript-var-let-const-41he) ！