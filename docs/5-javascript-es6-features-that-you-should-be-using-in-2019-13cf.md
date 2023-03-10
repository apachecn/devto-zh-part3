# 2019 年你应该使用的 5 个 Javascript (ES6+)特性

> 原文：<https://dev.to/oliversd/5-javascript-es6-features-that-you-should-be-using-in-2019-13cf>

[![tools](img/e3965ce3a5086076b3ae0ccc0dd1230f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M0_iN6fg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evle69yn9qyu4nx4llqz.jpg)

作为开发人员，我们必须努力使用我们所能使用的最好的工具和特性，使我们的工作变得更加容易和高效。

在这里，我将解释现代 Javascript 的 5 个非常有用的特性，您将一直使用它们:

1.  [Let 和 const 关键字](#letconstkeyword)
2.  [箭头功能](#arrowfunctions)
3.  [解构](#destructuring)
4.  [传播算子](#spread)
5.  [模板文字](#literals)

现在是 2019 年，每个现代浏览器都支持所有这些功能，所以没有借口！。

*您可以在您的浏览器开发工具中运行所有示例代码*

## 1。let 和 const 关键字

在 Javascript 中，var 是声明变量的唯一方式，var 的问题是它没有作用域，你可以多次声明同一个变量，这就是为什么现在我们有了 let 和 const 关键字。

### 让

let 允许你声明变量，这些变量的作用范围仅限于使用它的块、语句或表达式。这与 var 关键字不同，后者全局定义变量，或者局部定义整个函数的变量，而不考虑块范围。【1】

我们来看一个例子:

```
var date = new Date("2015-01-01");
var region = "US";

// Some were further in the code
if (region === "US") {
  var date = new Date();
  console.log("New date", date); // We get the current date as expected
}

console.log(date); //Expecting 2015-01-01T00:00:00.000Z but we get the current date 
```

我们开发人员不擅长命名，如果我们和其他人一起工作，这个问题会放大 10 倍。所以我们用同一个名字来称呼不同的事物并不罕见。

让我们看看这个如何与**让**
一起工作

```
let date = new Date("2015-01-01");
let region = "US";

// Some were further in the code
if (region === "US") {
  let date = new Date();
  console.log("New date", date); // We get the current date as expected
}

console.log(date); //We get 2015-01-01T00:00:00.000Z as expected :) 
```

对我来说，作用域是 let 更重要的特性。其他功能包括:

1.  重新声明:如果你在同一个函数或块范围内声明了一个同名的变量，它会引发一个*语法错误*。有利于停止使用同名变量。
2.  提升:如果你在声明前使用变量，你会得到一个引用错误。

### 常数

const 允许我们声明一个常量变量，一个不应该在代码中改变的值。我们来看一个例子:

```
const speedOfLight=299792458; //m*s-1

try {
  speedOfLight=300;
} catch(error) {
  console.log(error); // TypeError: Assignment to constant variable.
  // Note - error messages will vary depending on browser
}

console.log(speedOfLight); // Expected output 299792458 
```

const 的其他特性:

1.  范围:变量也是块范围的。
2.  不可变:常量变量的值不能改变。
3.  重新声明:与 *let* 相同，*常量*变量不能重新声明，会引发语法错误。

*注意:对于变量的另一个好习惯是总是在你的函数或块作用域的顶部声明变量，这样更容易跟踪。*

**对于这个 2019 请请不要再用** *var* **了。**

## 2。箭头功能

Arrow 函数(也称为 fat arrow for >符号)具有比常规函数更短的语法，并允许我们编写更简洁的代码。

我们来看看旧函数表达式和箭头函数的区别:

```
//ES5
let doubleNumbers = [1,2,3,4,5].map(function(number) { 
  return number*2;
});

//ES6 Arrow function
let doubleNumbers = [1,2,3,4,5].map((number) => { 
  return number*2 
}); 
```

在箭头函数中，当你只有一个参数时，你不需要括号，如果是这样的单行表达式，你可以去掉回车和花括号:

```
//ES6 Arrow function one-liner
let doubleNumbers = [1,2,3,4,5].map(number => number*2);

//ES6 Arrow function multiple arguments
handleClick((event, seconds) => {
  event.preventDefault();
  displayFireworks();
  showAnimation(seconds);
}); 
```

箭头函数节省了我们大量的输入，而且，在我看来，使代码更具可读性。

使用箭头函数我们失去的是，我们没有对*这个*、*自变量*、*超*或*新目标*的引用。这意味着如果你真的需要函数中的这些参数，你应该使用传统的函数。

我的建议是你应该尽可能多地使用箭头函数。在代码中，可读性是关键。

## 3。解构主义

这是我最喜欢的 ES6 的功能之一。

我们先来看一个例子:

```
// Old method
const myArray = ['apple', 'pear', 'orange', 'banana'];
let fruit1 = myArray[0];
let fruit2 = myArray[1];
let fruit3 = myArray[2];
let fruit4 = myArray[3];

//ES6 destructuring
let [fruit1, fruit2, fruit3, fruit4] = myArray; // much better isn't? 
```

我们可以在物体上使用它来:

```
 let dog = {
 name: 'Toby',
 age: 3,
 breed: 'Beagle',
 features: {
   color: 'White and brown',
   favoriteToy: 'Plastic duck'
 }
}

// We can obtain the values like this with destructuring

let {name, age, breed} = dog;

// What if we want only name and age and all the other in another variable

let {name, age, ...info} = dog; 
```

因此，析构赋值允许我们以一种简单有用的方式将数组或对象中的数据提取到不同的变量中。

我一直用它来处理 JSON 对象。

**奖金**

你也可以反过来:

```
let firstName="Albert"
let lastName="Einstein"
let person = {firstName, lastName}

console.log(person.firstName); // "Albert"
console.log(person.lastName); // "Einstein" 
```

## 4。传播符

Spread 运算符允许我们“传播”(咄！)或将数组“分解”成单个项目。

我们来看一个例子:

```
let first = [1,2,3];
let second = [4,5,6];

// If we do this
first.push(second);

// We get
console.log(first); // [1,2,3,[4,5,6]] that is not right

// Using the spread operator

first.push(...second);

console.log(first); // [1,2,3,4,5,6] that's what we wanted! 
```

使用扩展运算符(...)我们设法获得每个单独的元素，而不做迭代，这在许多情况下非常有用。再来看另一个例子:

```
let scores = [23, 45, 56];

function averageThreeScores(a, b, c) {
  let sum = a + b + c;
  return sum/3;
}

console.log(averageThreeScores(...scores)); // Result 41.333333... 
```

这里我们使用 spread 操作符向函数传递参数。

展开操作符也适用于对象。和数组一样，spread 操作符允许我们获得一个对象的每个单独的元素:

```
let name='Toby';
let age=3;
let features = {race: 'Beagle', size: 'small'};

let dog = {name, age, ...features}; // We expand the features object

console.log(dog); // {name: 'Toby', age: 3, race: 'Beagle', size: 'small'} 
```

扩展运算符也允许我们克隆一个对象，而不是使用 Object.assign:

```
let dog = {name: 'Toby', age: 3, race: 'Beagle', size: 'small'}

let puppy = {...dog, name: 'Max', age: 1}; // Clone dog object and modify its properties

console.log(puppy); // {name: 'Max', age: 1, race: 'Beagle', size: 'small'}
console.log(dog); // {name: 'Toby', age: 3, race: 'Beagle', size: 'small'} 
```

正如我们所看到的，我们克隆了狗对象，并在不修改原始对象的情况下更改了年龄和名称的值。

## 5。模板文字

我们在任何地方都使用字符串，我们通常需要传递一些变量给字符串。这就是模板字面量的用处。

模板文字用反勾(`)字符括起来，而不是用双引号或单引号。`

 `模板文字可以包含占位符。这些由美元符号和花括号(${expression}):
表示

```
let a = 5;
let b = 10;
console.log(`The sum of a and b is ${a+b} and the multiplication is ${a*b}`); 
// The sum of a and b is 15 and the multiplication is 50 
```

我们也可以写多行文本，比如:

```
let name='Mike';
let age=30;
let country='Italy';

console.log(`${name} is ${age} years old and
  lives in ${country} `);

// Mike is
//  30 years old and
//  lives in Italy 
```

在这里，Javascript 将显示多行文本，并考虑空格，而不需要特殊字符，如\n。

参考资料:
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/)

封面照片:[花色散在无刷](https://unsplash.com/photos/dQf7RZhMOJU)

* * *

也就是说，就目前而言，我希望这对您有所帮助。如果您有任何问题或任何想补充的内容，请留下您的评论！`