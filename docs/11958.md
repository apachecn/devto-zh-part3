# 你知道 ES6 - Part 1 吗

> 原文：<https://dev.to/this_mkhy/do-you-know-es6---part-1-387m>

* * *

### ES6 其 JS，ES6 是关于下一代 Javascript 的。

ES6 之所以这么有用，是因为所有 ES6 的功能反应，角度和 Vue 应用程序通常使用。总的来说，ES6 允许我们编写干净和健壮的 react 应用程序，这有助于我们做更强大的事情。

内容:

*   让和 const
*   箭头功能
*   模块(出口和进口)
*   班级
*   三个点...
*   解构

* * *

## 设和常数

Let 和 const 是创建变量的不同方式。我们在 js 中用 var 创建了一个变量，但是在 ES6 中，引入了两个不同的关键字，let 和 const。
Var 仍然有效，但是强烈建议你使用 let 和 const
Let 是新的 Var，你用它来创建一个有值的变量。但是这里最重要的一点是，如果你想创建一个真正可变的变量，使用 let。
如果你打算创建一个常量值，那么就使用 const，这样的值你只能赋值一次，永远不会改变。

在普通的 JS 中，我们使用 var 关键字来创建一个变量

```
var myName = 'Mohamed';
console.log(myName);

myName = 'Khaled';
console.log(myName); 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 中，我们可以用 let 关键字代替 var 来创建一个变量

```
let myName = 'Mohamed';
console.log(myName);

myName = 'Khaled';
console.log(myName); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用 const 来创建一个常量变量。这意味着我们不能重新分配这个值
在下一个例子中，我们得到一个错误，因为我们试图重新分配一个常量变量

```
const myName = 'Mohamed';
console.log(myName);

myName = 'Khaled';    //ERROR 
console.log(myName); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 箭头功能。

Arrow functions 是创建 Javascript 函数的不同语法。一个普通的 javascript 函数当然是这样的。

```
function printName(name){
  console.log(name);
}

printName();        //undefined 
printName('Mohamed');   //Mohamed 
```

Enter fullscreen mode Exit fullscreen mode

但是箭头功能:

```
const printName = (name) => {
  console.log(name);
}

printName();    
printName('Mohamed'); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有一个参数
,这个语法
还有其他的选择

```
const printName = name => {
  console.log(name);
}

printName();    
printName('Mohamed'); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有一个不接受参数的函数，我们需要传递一对空括号

```
const printName = () => {
  console.log('Mohamed');
}

printName(); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有一个接收多个参数的函数，我们需要括号

```
const printName = (name1, name2, age) => {
  console.log(name1, name2, age);
}

printName('Mohamed', 'Khaled', 23); 
//Mohamed
//Khaled
//23 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以更新我们的函数体

```
const mul = (number) => {
   return number * 5;
}

console.log (mul(3));       //15 
```

Enter fullscreen mode Exit fullscreen mode

我们可以更新这个函数，去掉大括号并返回关键字

```
const mul = (number) => number * 5;
console.log (mul(3)); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以更新

```
const mul = number => number * 5;
console.log (mul(3));       //15 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 模块(出口&进口)

我们可以把我们的代码分割到多个文件中，怎么做？我们必须以正确的顺序将它们导入到我们的 html 文件中，这样我们才能从另一个文件中导入内容

例如，如果我们有 person.js 文件，它有一个对象

```
//Object
const person = {
    name: 'Mohamed'
}

export default person 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有另一个文件 utility.js，我们可以导出多个东西

```
export const printMohamed = () => {
    console.log('Mohamed');
}

export const mul = number => number * 5;
export const baseData = 10; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在其他地方进口。比如这个文件 app.js

```
//Notice: We can name person whatever we want because it's the default 
import person from './person.js'
import prs from './person.js' 
```

Enter fullscreen mode Exit fullscreen mode

我们应该使用花括号明确地指向文件
中的特定内容

```
import {baseData} from './utility.js'
import {mul} from './utility.js' 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用你选择的任何名字作为关键字
分配一个别名

```
import {mul as multiply} from './utility.js'
import {printMohamed as mkhy} from './utility.js' 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在一个文件中有多个命名的导出，并且我们想要导入它们，我们使用特殊字符*然后分配一个别名

```
import * as bundled from './utility.js' 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有不止一次，我们想进口特殊出口

```
import {baseData},{printMohamed} from './utility.js' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 类

类是对象的蓝图，类可以有属性和方法

这是我们创建的具有 name 属性和 mul 方法的 Person 类。然后我们从这个类中创建了一个对象

```
//Create class
class Person{
    name = 'Mohamed';
        mul = number => number * 5;
}

//Use class, use new keyword
const myPerson = new Person();

console.log(myPerson.name); //"Mohamed"
console.log(myPerson.mul(3));   //15 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子，我们创建了一个具有构造函数和打印方法的类。然后我们从这个类中创建了一个对象

```
//Create class
class Person{
    //Default function method
    constructor(){
        this.name = 'Mohamed';
    }

    printMyName(){
        console.log(this.name);
    }
}

//Create an instance or object
const person = new Person();
person.printMyName();   //"Mohamed" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想继承遗产呢？这里我们使用超级关键字。这是一个关键字，它只是执行父构造函数

```
//Create Human class
class Human{
    constructor(){
        this.gender = 'male';
    }

    printGender(){
        console.log(this.gender);
    }
}

//Create Person class
class Person extends Human{
    constructor(){
            super();    
            this.name = 'Mohamed';
    }

    printMyName(){
            console.log(this.name);
    }
}

