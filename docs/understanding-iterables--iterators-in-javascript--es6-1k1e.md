# 理解 javascript -ES6 中的可迭代对象和迭代器

> 原文：<https://dev.to/skptricks/understanding-iterables--iterators-in-javascript--es6-1k1e>

* * *

## 来源:[理解 Iterables&JavaScript-ES6 中的迭代器](https://www.skptricks.com/2019/01/understanding-iterables-iterators-in-javascript.html)

在本教程中，我们将学习如何在 javascript 应用程序中使用**可迭代的&迭代器。这是一个非常简单的话题，在这个例子中，我们将为你提供* *Iterables &迭代器的完整细节。****

**Iterable** —由一种数据结构描述，这种数据结构提供了一种向公众公开其数据的方式。这是通过实现一个键为 **Symbol.iterator** 的方法来实现的。iterator 是迭代器的工厂。

**迭代器** —由包含指向迭代中下一个元素的指针的结构描述。

[![Understanding Iterables & Iterators in javascript -ES6](img/0e16e9c595e78b0bf6d742b03c67c39c.png "Understanding Iterables & Iterators in javascript -ES6")](https://3.bp.blogspot.com/-FX4g7OHwQv0/XDrXLt0HYOI/AAAAAAAACT4/17TqbrC2b8MnZ-H4akGW3DqYouB_e4bLQCLcBGAs/s1600/iterator.png)

让我们看看下面的例子，我们使用迭代器从数组中提取值。在这个例子中，我们使用 **next()函数**从数组中一个接一个地获取值，它将以对象的形式返回结果。

## * *举例**

```
const array = ['one','two','three']  

// Array is a data source because it implements an iterator.  
console.log(typeof array[Symbol.iterator]) // function  

// We first get the iterator which allow us to iterate (i.e. consume) over the array values.  
const iterator = array[Symbol.iterator]()  

// The iterator follows the protocol of being an object with the 'next' function.  
console.log(typeof iterator.next) // function  

// Calling .next() returns the next element in the iteration.  
console.log(iterator.next()) // { value: 'one', done: false }  
console.log(iterator.next()) // { value: 'two', done: false }  
console.log(iterator.next()) // { value: 'three', done: false }  

// Until there's no more elements to iterate, which then returns 'done' as true.  
iterator.next() // { value: undefined, done: true } 
```

## **输出:**

```
> "function"  
> "function"  
> Object { value: "one", done: false }  
> Object { value: "two", done: false }  
> Object { value: "three", done: false } 
```

> [了解 ES6 生成器的基础知识- Javascript](https://www.skptricks.com/2018/10/the-basics-of-es6-generators-javascript.html)

这都是关于 javascript 中的 **Iterables &迭代器。感谢你阅读这篇文章，如果你有任何问题，有关于这篇文章的另一个更好的有用的解决方案，请在评论区写消息。**