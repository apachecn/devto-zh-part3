# JavaScript:数据结构(第 1 部分——数组)

> 原文：<https://dev.to/banesag/javascript-data-structures-part-1-4eb5>

[![alt text](img/b70d2c325acf19c68049218075faed49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCrICA4---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5gwwcx6krdmlzmmpp39r.jpg)

### 数据结构:数组、集合和映射。

数据结构用于存储值列表，这些数据结构称为数组、集合和映射。但是什么是数据结构呢？根据维基百科，“在计算机科学中，数据结构是在计算机中组织数据的一种特殊方式，因此它可以被有效地使用”，对此我要补充的是，使用逻辑语句可以让你控制程序的流程，以及允许我们在代码中根据需要反复重复代码块的循环。

### 数组数据结构

数组数据结构或数组是有序的值列表，或者是由索引或键标识的元素(值或变量)的集合。最简单的数组数据结构是线性数组。

创建一个数组文字需要你写一对方括号。

***例如:***

```
const myArray = [ ];
<< [ ] 
```

我们可以添加一个构造函数

***例如:***

```
const myArray = new Array( );
<< [ ] 
```

数组不是原始值，而是一个特殊的内置对象，就像使用“typeof”操作符时一样:

```
typeof [ ];
<< ‘object’ 
```

### 初始化数组

但是，我们如何初始化一个数组呢？我们可以创建一个空数组。

***例如:***

```
const heroes = [ ]; 
```

现在，我们可以在英雄数组
中找到元素 0(零)的值

```
heroes [0];  // to access a specific value in an array, we write its index inside the square brackets
<< undefined 
```

### 向数组中添加值

通过将元素 0(零)赋值来放置一个新字符串。

***例如:***

```
heroes[0] = ‘Superman’; 
```

我们可以将数组中的项目视为一个变量，并使用赋值运算符=

***例如:***

```
heroes[0] = ‘Batman’; 
```

我们可以使用不同的指数添加更多的值:

```
heroes[1] = ‘Wonder Woman’;
heroes[2] = ‘Flash’;
heroes[5] = ‘Aquaman’; 
```

您可以重复该操作，直到您在数组中添加了所有想要或需要的元素。

然后，您可以通过在控制台上键入您的阵列名称(英雄)来查看您的阵列。

```
heroes;
<< [‘Batman’, ‘Wonder Woman’, ‘Flash’, undefined, undefined, ‘Aquaman’] 
```

您可以看到，那些分配了索引的元素按其名称显示在列表中，但是任何其他未使用的槽显示为未定义。

### 创建数组文本

我们可以创建数组文字来避免逐个添加值。

***例如:***

```
const avengers = [‘Captain America’, ‘Iron Man’, ‘Thor’, ‘Hulk’];
<< [‘Captain America’, ‘Iron Man’, ‘Thor’, ‘Hulk’] 
```

### 从数组中删除值

使用' delete '操作符允许我们从数组中删除一个项目。

***例如:***

```
delete avengers[3];
<< true 
```

该操作将删除存储在索引 3(“Hulk”)中的值，但它也将索引保留为未定义，因为即使删除值，空间仍然存在，这意味着数组仍然具有相同数量的元素，但我们删除的元素将是未定义的。

```
avengers;
<< [‘Captain America’, ‘Iron Man’, ‘Thor’,  ‘undefined’] 
```

### 析构数组

析构是指从数组中取出值并以单个值的形式呈现，这允许我们同时给多个值赋值。

```
const [x, y] = [1, 2]; 
```

这意味着每个变量都存在于数组之外，您可以单独检查它的值。

***例如:***

```
x
<< 1

y
<< 2 
```

或者，

```
[x, y] = [y, x];
x
<< 2

y
<< 1 
```

这是关于数据结构的一系列 3 个帖子的[数组](https://dev.to/banesag/javascript-data-structures-part-1-4eb5)(第一部分)[集](https://dev.to/banesag/javascript-data-structures-part-2-40cc)(第二部分)[图](https://dev.to/banesag/javascript-data-structures-part-3---maps-149i)(第三部分)将完成该系列。