//Create an instance or object
const person = new Person();
person.printMyName();   //"Mohamed"
person.printGender();   //"male" 
```

Enter fullscreen mode Exit fullscreen mode

注意下一个重要案例:
这里我们的 person 类是从 Human 类扩展而来的，但是 person 类有自己的属性和方法。

```
class Human{
    //Default function method
    constructor(){
        this.name = 'Mohamed';
        this.gender = 'male';
        this.age = 23;

    }

    printGender(){
        console.log(this.gender);
    }
    printAge(){
        console.log(this.age);
    }  
}

class Person extends Human{
    constructor(){
        super();
        this.name = 'Sarah';
        this.gender = 'Female';
        this.age = 35;
    }

    printMyName(){
        console.log(this.name);
    }
}

const person = new Person();
person.printMyName();   //"Sarah"
person.printGender();   //"Female"
person.printAge();  //35 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 关于类、属性和方法的重要注释

ES7 提供了不同的初始化属性和方法的语法
在 ES6 中，属性就像附加到类或对象上的变量

```
constructor(){
    this.myProperty = 'value';
        this.name = 'Mohamed';
} 
```

Enter fullscreen mode Exit fullscreen mode

在 ES7 中，我们可以直接在类内部分配一个属性，这样我们就可以跳过构造函数调用。
事实上，在幕后，这仍将被转换为使用构造函数

```
myProperty = 'value'
name = 'Mohamed'; 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 中，正如我们之前讨论的，方法就像附加到类或对象上的函数

```
//myMethod () {...}
printMyName(){
    console.log(this.name);
} 
```

Enter fullscreen mode Exit fullscreen mode

在 ES7 中:我们使用一个箭头函数作为属性值，所以你使用这个关键字
没有问题

```
//myMethod = () => {...}
printMyName = () => {console.log('Mohamed');}
printGender = () => {this.gender);}
printMyName = () => {this.name);} 
```

Enter fullscreen mode Exit fullscreen mode

在下一个例子中，我们可以去掉 human 类中的构造函数，去掉 this 关键字。此外，我们将我们的方法转换为箭头函数。最后，我们不再需要调用超级关键字。
**注意**:如果在 JSbin 上运行，会因为不能识别语法而出错。所以你其实需要选择 ES6/巴别

```
class Human{
      gender = 'female';

      printGender = () => {
        console.log(this.gender);
      }
}

class Person extends Human{
      name = 'Mohamed';
      gender = 'male';

      printMyName = () => {
        console.log(this.name);
      }
}

const person = new Person();
person.printMyName();   //"Mohamed"
person.printGender();   //"male" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 三个圆点...

*   spread 和 Rest 运算符称为三点
*   运算符只是三个点...
*   spread 运算符用于拆分数组元素或对象属性。换句话说，在安全复制旧对象的同时复制数组或向对象添加属性。spread 操作符取出所有元素、所有属性，并将它们分布到一个新的数组或对象中，或者分布到您正在使用它的任何地方

EX1...用数组

```
const numbers = [1,2,3];
const newNumbers = [numbers,4,5];
console.log(newNumbers);    //[[1, 2, 3], 4, 5]

const spreadNumbers =[...numbers,4,5];
console.log(spreadNumbers); //[1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

EX2..带对象

```
const oldPerson = {
  name : 'Mohamed'

};

const newPerson = {
  ...oldPerson,
  age: 23
}

console.log(newPerson); 
```

Enter fullscreen mode Exit fullscreen mode

输出
【对象对象】{
年龄:23，
姓名:【穆罕默德】

*   rest 操作符用于将函数参数列表合并到一个数组中，我们在函数参数列表中使用它

```
const filterFunc1 = (...args) => {
  return args.filter(el => el === 1);
}

console.log(filterFunc1(1,2,7,1,3,8,9,1,2));    //[1, 1, 1] 
```

Enter fullscreen mode Exit fullscreen mode

例 3

```
const filterFunc2 = (...args) => {
  return args.filter(el => el === 1 || el ===2);
}

console.log(filterFunc2(1,2,7,1,3,8,9,1,2));    //[1, 2, 1, 1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 解构

*   析构允许你轻松地提取数组元素或对象属性，并将它们存储在变量中
*   析构不同于扩展运算符所做的
*   析构允许你提取单个元素或属性，并把它们存储在数组和对象的变量中

数组示例:

```
[a,b] = ['Mohamed','Khaled']
console.log(a); //Mohamed
console.log(b); //Khaled 
```

Enter fullscreen mode Exit fullscreen mode

对象示例:

```
myInfo1 = {name:'Mohamed'};
console.log(myInfo1.name);  //Mohamed
console.log(myInfo1.age);   //undefined

myInfo2 = {name:'Mohamed', age:23};
console.log(myInfo2.name);  //Mohamed
console.log(myInfo2.age);   //23 
```

Enter fullscreen mode Exit fullscreen mode

例 1

```
const numbers = [1,2,3];
[num1,num2] = numbers;

console.log(num1,num2);
//1
//2 
```

Enter fullscreen mode Exit fullscreen mode

例 2

```
const numbers = [1,2,3];
[num1, ,num3] = numbers;

console.log(num1,num3);
//1
//3 
```

Enter fullscreen mode Exit fullscreen mode

例 3

```
const {name} = {name:'Mohamed', age:23}
console.log(name);  //Mohamed
console.log(age);   //undefined 
```

Enter fullscreen mode Exit fullscreen mode

例 4

```
const {name,age} = {name:'Mohamed', age:23}
console.log(name);  //Mohamed
console.log(age);   //23 
```

Enter fullscreen mode Exit fullscreen mode

**一些参考:**
了解更多[让](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
了解更多 [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
了解更多 [ES6 箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### 最后……[这里的](https://github.com/mkhy19/Crash-courses/tree/master/JS/ES6)是回购，可以找到所有源代码。