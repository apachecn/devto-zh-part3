# 理解“this”在 Javascript 默认绑定中的工作原理

> 原文：<https://dev.to/kenystev/understanding-how-this-works-in-javascript---default-binding-56m4>

我们在之前的文章中已经看到,`this`是根据调用的位置绑定的，如果你没有读过，请回到

[![kenystev image](img/8e922927a777aeb7c7a6b94a4f7646dd.png)](/kenystev) [## 理解“this”在 Javascript -调用点中是如何工作的

### 凯文·埃斯特维斯

#javascript#programming#ignoredconcepts#gettingdeeper](/kenystev/understanding-how-this-works-in-javascript---the-call-site-3hj3)

我首先要说的是，默认绑定是最后一种情况，在任何其他情况下都匹配。

当应用默认绑定时，**全局对象**将被绑定到被调用的函数，考虑下一个代码:

```
function foo() {
    console.log(this.a);
}

function bar() {
    function bazz() {
        console.log(this.b);
    }
    bazz();
}

var a = 3;
var b = 5;

foo();  // 2
bar();  // 5 
```

值得指出的是，只要`strict mode`不设置在被调用函数的定义范围之内或之外，那么**全局对象**就是合格的，但是如果`'use strict'`设置在其中之一，那么`this = undefined`。

`'use strict'`内部函数的定义

```
function foo() {
        'use strict';
    console.log(this.a);
}

var a = 3;

foo();  // TypeError: Cannot read property 'a' of undefined (this = undefined) 
```

`'use strict'`函数定义之外

```
function bar() {
    'use strict';
    function bazz() {
        console.log(this.b);
    }
    bazz();
}

var b = 5;

bar();  // TypeError: Cannot read property 'b' of undefined (this = undefined) 
```

值得指出的是，尽管投标完全基于*调用点*，但`'use strict'`关注点完全基于函数本身的声明位置。

```
function foo() {
    console.log(this.a);
}

var a = 3;

(function(){
    'use strict';    
    foo();  // 3
})(); 
```