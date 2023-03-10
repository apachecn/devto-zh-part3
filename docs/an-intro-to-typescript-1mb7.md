# 打字稿简介

> 原文：<https://dev.to/delph/an-intro-to-typescript-1mb7>

[![alt text](img/c9b3a6307650b3886f815cfb074c826f.png "TypeScript logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--owKJU0EB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/profile_images/743155381661143040/bynNY5dJ_400x400.jpg)

## 什么是打字稿

TypeScript 是编译成普通 JavaScript 的 JavaScript 的超集。TypeScript 是纯面向对象的，有类、接口和模块语句，就像在 Java 或 C#中一样。

与松散类型语言 JavaScript 不同，TypeScript 支持静态类型。

## 有些优点

*   自文档化:不用给代码添加注释，用一个类型系统来注释你的代码，它将总是与实际的代码同步

*   避免常见的陷阱:与解释型语言 JavaScript 不同，TypeScript 会指出任何编译错误。您可以在开发的早期阶段捕捉到错误，更少的错误=成功

### 基本类型

```
const name: string = "Jane";
const age: number = 40;
const isAlive: boolean = true;

isAlive.indexOf("Jane"); // will show an error since indexOf cannot be called on a boolean 
```

或者，这也可以:

```
const name = "Jane";
const age = 40;
const isAlive = true;

isAlive.indexOf("Jane"); // will also throw an error 
```

### 数组

```
let food: string[];
food = ["cookie", "milk", "biscuit"];
food.push("donut");

food = [1, 2, 3]; // will throw an error
food.push(true); // will also throw an error 
```

### 功能

下面的函数接受一个字符串类型的参数(word)并返回一个字符串。

```
function reverse(word: string): string {
  return word
    .split("")
    .reverse()
    .join("");
}

reverse("racecar"); // this is ok
reverse(true); // this will throw an error 
```

我们可以省略返回类型，因为编译器可以推断返回类型。因此这也是可行的:

```
function reverse(word: string) {
  return word
    .split("")
    .reverse()
    .join("");
} 
```

### 界面

接口定义了接口成员必须遵守的语法(如属性、方法)。

接口只包含成员的声明。定义成员是派生类的责任。

```
interface Person {
  name: string;
  age: number;
}

function birthYear(person: Person) {
  return 2018 - person.age;
} 
```

### 别名

类型别名为类型创建新名称。

```
type Vegetable = "broccoli" | "carrot";
// the '|' is a union type, which means that Vegetable can be either a 'broccoli' or 'carrot'

type Fruit = "apple" | "banana" | "kiwi";

type Ingredient = Vegetable | Fruit; // chaining union types

const juice: Ingredient[] = ["broccoli", "kiwi"];
// probably a terrible juice but still valid in TypeScript

const juice2: Ingredient[] = ["broccoli", "bread"];
// will throw an error since bread is not a fruit or vegetable 
```

## 上手

1.  安装-对于 NPM 用户:

```
npm install -g typescript 
```

1.  用'命名您的文件。ts '扩展。例如，“helloworld.ts”

2.  编写您的代码

3.  通过在命令行中键入`tsc helloworld.ts`来编译您的代码。如果一切顺利，就会生成一个 JavaScript 文件 helloworld.js！

## 在您的 React 应用程序中键入脚本

Create-React-App 现在内置了对使用 TypeScript 的支持！

要创建一个新项目，只需输入:

```
npx create-react-app my-typescript-app --typescript 
```

向现有应用程序添加类型脚本:

```
npm install --save typescript @types/node @types/react @types/react-dom @types/jest 
```

接下来，将任何文件重命名为 TypeScript 文件(例如，将 src/index.js 重命名为 src/index.tsx ),并重新启动开发服务器！