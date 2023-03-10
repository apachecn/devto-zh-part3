# 你知道 ES6 - Part 2 吗

> 原文：<https://dev.to/this_mkhy/do-you-know-es6-part-2-596e>

## 在你开始阅读这部分之前，我们已经讨论了 ES6 的一些特性[这里](https://dev.to/mkhy19/do-you-know-es6---part-1-387m)

我们之前讨论过，ES6 是 JS。ES6 是关于下一代 Javascript 的。总的来说，ES6 允许我们编写干净和健壮的 react 应用程序，这有助于我们做更强大的事情。

内容:

*   数组函数
*   模板文字
*   对象文字
*   对于...来说

* * *

## 数组函数

我们可以在数组上执行很多功能，比如 map、concat、join、splice，还有很多这样的方法。这些方法都不是 ES6 而是普通的 js。

**map** 是内置数组方法，返回一个真正的新数组。

```
const numbers = [1,2,3];
const doubleNumbers = numbers.map((num) => {
  return num*2;
});

console.log(numbers);       //[1, 2, 3]
console.log(doubleNumbers); //[2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

**concat** 是一个连接两个或多个数组并返回一个新数组的方法，该数组包含连接后数组的值

```
const myName = ["Mohamed", "Khaled"];
const myInfo1 = ["Computer", "Science", "Student"];
const myInfo2 = ["Front End Developer"];

const concatTwoArray = (arr1,arr2,arr3) => arr1.concat(arr2,arr3);

console.log(concatTwoArray(myName,myInfo1,myInfo2)); 
```

Enter fullscreen mode Exit fullscreen mode

**join** 是将一个数组的元素连接成一个字符串并返回最终字符串的方法。
最后的字符串将由一个指定的分隔符分隔，比如默认的分隔符，即逗号(，)、横杠(|)、破折号(-)、空格或其他分隔符。

```
 let fruits = ["Mohamed", "Khaled", "Yousef", "Mohamed", "Ali"];
let energy1 = fruits.join();
let energy2 = fruits.join("  ");
let energy3 = fruits.join("|");
let energy4 = fruits.join(" and ");
console.log(energy1, energy2, energy3, energy4); 
```

Enter fullscreen mode Exit fullscreen mode

**拼接方法**是在一个数组中添加元素和从一个数组中删除元素，并返回最后一个元素。

在下一个例子中，我们从索引 2 中删除了一个元素(第三个元素)。然后，我们从索引中删除 3 个元素，并添加 2 个元素【计算机和科学】

```
let names = ["Mohamed", "Khaled", "Yousef", "Mohamed", "Ali"];
names.splice(2, 1);
console.log(names);

names.splice(2, 3, "Computer", "Science");
console.log(names); 
```

Enter fullscreen mode Exit fullscreen mode

[阅读更多](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#)

* * *

## 模板文字

在我们定义什么是模板文字之前，让我们记住在普通的 JS 中将字符串连接在一起是通过使用字符串连接操作符(+)。您也可以使用字符串的 concat()方法。

```
const my_name = 'Mohamed';
const message0 = 'My name is ' + my_name;
console.log(message0); 
```

Enter fullscreen mode Exit fullscreen mode

事实上，使用+操作符连接字符串并不是一个好的做法，因为当你需要构建多行字符串时会变得更加复杂。

```
const fstName1 = 'Mohamed', lastName1 = 'Khaled';
const message1 = 'My name is ' + fstName1 + '.\n\n' + 'My last name is ' + lastName1;
console.log(message1); 
```

Enter fullscreen mode Exit fullscreen mode

所以模板文字解决了多行字符串的问题。模板文字是包含嵌入表达式的字符串文字。
用反斜线(`)表示，而不是单引号(’)或双引号(" ")。模板文本可以包含使用${expression}表示的占位符。这使得构建字符串更加容易。`

 `通过使用模板文字，您可以删除引号和字符串连接运算符。通常，模板文字用于更简单的字符串插值。

```
const my_Name = 'Mohamed';
const message1 = `My name is ${my_Name}`;
console.log(message1); 
```

Enter fullscreen mode Exit fullscreen mode

此外，您可以在表达式中引用对象的属性。

```
const myName = {
  name: 'Mohamed',
  age: 23
};

const myFather = {
  name: 'Khaled'
}

let message2 = 'My name is ' + myName.name + '. I am ' + myName.age + ' years old' + ' and my father name is ' + myFather.name;
console.log(message2);

let message3 = `My name is ${myName.name}. I am ${myName.age} years old and my father name is ${myFather.name}`;
console.log(message3); 
```

Enter fullscreen mode Exit fullscreen mode

之前的多行例子呢？

```
const fstName = 'Mohamed', lastName = 'Khaled';
const message0 = `My name is ${fstName}, 

My last name is ${lastName}`;
console.log(message0); 
```

Enter fullscreen mode Exit fullscreen mode

