# JavaScript:数据结构(第 2 部分-集合)

> 原文：<https://dev.to/banesag/javascript-data-structures-part-2-40cc>

[![alt text](img/b70d2c325acf19c68049218075faed49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCrICA4---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5gwwcx6krdmlzmmpp39r.jpg)

### 数据结构:数组、集合和映射。

数据结构是在 3 篇系列博客的第 1 部分中定义的，其中包括[数组](https://dev.to/banesag/javascript-data-structures-part-1-4eb5)(第 1 部分)[集合](https://dev.to/banesag/javascript-data-structures-part-2-40cc)(第 2 部分)，以及[地图](https://dev.to/banesag/javascript-data-structures-part-3---maps-149i)(第 3 部分)。但是，重新审视这个概念；数据结构指的是如何组织数据，以便有效地使用数据，包括允许控制程序流程的逻辑语句。

### 集合数据结构

集合数据结构由唯一值的集合表示，不需要复制它们。集合提供了一种有用的方法来跟踪数据，方法是使用花括号{}在集合结构中分组和组织数据。

**创建器械包**

使用运算符 ***new*** 和 set()构造函数创建一个空集:

***例如:***

```
const list = new Set ( ); 
```

**添加数值**

使用 add 方法将允许向集合中添加值。

***例如:***

```
list.add ( );
<< Set { 1 } 
```

添加更多值

```
list.add (2 ) .add (3) .add (4) ; // adding multiple values is possible by repeating just the add ( ) method
<< Set { 1 , 2, 3, 4} 
```

***注意:如果您试图将一个现有值添加到集合中，它将会忽略，因为集合数据结构的性质不允许重复值***

**使用数组将多个值相加**

我们还可以将多个值添加到一个集合中，作为数组中的一个参数。

***例如:***

```
const numbers = new Set ([ 1, 2, 3 ]); 
```

***使用字符串作为自变量***

使用字符串与使用数字没有什么不同，如果一个元素是重复的，那么它只会显示一个元素。

```
const letters = new Set (‘hello’ );
letters
<< Set { ‘h’, ‘e’, ‘l’, ‘o’ } // it eliminates one ‘l’ since in sets values cannot been duplicated 
```

```
const letters = new Set ( ) .add ( ‘the’)  .add (‘quick’) .add (‘brown’) .add (‘fox’)
words
<< {‘the’, ‘quick’, ‘brown’, ‘fox’} 
```

非原始值(数组和对象)被认为是唯一的值，即使包含相同的值，也允许重复出现在集合中的值。

```
const arrays = new Set ( ) .add ([1]) .add ([1]);
arrays
<< Set { [ 1 ], [ 1 ] } // these arrays look the same but are different objects 
```

你可以测试它的严格相等性

```
[ 1 ]  === [ 1 ];
<< false 
```

**设定方法**

你可以使用 size()方法找到一个集合中值的个数:

```
const jla = new Set ( ) . add(‘Superman’) .add (‘Batman’) .add (‘Wonder Woman’);
<< Set { ‘Superman’,  ‘Batman’, ‘Wonder Woman’ }

jla.size ( );
<< 3 
```

现在，检查集合中的值

```
jla.has (‘Superman’);
<< true

jla.has (‘Green Lantern’);
<< false 
```

***注意:has()方法比 includes()或 indexOf()方法*** 更高效、更快

**移除数值**

您可以使用 delete()方法删除一个值，如果该值被成功删除，它将返回一个布尔值 true，否则返回 false。

```
jla.delete ( ‘Superman’ );
<< true

jla.delete ( ‘Flash’ );
<< false 
```

clear()方法将 ***清除*** 集合中的所有值，所以使用时要小心。

```
jla.clear ( );
jla
<< Set { }

jla.size 
<< 0 
```

**转换器械包**

通过使用 spread 运算符，可以直接在数组内部将集合转换为数组。

***例如:***

```
const shoppingSet = new Set ( ) .add (‘Apples’) .add (‘Bananas’) .add (‘Beans’);

shoppingSet
<< Set { ‘Apples’, ‘Bananas’, ‘Beans’} 
```

现在把它转换成一个数组:

```
const shoppingArray = […shoppingSet]

shoppingSrray
<< [ ‘Apples’, ‘Bananas’, ‘Beans’ ] 
```

另一种转换方法是 Array.from ( )

```
const shoppingSet = new Set ( ) .add (‘Apples’) .add (‘Bananas’) .add (‘Beans’);
const shoppingArray = Array.from(shoppingSet); 
```

通过结合使用 spread 操作符和将数组传递给新的 Set()构造函数的能力，您创建了一个数组的副本，并删除了所有重复的内容:

```
const duplicate = [ 3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 9];
<< [ 3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 9]

Const nonduplicate = […new Set ( repeatedArray)];
<< [ 3, 1, 4, 5, 9, 2, 6 ] 
```

### 结论

试想，数据结构存储数据，存储和组织得越好，它的预期用途就越有效；就像单词存储在字典中一样，数据存储在数据结构中，同样，如果单词随机存储在没有结构的字典中，我们会很难使用它，数据也会发生同样的情况。