# 布景，布景无处不在

> 原文：<https://dev.to/stereobooster/sets-sets-are-everywhere-4f3b>

我坚信在旅途中学习是可以的(除非你有一个对生命至关重要的职业，比如医学博士或者你为自动驾驶编写软件)。如果你边做边学，你会获得更多的实用知识。如果这个领域已经广为人知，有时停下来把实际知识总结成理论或研究理论是很有用的。

在数学方面(不总是)，他们倾向于教授理论，而不解释它的实际应用。

在这篇文章中，我想谈谈数学这个主题，乍看之下，它似乎与开发人员无关——集合。我将解释基本思想以及它们在开发中如何出现在许多不同的地方，例如作为数据结构、类型、RDBMS。

我将给出一个简短的理论定义，然后是一个实际的例子。我从利兰·威尔金森的《图形语法》一书中引用了一些定义。

## 一套

一个**集合**是唯一对象的集合。集合中的一个对象被称为集合中的一个元素或**成员。如果我们同意用大写字母(例如 X)来表示集合，用小写字母(例如 X)来表示集合的成员，那么“X 是 X 的元素”的数学符号就是** 

```
x ∈ X, where  x = 1 and X = {1} 
```

## 设定为数据结构

在一些编程语言中 set 是一个内置的数据结构，例如在 JS:

```
const x = 1
const X = new Set([1]);
X.has(x) 
```

或者用 Python

```
x = 1
X = set([1])
print(x in X) 
```

实际上，它可以用来查找一个列表中的所有唯一值，例如在 JS
中

```
const unique = [...new Set(list)]; 
```

## 空集和数字

**空集** ( `∅`或`{}`)是空集或没有元素的集合。在数学上，他们用 **R** ( `ℝ`)表示实数集，用 **Z** ( `ℤ`)表示整数，用 **N** ( `ℕ`)表示自然数

## 类型为集合

类型可以被建模为集合(至少在某种程度上)。我们可以说，类型是这种类型的所有值的集合，例如，当我们说某个变量是整数类型时，我们的意思是它可以取整数集合的所有值 **Z** (显然受计算机内存的限制)。

空集对应于没有值的类型。会是什么呢？它是一种没有返回值的函数(有些人会称之为过程)，例如在 TypeScript:

```
function hello(name: string): void {
  console.log(`Hello, ${name}!`);
} 
```

