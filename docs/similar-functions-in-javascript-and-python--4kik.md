# JavaScript 和 Python 中的类似函数

> 原文：<https://dev.to/moz5691/similar-functions-in-javascript-and-python--4kik>

计算机语言之间有一些相似之处。如今最流行的两种计算机语言可能是 JavaScript 和 Python。我研究了几种语言之间的一些有用的特性。我在这里比较的前三个函数基本上服务于相同的目的，在 JavaScript 和 Python 之间有非常相似的语法。这个博客的目的是展示两种语言在基本语法之外的相似之处。

第一个是来自 JavaScript 的 Arrow 函数和来自 Python 的 Lambda。

## Python 中的 Lambda，JavaScript 中的 Arrow 函数

首先，在 JavaScript 中，
我们可以把这个简单的函数改成 JavaScript 中的 arrow 函数。我不会在这里提到“this”与 arrow 函数的绑定，而只是谈谈 arrow 函数的简单性。

```
//JavaScript function
 function funcName(param) {
  return param + 10;
}

//JavaScript arrow function
const funcName = (param) => param + 2 
```

JavaScript arrow 函数和 Python lambda 都可以通过分配函数名来作为函数使用，也可以只是作为匿名函数使用。匿名函数意味着函数没有指定名字，这通常是为了在另一个函数调用中实现简单的目的。JavaScript 中的 Arrow 函数不仅仅是简单的，比如词法“this”绑定。

```
#Python function 
 def func_name(param):
      return param + 10

#Python lambda func_name = lambda param: param + 10 
```

## 地图功能

JavaScript 和 Python 之间的映射函数几乎相同。在 JavaScript 中，forEach 函数做类似的工作，但在某些情况下并不使用。当我们需要在 map 函数调用后在不同的内存位置创建对象的新副本时，使用 map 函数。在 Python 中，map 函数的工作方式与不破坏内存中的原始副本相同，而是创建一个新副本来重建新副本。

看下面的例子，“nums”是原始副本，“doubles”是新副本。运行地图功能后，我们不会破坏“nums”。

Python 中的地图与地图
:

```
 nums = [ 1, 2, 3, 4, 5 ] 
 def double(x):
     return x * 2

 doubles = map(double, nums) 
```

这里可以用 Python lambda 来简化。只有一行代码…厉害！

```
 doubles = list(map(lambda x: x * 2, nums))

 #Result:
  [ 2, 4, 6, 8, 10 ] 
```

在 JavaScript:
我们也可以在这里应用箭头函数来简化它。在 JavaScript 中，map 函数在 React.js 中被广泛使用，原因和下面的“nums”在运行 Map 函数后不会被修改一样。

```
 const nums  = [1, 2, 3, 4, 5]
  const doubles = nums.map( x => x * 2);
  console.log(doubles)

  //Result:
  [2, 4, 6, 8, 10] 
```

## 过滤功能

JavaScript 和 Python 之间的 Filer 函数类似于我们从 map 函数中看到的。同样的规则也适用于此，我们不会破坏原始副本，而是在运行过滤功能后重建一个新副本。

在 Python 中:
让我们试着从给定列表中取出偶数。

```
 nums = [1,2,3,4,5,6]
 evens = list(filter(lambda x : x % 2 == 0, nums))

 #Result:
 [2, 4, 6] 
```

在 JavaScript 中:

```
 const nums = [1,2,3,4,5,6]
 const evens = nums.filter( x => x % 2 == 0 )
 console.log(evens)

 //Result: 
 [2,4,6] 
```

## 结论

先学 JavaScript 再学 Python，或者先学 Python 再学 JavaScript 会很有趣。这就像学习另一门外语，但你试图学习的新语言已经和你已经会读会写的语言非常相似了。这两种语言都很棒，它在现代计算机编程语言中的受欢迎程度是不可否认的。