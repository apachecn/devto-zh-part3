# ES6 的好家伙。Let & Const。🤘

> 原文：<https://dev.to/dmitryvdovichencko/goodfellas-from-es6-let-const-9ma>

这是第一篇关于生活方式中 ES6 特性的文章。这个故事是关于出租和出租的😎。这两个家伙做了老人- Var。🕵
让我们来看看，它们在例子中是如何工作的。

[![Goodfellas](img/46fe85242e74eb6f7634ea2e387acf7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UERi3d7A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ihrk2pi38p7c56w3e56h.jpg)

现在是`let`，不要把他从街区里弄出来😎，不要在背后议论他。如果你想只为你的块，甚至为`loop`的每次迭代做一些变量，`let`可以帮你。

```
{ /* Don't even try to talk about me behind my back, it works with var, 
not with me.💪 If you try, you've got reference error, boom!💥*/ 
  console.log(varFromTheBlock);
    // ReferenceError
  let varFromTheBlock = "I'm Var from the Block, don't mess with me out from the block";
  // Cause you got reference error, dude, you don't want it.
  console.log(varFromTheBlock);
// "I'm Var from the Block, don't mess with me out from the block"
 }
  console.log(varFromTheBlock); //ReferenceError: varFromTheBlock is not defined

// let in for loop
  var arrForIteration = [];
  for (let i = 0; i < 5; i++){
    arrForIteration.push( function(){
      console.log( i );
    } );
  }
  arrForIteration[3]();//3 
```

现在是`const`💪，他长得像`let`，但更有原则，更保守。他像一块岩石。如果你想创造一些别人永远无法改变的东西，他可以帮助你。

```
{
  const bigConst = ['cars','weapons'];
 // Hey I'm bigConst and I work for some huge array.🕵
 bigConst.push('cash');
 // Yes I can talk with array if you want and bring him some 'cash' 💵
 console.log( bigConst ); // ['cars','weapons','cash']
  //  But don't try to broke my powerful connections with guys, which I'm working for!
  bigConst = 'plant'; //TypeError
  // I told you! It's typeError, man.
} 
```