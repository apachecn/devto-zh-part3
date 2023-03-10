# 了解模块

> 原文：<https://dev.to/edizle585/learning-about-modules-iio>

模块是可重用的代码片段，可以从一个程序中导出，并导入到另一个程序中使用。它们很有用，因为它使得修复、重用和保护代码变得更容易。这也有助于防止全局命名空间
我们在一个文件中定义一个 module.exports 等于正在导入的对象的模块。
——例子

```
Const self = aggregates.all();
Module.exports = self; 
```

或者
Const plane = { }
plane . parts = 4

```
Module.exports = plane; 
```

要使用导出的模块和其中定义的行为，可以用 require 函数导入模块。

-示例

```
const christianity  = require(‘self’);

Const airPort = require(‘plane’); 
```

您还可以将任何数据和函数的集合包装到一个对象中，并将其导出，使其等于 module.exports。

-示例

```
Module.exports = {
post-modernity:() 
```

= >回归怀疑论，
浪漫主义:()= >回归‘嘿爱默生，
}

您可以用导出默认值做同样的事情