# 类型脚本接口

> 原文：<https://dev.to/taylorhutchison/typescript-interfaces-55cf>

### 键入脚本接口

TypeScript 中的接口是一个有用的特性，我认为它既没有被充分利用，又被误解了。所以在这篇文章中，我将描述什么是接口，并与 C#中的接口进行对比，以突出一个重要的区别。

查看 [TypeScript 手册](https://www.typescriptlang.org/docs/handbook/interfaces.html)，接口被描述为给值的“形状”命名。如果你是计算机科学家或打字语言专家，这是一个很好的描述，但是对于新程序员来说，这个定义是不够的。

当 TypeScript 手册谈到“形状”时，它指的是作为对象一部分的属性和函数以及它们的推断类型。以定义为
的对象为例

```
const config = {
    log: true,
    user: 'example'
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个对象的形状有两个属性:一个名为 log 的布尔值和一个名为 user 的字符串。在 JavaScript 中，我们经常创建对象文字，并将它们传递给函数，函数检查对象以查看所需的属性是否存在。

约束一个函数使它只接受某些类型的一种方法是使用类，这对于任何有 OO 背景的人来说都是熟悉的。您创建一个具有某些属性的类，然后用 new 关键字实例化该类。这种方法有很多好处，但并不总是如此。

如果您喜欢在 javascript 中使用对象文字，或者您主要从外部 API 获取数据，并且您不需要“新建”对象，那么接口是为您的应用程序添加类型安全的首选方法。

举这个例子:

```
 interface INamed {
    name: string;
}

function sayHello(INamed object) {
    console.log(`Hello, my name is ${object.name}`);
}

const person = {
    name: 'Taylor',
    profession: 'Software Developer'
}

const country = {
    name: 'England',
    area: 50346
}

sayHello(person);
sayHello(country); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，使用了一个接口来约束传递给 sayHello 函数的类型。对象只需包含 string 类型的 name 属性就可以被传入。

最重要的是:我们不需要声明 person 或 country 对象来实现 INamed 接口。它之所以有效，仅仅是因为它们具有那些属性，并且 typescript 编译器能够计算出它们通过了“它是否具有字符串类型的名称属性”测试。

所以总而言之，除非你绝对需要类，否则一定要使用接口。