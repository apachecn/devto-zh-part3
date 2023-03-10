# 飞镖语言中的变量

> 原文：<https://dev.to/jrperdomoz/variables-en-dart-lang-4iom>

最近开始与使用编程语言 [Dart](https://www.dartlang.org) 的浮动开发 sdk 合作。这就是为什么我将撰写 Dart 和 float 文档的一些翻译，以及一些自己的帖子。我将直接从使用 Dart 变量开始。

fuente:[https://www . dartlang . org/guides/language/language-tour # variables](https://www.dartlang.org/guides/language/language-tour#variables)

下面是如何创建变量并将其初始化的示例:

```
var name = 'Jonatan'; 
```

变量存储引用。在此处，名为 **name** 的变量包含对值为【Jonatan】的对象 **String** 的引用。

推断变量 **name** 的类型为 **String** ，但可以通过指定来更改该类型。如果某个对象未被约束为单个类型，请按照[设计准则](https://www.dartlang.org/guides/language/effective-dart/design#do-annotate-with-object-instead-of-dynamic-to-indicate-any-object-is-allowed)指定该对象或动态类型。

```
dynamic name = 'Jonatan'; 
```

另一种选择是明确声明要推断的类型:

```
String name = 'Jonatan'; 
```

### 默认值

未初始化的变量具有初始值 *null* 。即使是数字类型的变量最初也是 *null* ，因为数字和 Dart 中的所有其他数据一样都是对象。

### 最终 y 常数

如果您从未打算更改变量的值，请使用 final 或 **const** ，而不是 var 或除了一种类型之外。一个最终变量只能配置一次；const 变量是编译时常量。(Const 变量隐式结束。)。最后一个顶级变量或类在第一次使用时初始化。