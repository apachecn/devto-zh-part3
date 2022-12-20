# 只是路过

> 原文：<https://dev.to/slatham/just-passing-by-2hgf>

这是给自己和其他路人的一个提示。

### 按值

遵循下面的例子...

```
 let a = 42;    // define variable "a" as 42
let b = a;     // define b to be equal to a

// log out the results
console.log(a);    // => 42
console.log(b);    // => 42 

// now if we give b the value of 100
b = 100;

// log out the results again
console.log(a);    // => 42
console.log(b);    // => 100 
```

需要记住的一点是，当我们在第 2 行将“b”赋值为等于“a”时，发生的是存储在“a”中的值(本例中为 42)被**复制**到“b”。据说是传值的。

当我们最终给“b”另一个值(本例中为 100)时，它对变量“a”没有影响。我们可以看到“a”仍然是 42，“b”现在已经变成了 100。

### 被引用

现在考虑这个熟悉的例子...

```
 let a = {value: 42};    // define variable "a" to be an object
let b = a;              // define b to be equal to a

// log out the results
console.log(a);    // => {value: 42}
console.log(b);    // => {value: 42} 

// now if we change a property of b
b.value = 100;

// log out the results again
console.log(a);    // => {value: 100}
console.log(b);    // => {value: 100} 
```

这里要记住的一点是，当我们在第 2 行将“b”赋值为等于“a”时，所发生的是将对“a”的值的引用传递给“b”。然后“a”和“b”**都指向值**(在本例中是对象{value: 42})。它们都只是对存储在内存中的对象{value: 42}的引用。当我们将“b.value”的值更改为 100 时，我们看到“a”也显示了这一变化。

## 那么到底是怎么回事呢？

这取决于变量中存储的值。如果您正在处理一个原始类型——比如第一个例子中的一个数字或一个字符串，那么就使用按值传递。另一方面，对于对象和数组，使用按引用传递。

简而言之:如果你将一个数组或一个对象传递给一个变量或函数，你只是传递了对它的引用。

所以如果我们看下面的例子并通读代码...

```
// define a function that takes two parameters
const myFunction = (x,y) => {

  x[3] = 42;    // change the 4th array element to 42
  y = 100;     // change y to equal 100 

}

let a = [1,2,3,4];    // define a to be an array
let b = 42;           // define b to be the number 42
myFunction(a,b);      // call the function with a and b as the arguments

// log out the result on a and b of calling the function
console.log(a);    // => [1,2,3,42] 
console.log(b);    // => 42 
```

我们可以看到“a”被函数变异了，而“b”没有。原始数字 42 通过值传递给函数。所以“y”是“b”的副本，然后“y”变成 100，而“b”保持不变。

另一方面，如果对数组“a”的引用被传递给函数，那么该数组就会发生变异，因为“x”和“a”只是指向原始数组的指针。

清澈如泥！