不要被 JavaScript 中的`undefined`或不同语言中的`null`所迷惑。我们说`void`对应于空集，但是实际上它可能有一个(或者两个，在 JavaScript 中，取决于你如何处理它)值`undefined` / `null`，这样做是为了方便，所以代表值缺失的值可能作为函数或变量的结果传递。有不同的方法来表示值的缺失，例如[可能是单子](https://www.youtube.com/watch?v=YR5WdGrpoug)或[空对象模式](https://www.youtube.com/watch?v=OMPfEXIlTVE)。

附注:同样在 TypeScript 中，他们有一个真正的空集

```
let x: never;
x = undefined; // error 
```

在这一点上变得非常混乱，但是不要这样。在数学中，有一个简单的定义，但是在 JavaScript 中，他们引入了两个空值(undefined 和 null)而犯了一个错误。为了方便起见，我们需要 null，否则我们将被迫使用 monad 或类似的解决方案。TypeScript 是 JavaScript 的超集，因此它继承了它的所有特性，但也因为它是现代类型系统，他们引入了适当的空集，例如，在[穷举检查](https://www.typescriptlang.org/docs/handbook/advanced-types.html#exhaustiveness-checking)中使用。

## 子集，超集

如果集合 A 的每个元素也是集合 B 的元素，那么 A 是 B 的子集，记为`A ⊆ B`。如果 A 是 B 的子集，但 B 至少有一个元素不在 A 中，那么 A 是 B 的**真子集**，记为`A ⊂ B`。同样，我们可以定义子集的反向版本，称为超集，表示为`⊇`，而正确的超集表示为`⊃`。

在 JavaScript 中

```
const Zet = require('zet');
const A = new Zet([1, 2, 3]);
const B = new Zet([3, 4, 5]);
A.subset(B) 
```

在 Python 中

```
A = {1,2,3}
B = {3,4,5}
A <= B 
```

## TypeScript 作为 JavaScript 的超集

他们说 TypeScript 是 JavaScript 的超集是什么意思？这意味着，如果我们将 JavaScript 和 TypeScript 语言建模为一组语法正确的程序(在这些语言中)，其中一组将是另一组的子集:

```
TypeScript ⊃ JavaScript 
```

## 利斯科夫代换原理和子集

在面向对象设计中，他们有固定的原则，首字母缩略词 L 代表 Liskov 替换原则，即派生类必须可以替换它们的基类。

我们可以说 class 是这个类的所有实例的集合，那么如果我们有 class A 和 class B——A 的子类:

```
class A {}
class B extends A {} 
```

并且它们遵循利斯科夫替换原理，我们可以看到集合 B 是 A 的子集，因为 B 类的所有实例也都被认为是 A 类的实例(从行为角度来看，名义上它们是不同的)。

注意:我们只是展示了集合、类型(在编程中)和类之间的对应关系。

## 基数

如果 A 的每个元素可以与 B 的一个元素配对，使得 A 的每个元素恰好出现一次，并且 B 的每个元素恰好出现一次，在配对中我们说 A 和 B 是等价的，记为 A ~ B。如果集合 A 等价于整数集合的子集，我们说集合 A 是可数的。如果 A 为空或等价于集合{1，2，..m }，其中 m 是正整数，我们说 A 是有限集。有限集的基数是 m，它的元素个数。
索引集是形式为{(1，a)，(2，b)的集合，..(m，n)}
`(a, b)`称为**元组**(或**有序对**)。`(a, b,.. z)`称为 n 元组，有时人们也称 n 元组为元组。

## 关系数据库和数据框架

关系数据库中的表意味着元组集(元组集也可以称为**框架**)。有些数据库忽略了这个思路，比如 MySQL。
这解释了为什么有主键很好——很容易保证每个元组的唯一性(不需要检查所有的值，我们可以检查唯一的一个)。
这也解释了为什么 PostgreSQL 可以以不同的顺序返回行，除非为每个查询指定了`ORDER BY`——因为集合不提供顺序。相比之下，MySQL 按照插入的顺序返回行(至少我上次检查时是这样的)。

SQL 中有限集合的基数

```
SELECT COUNT(*) FROM table_name; 
```

在 Python 中，他们有`pandas.DataFrame`。其可以被建模为元组的索引集或索引集的元组。它被实现为一个面向列的存储，对于数学模型来说这无关紧要，它改变了性能，但不是抽象。

## 交集和并集运算

两个集合 A 和 B 的**交**，用`A ∩ B`表示，是元素同时属于 A 和 B 集合的集合:

```
A ∩ B = { x: x ∈ A and x ∈ B } 
```

例如

```
A = {1, 2} and B = {2, 3, 4} then A ∩ B = {2} 
```

由`A ∪ B`表示的两个集合 A 和 B 的**并**是元素属于 A 或 B 集合的集合:

```
A ∪ B = { x: x ∈ A or x ∈ B } 
```

例如

```
A = {1, 2} and B = {2, 3, 4} then A ∪ B = {1, 2, 3, 4} 
```

由`A ⊔ B`表示的两个集合 A 和 B 的**不相交并集**产生其成员是标记元素的集合。标记元素是 x:$形式的元素，其中 x ∈ X 是元素，符号$是标记。有时称为标识符或颜色的标签；它可以是字符串、数值或其他信息。对于不相交的并集，我们用包含它的集合的名称来标记元素。例如

```
A = {1, 2} and B = {2, 3, 4} then A ⊔ B = {1:A, 2:A, 2:B, 3:B, 4:B} 
```

集合 A 和 B 的(笛卡儿)积由`A × B`表示，集合 A 和 B 的元素组合

```
A × B = { (a,b): a ∈ A or b ∈ B } 
```

例如

```
A = {1, 2} and B = {2, 3, 4} then A × B = {(1,2), (1,3), (1,4), (2,2), (2,3), (2,4)} 
```

## 更多关于类型

如果我们说集合和类型之间有对应关系，那么操作也应该有对应关系。

让我们以 TypeScript 为例。工会:

```
type A = 'a' 
type B = 'b'
type C = A | B 
```

在类型系统中，不相交的联合有时被称为区别联合或标记联合。例如

```
interface Rectangle {
    type: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    type: "circle";
    radius: number;
}
type Shape = Rectangle | Circle; 
```

在给定的例子中,`type`字段是一个标签。这种模式经常在事件处理中使用，并且与模式匹配配合得很好。

在我们继续讨论笛卡尔积之前，我们需要讨论如何用集合
来表示记录类型

```
type A = { a: number };
type B = { b: number} 
```

我们可以将其表示为一组元组，其中每个元组中的第一个元素是属性的名称:

```
A = {(a, 1), (a, 2),.. (a,n)}
B = {(b, 1), (b, 2),.. (b,n)} 
```

笛卡尔乘积会是什么样子？

```
C = A × B = {((a,1), (b,1)), ((a,1), (b,2)), ((a,2), (b,1)),.. ((a,n), (b,n)) } 
```

正如您所猜测，元组元组表示具有两个字段的记录:

```
type C = { a: number, b: number } 
```

然后笛卡尔积对应[“交集类型”](https://www.typescriptlang.org/docs/handbook/advanced-types.html#intersection-types)(至少他们在 TypeScript 里是这么叫的)。

```
type C = A & B 
```

## PS

我的想法是介绍该理论的最低限度，但足以看到它在现实生活中是如何使用的。告诉我它是否可掌握，特别是如果你没有 CS 学位。

你认为了解这些学科背后的一点理论有用吗？这些想法中有你喜欢的吗？

> 封面图片:∞焊接离子