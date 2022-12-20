# TIL: JavaScript 对象&访问属性

> 原文：<https://dev.to/vickilanger/til-javascript-objects-accessing-properties-3abp>

今天，我创建了我的第一个 JavaScript 对象！

液体错误:内部

```
var myDog = {
  "name": "Cheeto",
  "enemies": "Puppy the cat"
}; 
```

Enter fullscreen mode Exit fullscreen mode

我也能够用点和括号符号
访问属性

```
>var nameval = myDog.name; // "Cheeto"
>var enemiesval = myDog.enemies; // "Puppy the cat" 
```

Enter fullscreen mode Exit fullscreen mode

```
var nameValue = myDog["Name"]; // Cheeto
var enemiesValue = myDog['enemies']; // Puppy the cat 
```

Enter fullscreen mode Exit fullscreen mode