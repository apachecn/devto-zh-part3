# 20 个棘手的 ES6 面试问题来改进你的 JavaScript 技能

> 原文：<https://dev.to/fullstackcafe/20-tricky-es6-interview-questions-to-revamp-your-javascript-skills-5a4o>

[![20 Tricky ES6/ES2015 Interview Questions to Revamp Your Javascript Skills](img/473362f0a502ea92620f7e3cd416e1ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FIkPqMgJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/611328/pexels-photo-611328.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26w%3D500) 
ES6 是指版本 6 的 ECMA 脚本编程语言。ECMA 脚本是 JavaScript 的标准化名称，版本 6 是继 2011 年发布的版本 5 之后的下一个版本。它是对 JavaScript 语言的一个重大改进，增加了许多旨在使大规模软件开发更容易的特性。ES6 和 ES2015 名称都用于 JavaScript 版本，该版本引入了箭头函数、类、模板字符串、承诺等。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:你能解释一下 ES5 和 ES6 的区别吗

> 题目: **JavaScript**
> 难度:⭐⭐⭐

*   ECMAScript 5(ES5):ECMAScript 第五版，于 2009 年标准化。这个标准已经在所有现代浏览器中相当完整地实现了

*   **ECMAScript 6(ES6)/ECMAScript 2015(es 2015)**:ECMAScript 第 6 版，2015 年标准化。这个标准已经在大多数现代浏览器中部分实现。

以下是 ES5 和 ES6 的一些主要区别:

*   **箭头功能** & **字符串插值**:考虑:

```
const greetings = (name) => {
      return `hello ${name}`;
} 
```

甚至:

```
const greetings = name => `hello ${name}`; 
```

*   **常数**。Const 在许多方面都像其他语言中的常量一样工作，但是有一些注意事项。Const 代表对一个值的“常量引用”。所以用 const，你实际上可以改变被变量引用的对象的属性。你不能改变引用本身。

```
const NAMES = [];
NAMES.push("Jim");
console.log(NAMES.length === 1); // true
NAMES = ["Steve", "John"]; // error 
```

*   **块范围的变量**。新的 ES6 关键字`let`允许开发人员在块级别确定变量的范围。`Let`的提升方式与`var`不同。
*   **默认参数值**默认参数允许我们用默认值初始化函数。当参数被省略或未定义时，将使用默认值，这意味着 null 是有效值。

```
// Basic syntax
function multiply (a, b = 2) {
     return a * b;
}
multiply(5); // 10 
```

*   **类定义和继承**

    ES6 引入了对类(`class`关键字)、构造函数(`constructor`关键字)、继承的`extend`关键字的语言支持。

*   **for-of 运算符**

    The for...of 语句创建一个遍历可迭代对象的循环。

*   **扩散算子**
    用于对象合并

```
const obj1 = { a: 1, b: 2 }
const obj2 = { a: 2, c: 3, d: 4}
const obj3 = {...obj1, ...obj2} 
```

*   **承诺**承诺提供了一种机制来处理异步操作的结果和错误。您可以用回调完成同样的事情，但是 promises 通过方法链接和简洁的错误处理提供了更好的可读性。

```
const isGreater = (a, b) => {
  return new Promise ((resolve, reject) => {
    if(a > b) {
      resolve(true)
    } else {
      reject(false)
    }
    })
}
isGreater(1, 2)
  .then(result => {
    console.log('greater')
  })
 .catch(result => {
    console.log('smaller')
 }) 
```

*   **模块导出&导入**考虑模块导出:

```
const myModule = { x: 1, y: () => { console.log('This is ES5') }}
export default myModule; 
```

和导入:

```
import myModule from './myModule'; 
```

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q2:什么是 IIFEs(立即调用函数表达式)？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

这是一个立即调用的函数表达式，简称 IIFE。它在创建后立即执行:

```
(function IIFE(){
    console.log( "Hello!" );
})();
// "Hello!" 
```

当试图避免污染全局名称空间时，经常使用这种模式，因为在 life 中使用的所有变量(像在任何其他普通函数中一样)在其作用域之外是不可见的。

🔗**来源:**【stackoverflow.com】T2

### Q3:ES6 中什么时候应该使用箭头函数？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

我现在对 ES6 及更高版本中的函数使用以下经验法则:

*   在全局范围和 Object.prototype 属性中使用`function`。
*   将`class`用于对象构造器。
*   在其他地方使用`=>`。

为什么几乎处处使用箭头函数？

*   **作用域安全**:当箭头函数被一致使用时，所有东西都保证使用相同的 thisObject 作为根。如果一个标准函数回调和一堆箭头函数混合在一起，作用域就有可能变得混乱。
*   **紧凑性**:箭头函数更容易读写。(这似乎有些固执己见，所以我将进一步给出几个例子)。
*   **清晰**:当几乎所有东西都是箭头函数时，任何常规函数都会立即突出来定义范围。开发人员总是可以查找下一个更高的函数语句来了解 thisObject 是什么。

🔗**来源:**【stackoverflow.com】T2

### Q4:把符号带到 ES6 的动机是什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

`Symbols`是一种新的、特殊的对象，可以用作对象中唯一的属性名。使用`Symbol`而不是`string`允许不同的模块创建彼此不冲突的属性。`Symbols`也可以被设为私有，这样那些没有直接访问`Symbol`权限的人就不能访问它们的属性。

`Symbols`是新的**原语**。就像`number`、`string`和`boolean`原语一样，`Symbol`有一个函数可以用来创建它们。与其他原语不同，`Symbols`没有文字语法(例如`string`如何拥有`''`)——创建它们的唯一方法是使用`Symbol`构造函数，如下所示:

```
let symbol = Symbol(); 
```

实际上，`Symbol`只是给对象附加属性的一种稍微不同的方式——你可以很容易地提供众所周知的`Symbols`作为标准方法，就像`Object.prototype.hasOwnProperty`出现在从`Object`继承的所有东西中一样。

🔗**来源:**【stackoverflow.com】T2

### Q5:在 ES6 中使用 spread 语法有什么好处，与 rest 语法有什么不同？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

ES6 的 spread 语法在用函数范式编码时非常有用，因为我们可以轻松地创建数组或对象的副本，而无需求助于`Object.create`、`slice`或库函数。这个语言特性经常在 Redux 和 rx.js 项目中使用。

```
function putDookieInAnyArray(arr) {
  return [...arr, 'dookie'];
}

const result = putDookieInAnyArray(['I', 'really', "don't", 'like']); // ["I", "really", "don't", "like", "dookie"]

const person = {
  name: 'Todd',
  age: 29,
};

const copyOfTodd = { ...person }; 
```

ES6 的 rest 语法为包含传递给函数的任意数量的参数提供了一种简化方式。它类似于 spread 语法的逆语法，将数据填充到一个数组中，而不是对一个数据数组进行解包，它适用于函数参数，也适用于数组和对象析构赋值。

```
function addFiveToABunchOfNumbers(...numbers) {
  return numbers.map(x => x + 5);
}

const result = addFiveToABunchOfNumbers(4, 5, 6, 7, 8, 9, 10); // [9, 10, 11, 12, 13, 14, 15]

const [a, b, ...rest] = [1, 2, 3, 4]; // a: 1, b: 2, rest: [3, 4]

const { e, f, ...others } = {
  e: 1,
  f: 2,
  g: 3,
  h: 4,
}; // e: 1, f: 2, others: { g: 3, h: 4 } 
```

🔗**来源:**【github.com/yangshun】T2

### Q6:ES6 类和 ES5 函数构造器有什么区别？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

让我们先来看看每个的例子:

```
// ES5 Function Constructor
function Person(name) {
  this.name = name;
}

// ES6 Class
class Person {
  constructor(name) {
    this.name = name;
  }
} 
```

对于简单的构造函数，它们看起来非常相似。

构造函数的主要区别在于使用继承。如果我们想创建一个继承了`Person`的`Student`类，并添加一个`studentId`字段，这是我们除了上述之外要做的事情。

```
// ES5 Function Constructor
function Student(name, studentId) {
  // Call constructor of superclass to initialize superclass-derived members.
  Person.call(this, name);

  // Initialize subclass's own members.
  this.studentId = studentId;
}

Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;

// ES6 Class
class Student extends Person {
  constructor(name, studentId) {
    super(name);
    this.studentId = studentId;
  }
} 
```

在 ES5 中使用继承要冗长得多，而 ES6 版本更容易理解和记忆。

🔗**来源:**【github.com/yangshun】T2

### Q7:`.call`和`.apply`有什么区别？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

`.call`和`.apply`都用于调用函数，第一个参数将作为函数中`this`的值。然而，`.call`接受逗号分隔的参数作为下一个参数，而`.apply`接受一个参数数组作为下一个参数。记住这一点的一个简单方法是 C 代表`call`，用逗号分隔，A 代表`apply`和一个参数数组。

```
function add(a, b) {
  return a + b;
}

console.log(add.call(null, 1, 2)); // 3
console.log(add.apply(null, [1, 2])); // 3 
```

🔗**来源:**【github.com/yangshun】T2

### Q8:为什么要用 ES6 类？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

您可能选择使用**类**的一些原因:

*   语法更简单，不容易出错。
*   使用新语法建立继承层次比使用旧语法要容易得多(也更不容易出错)。
*   `class`保护你免于在构造函数中使用`new`的常见错误(如果`this`不是构造函数的有效对象，让构造函数抛出一个异常)。
*   使用新语法调用方法的父原型版本比旧语法要简单得多(`super.method()`而不是`ParentConstructor.prototype.method.call(this)`或`Object.getPrototypeOf(Object.getPrototypeOf(this)).method.call(this)`)。

考虑:

```
// **ES5**
var Person = function(first, last) {
    if (!(this instanceof Person)) {
        throw new Error("Person is a constructor function, use new with it");
    }
    this.first = first;
    this.last = last;
};

Person.prototype.personMethod = function() {
    return "Result from personMethod: this.first = " + this.first + ", this.last = " + this.last;
};

var Employee = function(first, last, position) {
    if (!(this instanceof Employee)) {
        throw new Error("Employee is a constructor function, use new with it");
    }
    Person.call(this, first, last);
    this.position = position;
};
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;
Employee.prototype.personMethod = function() {
    var result = Person.prototype.personMethod.call(this);
    return result + ", this.position = " + this.position;
};
Employee.prototype.employeeMethod = function() {
    // ...
}; 
```

ES6 类也是一样:

```
// ***ES2015+**
class Person {
    constructor(first, last) {
        this.first = first;
        this.last = last;
    }

    personMethod() {
        // ...
    }
}

class Employee extends Person {
    constructor(first, last, position) {
        super(first, last);
        this.position = position;
    }

    employeeMethod() {
        // ...
    }
} 
```

🔗**来源:**【stackoverflow.com】T2

### Q9:JavaScript 中定义枚举的首选语法是什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

由于 1.8.5 可以密封和冻结对象，因此将上述定义为:

```
var DaysEnum = Object.freeze({
    "monday": 1,
    "tuesday": 2,
    "wednesday": 3,
    ...
}) 
```

或者

```
var DaysEnum = {
    "monday": 1,
    "tuesday": 2,
    "wednesday": 3,
    ...
}
Object.freeze(DaysEnum) 
```

瞧啊。JS 枚举。

然而，这并不妨碍你给一个变量赋一个不需要的值，这通常是枚举的主要目的:

```
let day = DaysEnum.tuesday
day = 298832342 // goes through without any errors 
```

🔗**来源:**【stackoverflow.com】T2

### Q10:解释 Object.freeze()与 const 的区别

> 题目: **JavaScript**
> 难度:⭐⭐⭐

`const`和`Object.freeze`是完全不同的两回事。

*   `const`适用于**绑定**(“变量”)。它创建了一个不可变的绑定，也就是说，你不能给绑定赋值。

```
const person = {
    name: "Leonardo"
};
let animal = {
    species: "snake"
};
person = animal; // ERROR "person" is read-only 
```

*   `Object.freeze`作用于**值**，更具体地说，*对象值*。它使一个对象不可变，也就是说，你不能改变它的属性。

```
let person = {
    name: "Leonardo"
};
let animal = {
    species: "snake"
};
Object.freeze(person);
person.name = "Lima"; //TypeError: Cannot assign to read only property 'name' of object
console.log(person); 
```

🔗**来源:**【stackoverflow.com】T2

### Q11:JavaScript 中的提升是什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

*提升*是 JavaScript 解释器将所有变量和函数声明移动到当前作用域顶部的动作。*吊装*有两种类型:

*   可变提升-罕见
*   功能提升-更常见

一个`var`(或函数声明)出现在一个作用域中的任何地方，该声明被认为属于整个作用域，并且可以在任何地方访问。

```
var a = 2;
foo();                 // works because `foo()`
                         // declaration is "hoisted"

function foo() {
    a = 3;
    console.log( a );   // 3
    var a;             // declaration is "hoisted"
                         // to the top of `foo()`
}

console.log( a );   // 2 
```

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q12:解释原型设计模式

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

原型模式创建新的对象，但是它不是创建未初始化的对象，而是返回用它*从原型对象或样本对象复制*的值初始化的对象。原型模式也称为属性模式。

原型模式有用的一个例子是用与数据库中的默认值相匹配的值来初始化业务对象。prototype 对象保存着复制到新创建的业务对象中的默认值。

经典语言很少使用原型模式，但是作为原型语言的 JavaScript 在构造新对象及其原型时使用这种模式。

🔗**来源:**【dofactory.com】T2

### Q13:ES6 中的时间死区是什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

在 ES6 中`let`和`const`被挂起(如`var`、`class`和`function`)，但是从进入范围到被声明不能进入有一段时间。**这个时期是颞死区(TDZ)** 。

考虑:

```
//console.log(aLet)  // would throw ReferenceError

let aLet;
console.log(aLet); // undefined
aLet = 10;
console.log(aLet); // 10 
```

在这个例子中， **TDZ** 在`aLet`被声明时结束，而不是被赋值。

🔗**资料来源:** [github.com/ajzawawi](https://github.com/ajzawawi/js-interview-prep/blob/master/answers/es6/temporal-dead-zone.md)

### Q14:ES6 中什么时候不应该使用箭头功能？举出三个或更多案例。

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

不应使用箭头功能:

*   当我们需要函数提升时——因为箭头函数是匿名的。
*   当我们想在函数中使用这个/参数时——因为箭头函数本身没有这个/参数，它们依赖于它们的外部上下文。
*   当我们想使用命名函数时——因为箭头函数是匿名的。
*   当我们想用函数作为构造函数时——因为 arrow 函数没有自己的这个。
*   当我们想在 object literal 中添加函数作为属性并在其中使用 object 时——因为我们不能访问它(它应该是 object 本身)。

🔗**来源:**【stackoverflow.com】T2

### Q15:ES6 weak map 有哪些实际用途？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

WeakMaps 提供了一种从外部扩展对象而不干扰垃圾收集的方法。每当你想扩展一个对象，但是因为它是密封的而不能扩展的时候——或者从外部源——就可以使用 WeakMap。

**WeakMap** 仅适用于 ES6 及以上车型。WeakMap 是键和值对的集合，其中键必须是对象。

```
var map = new WeakMap();
var pavloHero = {
    first: "Pavlo",
    last: "Hero"
};
var gabrielFranco = {
    first: "Gabriel",
    last: "Franco"
};
map.set(pavloHero, "This is Hero");
map.set(gabrielFranco, "This is Franco");
console.log(map.get(pavloHero)); //This is Hero 
```

WeakMaps 有趣的一面是，它对地图中的键有一个弱引用。弱引用意味着如果对象被销毁，垃圾收集器将从 WeakMap 中移除整个条目，从而释放内存。

🔗**来源:**【stackoverflow.com】T2

### Q16:解释为什么下面的不像生活。需要改变什么才能让它成为真正的生活？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

```
function foo(){ }(); 
```

* * *

IIFE 代表立即调用的函数表达式。JavaScript 解析器将`function foo(){ }();`读作`function foo(){ }`和`();`，其中前者是一个函数声明，后者(一对括号)是试图调用一个函数，但没有指定名称，因此它抛出`Uncaught SyntaxError: Unexpected token )`。

这里有两种方法可以解决这个问题，包括添加更多的括号:`(function foo(){ })()`和`(function foo(){ }())`。这些函数不在全局作用域中公开，如果不需要在主体中引用它自己，甚至可以省略它的名称。

你也可以使用`void`操作符:`void function foo(){ }();`。不幸的是，这种方法有一个问题。给定表达式的求值永远是`undefined`，所以如果你的 IIFE 函数返回任何东西，你就不能使用它。一个例子:

```
// Don't add JS syntax to this code block to prevent Prettier from formatting it.
const foo = void
function bar() {
    return 'foo';
}();

console.log(foo); // undefined 
```

🔗**来源:**【github.com/yangshun】T2

### Q17:你能比较一下模块模式和构造器/原型模式的用法吗？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

module 模式通常用于命名空间，在这种情况下，您将有一个单独的实例作为存储来对相关的函数和对象进行分组。这是一个不同于原型的用例。他们并没有真正地相互竞争。你可以很愉快地一起使用这两者(比如把一个构造函数放在一个模块中，然后说`new MyNamespace.MyModule.MyClass(arguments)`)。

构造函数和原型是实现类和实例的合理方法之一。它们并不完全对应于那个模型，所以你通常需要选择一个特定的模式或者辅助方法来根据原型实现类。

🔗**来源:**【stackoverflow.com】T2

### Q18:ES6 地图和 WeakMap 有什么区别？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐⭐

当它们的键/值引用的对象被删除时，它们的行为会有所不同。让我们以下面的代码为例:

var Map = new Map()；
var weak map = new weak map()；

```
(function() {
    var a = {
        x: 12
    };
    var b = {
        y: 12
    };

    map.set(a, 1);
    weakmap.set(b, 2);
})() 
```

上面的生命被执行，我们无法再引用`{x: 12}`和`{y: 12}`。垃圾收集器继续从“WeakMap”中删除键 b 指针，并从内存中删除`{y: 12}`。但是在“Map”的情况下，垃圾收集器不会从“Map”中移除指针，也不会从内存中移除`{x: 12}`。

WeakMap 允许垃圾收集器执行其任务，但不允许映射。对于手动编写的映射，键数组将保持对键对象的引用，防止它们被垃圾收集。在本机 WeakMaps 中，对键对象的引用被“*弱地*保存”，这意味着在没有对该对象的其他引用的情况下，它们不会阻止垃圾收集。

🔗**来源:**【stackoverflow.com】T2

### Q19:你能给出一个 curry 函数的例子吗？为什么这个语法有优势？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐⭐

**Currying** 是一种模式，其中一个具有多个参数的函数被分解成多个函数，当这些函数被串行调用时，将一次累加所有需要的参数。这种技术有助于使以函数式风格编写的代码更易于阅读和编写。需要注意的是，对于一个要定制的函数，它需要从一个函数开始，然后分解成一系列函数，每个函数接受一个参数。

```
function curry(fn) {
  if (fn.length === 0) {
    return fn;
  }

  function _curried(depth, args) {
    return function(newArgument) {
      if (depth - 1 === 0) {
        return fn(...args, newArgument);
      }
      return _curried(depth - 1, [...args, newArgument]);
    };
  }

  return _curried(fn.length, []);
}

function add(a, b) {
  return a + b;
}

var curriedAdd = curry(add);
var addFive = curriedAdd(5);

var result = [0, 1, 2, 3, 4, 5].map(addFive); // [5, 6, 7, 8, 9, 10] 
```

🔗**来源:**【github.com/yangshun】T2

### Q20:如何在 JavaScript 中“深度冻结”对象？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐⭐

如果你想确保对象被深度冻结，你必须创建一个递归函数来冻结对象类型的每个属性:

无深度冷冻:

```
let person = {
    name: "Leonardo",
    profession: {
        name: "developer"
    }
};
Object.freeze(person); // make object immutable
person.profession.name = "doctor";
console.log(person); //output { name: 'Leonardo', profession: { name: 'doctor' } } 
```

有深度冻结:

```
function deepFreeze(object) {
    let propNames = Object.getOwnPropertyNames(object);
    for (let name of propNames) {
        let value = object[name];
        object[name] = value && typeof value === "object" ?
            deepFreeze(value) : value;
    }
    return Object.freeze(object);
}
let person = {
    name: "Leonardo",
    profession: {
        name: "developer"
    }
};
deepFreeze(person);
person.profession.name = "doctor"; // TypeError: Cannot assign to read only property 'name' of object 
```

🔗**来源:**【medium.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *如果你喜欢这篇文章，请分享给你的开发者伙伴！*
> *查看更多全栈面试问题&答案上👉[www . full stack . cafe](https://www.fullstack.cafe)T9】*