**析构数组和模板文字**
酷，我想你还记得第一部分中的析构数组。
这里我用一行和多行打印我的名字

```
const names = ['Ahmed', 'Mohamed', 'Ali', 'Sarah', 'Khaled', 'John', 'Adel', 'Yousef'];

const [,First, , ,Second, , , Third] = names;

let myName = `My name is ${First}  ${Second}  ${Third}`;
console.log(myName);

myName = `My name is
1\. ${First} 2\. ${Second} 3\. ${Third}`;
console.log(myName); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 对象文字速记

ES6 的特性之一是消除重复，使语法更容易阅读，更简洁。

例如，当我们编写一个对象并将属性名指定为变量名时，比如 first: first，second: second。

```
//Normal JS Object
let first = 'Mohamed';
let second = 'Khaled';
let third = 'Yousef';

const myName = {
  first: first,
  second: second,
  third: third
};

console.log(myName); 
```

Enter fullscreen mode Exit fullscreen mode

**初始化属性的对象文字简写**
事实上我们不需要这种重复，所以我们可以从对象属性中删除重复的变量名

```
let first = 'Mohamed';
let second = 'Khaled';
let third = 'Yousef';

const myName = {
  first,
  second,
  third
};

console.log(myName); 
```

Enter fullscreen mode Exit fullscreen mode

在下一个例子中，当我们在对象
中创建方法时，在普通 JS 中也是如此，我们在我们的对象中创建一个匿名函数，并将其赋给属性 writeMyName，mul function
也是如此

```
let first = 'Mohamed';
let second = 'Khaled';
let third = 'Yousef';

let fs = 5, sec = 7;

const myName = {
  first: first,
  second: second,
  third: third,

  fs: fs,
  sec: sec,

  writeMyName: function(x,y,z) {
      return first.concat(second,third);
  },

  mul: function(a,b) {
      return fs*sec;
  }

};

console.log(myName.writeMyName());
console.log(myName.mul()); 
```

Enter fullscreen mode Exit fullscreen mode

**对象文字速记方法**
在 ES6 中，我们不需要用 function 关键字来定义方法

```
let first = 'Mohamed';
let second = 'Khaled';
let third = 'Yousef';

let fs = 5, sec = 7;
const myName = {
  first: first,
  second: second,
  third: third,

  fs: fs,
  sec: sec,
  writeMyName(x,y,z) {
      return first.concat(second,third);
  },

  mul(a,b) {
      return fs*sec;
  }

};

console.log(myName.writeMyName());
console.log(myName.mul()); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 此为...循环的

森林...of 循环是循环类型之一，用于迭代
类型的 for 循环，如 for 循环、for..in 循环、forEach 循环和 for 循环..循环的。

让我们提醒所有的循环类型:
**【for】循环**
问题是 JS 必须在每次迭代中查找数组的长度。这意味着 Js 必须继续计算数组的长度和退出条件。

在这个例子中，我们使用变量 I 作为计数器来跟踪循环并访问数组中的值。我们还使用 numbers.length 来确定循环的退出条件。

```
const numbers = [1, 2, 3, 4, 5];

for (let i = 0; i < numbers.length; i++) {
  console.log(numbers[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

**该为...在**循环中
为...in 循环通过消除计数逻辑和退出条件，改善了 for 循环的问题。
但是，您仍然需要处理使用索引来访问数组值的问题。

```
const numbers = [1, 2, 3, 4, 5];

for (const index in numbers) {
  console.log(numbers[index]);
} 
```

Enter fullscreen mode Exit fullscreen mode

**forEach**
实际上是一个数组方法，所以只能和数组独占使用。

```
const numbers = ['One', 'Two', 'Three', 'Four'];
numbers.forEach(function(num) {
  console.log(num);
}); 

const doubleNumbers = [1, 2, 3, 4];
doubleNumbers.forEach((num) => { console.log(num*2); }); 
```

Enter fullscreen mode Exit fullscreen mode

**该为...**环路
现在的情况怎么样了...循环的
为...of 循环是所有 for 循环中最简洁的版本，因为它用于循环任何类型的可迭代对象数据。

```
const numbers = [1, 2, 3, 4, 5];

for (const num of numbers) {
  console.log(num);
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以停止或破坏森林..随时循环。
在下一个例子中，我们打印偶数

```
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

for (const num of numbers) {
  if (num % 2 === 1) {
    continue;
  }
  console.log(num);
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们使用...of 循环遍历 days 数组
中的每一天，并大写该天的第一个字母

```
const days = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'];

const capitalize = st => st.charAt(0).toUpperCase().concat(st.slice(1));

for (const day of days) {
  console.log(capitalize(day));
} 
```

Enter fullscreen mode Exit fullscreen mode

### 最后……[这里的](https://github.com/mkhy19/Crash-courses/tree/master/JS/ES6)是回购，可以找到所有源代码。`