# Typescript 构造函数速记

> 原文：<https://dev.to/satansdeer/typescript-constructor-shorthand-3ibd>

这里有一件事，在 Typescript 中有一个从构造函数参数创建和分配类属性的简写。

假设你有下面的代码，假设你有类`User` :

```
class User {
  private name: string;
  private surname: string;
  private age: number;

  constructor(name: string, surname: string, age: number) {
    this.name = name;
    this.surname = surname;
    this.age = age;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用更短的语法来编写相同的类:

```
class User {
  constructor(
    private name: string,
    private surname: string,
    private age: number
  ) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下`Typescript`会自动生成 thore 属性。是的，两种定义将产生相同的 Javascript 代码:

```
var User = /** @class */ (function() {
  function User(name, surname, age) {
    this.name = name;
    this.surname = surname;
    this.age = age;
  }
  return User;
})(); 
```

Enter fullscreen mode Exit fullscreen mode

它不仅适用于`private`访问等级修改器，你也可以使用`public`或`protected`。

因此，您可以使用这种构造函数赋值技术来节省一些代码行。