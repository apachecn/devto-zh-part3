# TypeScript 的完整介绍指南

> 原文：<https://dev.to/borrellidev/a-complete-introduction-guide-to-typescript-4a66>

## 

随着 TypeScript 的力量在过去几年中不断增强，成千上万的开发人员决定开始使用这个 [Javascript 超集](https://thecoderswag.com/you-absolutely-have-to-learn-typescript-in-2019/)来增强他们代码的能力。本指南旨在成为所有想学习如何使用 TypeScript 并在他们的下一个项目中使用它的开发人员的快速入门。

## #1 单词 Types 的意思是:使用它们！

TypeScript 的一个最大的特性是**编译时类型检查**以防止任何**不匹配**你正在为你的变量使用的类型，是的，你实际上可以在 TypeScript 中使用类型，这里有一些关于你如何使用它们的例子:

```
// legal
let isReady : boolean = false; 
let decimal : number = 20;     
let name    : string = "Dev.to";
let numbers : number[] = [1,2, 3]; 

// illegal
let isReady : boolean = 10; 
let decimal : number = "not a number";     
let name    : string = true;
let numbers : number[] = "not an array of numbers"; 
```

### #1.1 我可以对我的变量使用多种类型吗？

当然你可以，通过简单地为你的一个变量使用 **any** 类型，你将能够**分配不同的值类型**，比如:

```
let unknown : any = 30; 
unknown = "what is this variable?";
```

如果你想要**限制你可以分配**给你的变量的类型，你最终可以像这样使用管道操作符:

```
let multiple : boolean | number = 10; 
multiple = true; // still valid
```

### #1.2 如果不想指定变量的类型怎么办？

没问题！TypeScript 支持隐式和显式类型化。在第一种情况下，您将精确地指定变量的类型，就像我们到目前为止所看到的那样，在第二种情况下，**每当您第一次初始化变量** a 值时，类型将自动分配给变量，这种机制更好地被称为**类型推断**。

```
let explicit : number = 10; // explicitily using the type 'number'
let implicit = 10; // inference of the type 'number'
```

注意类型推断在其他有用的情况下是如何变得方便的，比如函数返回值:

```
// inference will set 'number' as the return type for this function
function add(a: number, b: number) {
    return a + b;
}
```

### #1.3 我可以检查变量的类型吗？

想确定你用的是正确的类型吗？合适的班级？您可以像这样使用操作符的**实例来使用它:**

```
import { Cake } from './cake.model'; 
let cake = new Cake('eggs', 'milk');

if (cake instanceof Cake) {
    console.log("We've got a cake here!"); 
}
```

这对于**用户定义的类型**特别有用，当你从另一个对象继承属性时，它也能工作。

### #1.4 我可以转换变量的类型吗？

这种类型的操作通常被称为**强制转换**，它可以在 TypeScript 中的特殊情况下执行，我们可能需要使用特定类型来处理变量。让我们假设你定义了一个类型为 **any** 的变量，但是你现在想在这个变量**上使用一些普通的**字符串**函数，你现在不能访问这些函数**因为这个类型不是字符串，你可以告诉 TypeScript 这样处理这个变量:

```
let unknown : any; 
let unknown = "hello"; 

console.log("length is : ", (<string>unknown).length); 
```

## #2 使用阵列

当涉及到在 TypeScript 中使用数组时，上面提到的所有内容都非常适用:

```
// legal
let numbers : number[] = [1, 2, 3]; 
let strings: string[] = ["hello", "good", "world"]; 
let booleans : boolean[] = [true, false, true]; 
let whatever: any[] = ['Hello World', 10];

// illegal 
let numbers : numbers[] = [1, true, 3]; 
let strings: string[] = ["hello", 1, "world"]; 
let booleans : boolean[] = [true, 100, true]; 

// other possibilities include
let numbersAndBooleans = (number | boolean) = [true, 100]; // using the pipe operator
let matrix = number[][] = [[10, 20]]; 
```

### #2.1 使用元组

元组类型是一个全新的概念，它允许您表达一个数组，其中固定数量的元素的类型是已知的，但不需要相同。考虑一下，如果您想将一个值表示为一对布尔值和一个数字:

```
// Using a tuple
let x: [boolean, number];
x = [true, 10]; // initialization
x = [10, "hello"]; // illegal initialization
```

### #2.2 我真正错过的东西:Enums！

这个对 Javascript 的伟大补充是我过去用 Java 编码时真正缺少的东西，枚举基本上是一组命名的常量。枚举有三种类型:

*   数字枚举
*   字符串枚举
*   异构枚举

为了不使这篇文章太长，我不会对枚举进行太多的描述，只要记住，如果您想更好地记录您的意图或者创建一组不同的情况，比如:

```
enum Direction { 
    Up = 1,
    Down,
    Left,
    Right,
}
movePlayer(Direction.Up); 
```

## #3 物件呢？

对象是 Typescript 的另一个重要部分，让我们通过一个例子来更详细地分析它们:

```
// Javascript definition
let user = {
name: "piero", 
surname: "borrelli"
}

// Typescript definition is the same
let user = {
name: "piero", 
surname: "borrelli"
}

// except that now name and surname will be considered as
{name: string, surname: string}
```

我们拥有的对象的两个属性被推断为 string 类型，这意味着任何向它们分配不同类型的值的尝试都是无效的:

```
    user.name = 35; // invalid

```

### #3.1 对象类型

对象是不属于原始类型类别的类型，如:布尔型、字符串型、数字型、符号型、空型、未定义型，它遵循以下语法:

```
    let user: { name: string, surname: string};

```

## #4 功能

当谈到函数时，Typescript 引入了在处理函数时使用类型的可能性，例如，我们首先希望将它们用于函数参数:

```
// define types for the function parameters
function test(name: string, surname: string) {
 return name + " " + surname;  
}

let fullName = test("piero", "borrelli"); // legal
let fullName = test(10, "borrelli"); // illegal
```

另一个可能需要指定类型的地方是从函数返回值的时候。注意，在上面的函数中，返回类型被自动推断为 string 类型。让我们看看如何使用以下方法显式定义函数的返回类型:

```
// define return type for the function
function test(name: string, surname: string): string {
 return name + " " + surname;  
}

// illegal
function test(name: string, surname: string): string {
 return name.length; // will return a number here which is not expected  
}
```

## #5 面向对象部分

自从 ECMAScript 6 发布以来，Javascript 程序员已经能够使用面向对象的方法构建他们的程序。Typescript 也支持这种方法，所以让我们通过一些例子来分析我们将如何使用它:

```
class Point{
    x: number;
    y: number; 
    constructor(x: number, y: number) {
        this.x = x; // where 'this' refers to the current object
        this.y = y; 
    }
    getPoints() {
        return "x: " + this.x + " y: " + this.y;
    }
}

let Point = new Point(10, 20);
```

对于大多数使用过 C#或 Java 等语言的人来说，这看起来非常熟悉，我们有一个名为 Point 的类，它有两个成员 x 和 y，我们可以自由地访问它们(稍后会详细介绍)，我们还调用了一个名为 getPoints()的类方法。然后，我们可以使用 new 关键字创建 Point 类型对象的实例。

## 使用访问修饰符

因为这是一个完全不同的主题，所以不要对此进行太多的详细描述，但是请记住，在 Typescript 中，您还可以像这样为您的类变量定义访问修饰符:

```
class Point{
   private x: number;
   private y: number; 
    constructor(x: number, y: number) {
        this.x = x; // where 'this' refers to the current object
        this.y = y; 
    }
    getPoints() {
        return "x: " + this.x + " y: " + this.y;
    }
}

```

如同基本上所有面向对象的编程语言一样，我们可以使用访问修饰符来确定谁能够访问我们的类数据。默认情况下，public 被设置为成员默认修饰符，当您希望成员在其类(private)之外不可访问时，以及当您希望成员仅在其类或派生类之内可访问时，分别使用 private 和 protected。

## 遗产

如前所述，Typescript 支持最常用的面向对象模式，包括继承。因此，使用 Typescript，您将能够定义一个类，然后定义它的子类，该子类将继承超类的基本方法和成员:

```
class Animal {
    move(steps: number = 0) {
        console.log(`Animal moved ${steps}m.`);
    }
}

class cat extends Animal {
    meow() {
        console.log('Meow');
    }
}

const cat= new Cat();
cat.meow();
cat.move(1000);
```

## 接口

另一种常见的面向对象技术，你可以用它来创建一个接口。这在 Typescript 中是可能的，它的主要焦点是类型检查，我们可以使用接口给这些类型命名。所以基本上在使用它们的时候，我们会创建一组相关的方法和成员来描述一个特定的对象:

```
interface Box{
   width: number, 
   height: number
}
```

## 结论

这篇文章背后的想法只是为了能够给你一个快速入门，告诉你可以用 Typescript 做什么，它如何帮助我们解决常见的问题，以及它与 Javascript 的不同方法。希望它能让你对一些有用的东西有新的认识！

如果您对使用 TypeScript 了解更多细节感兴趣，您可以查看这些资源:[这里](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)和[这里](https://code.visualstudio.com/docs/typescript/typescript-tutorial)

感谢阅读，

皮耶罗·博雷利。