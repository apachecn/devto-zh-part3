# Javascript 中类似 Python 的范围迭代器

> 原文：<https://dev.to/eatsjobs/python-like-range-iterator-in-javascript-2b35>

为了模拟一些测试数据或者用假数据填充数组等等，有时我会想念 Javascript 中的 python 范围迭代器。

```
myList = [0,1,2,3]
for i in range(0, len(myList))
    print i 
```

Enter fullscreen mode Exit fullscreen mode

有了这个并不新的 Javascript，我们可以“生成”自己的迭代器。

range 的实现可以是这样的:

```
function range(start = 0, end, step = 1) {
  return {
    *[Symbol.iterator]() {
      let newStart = start,
        newEnd = end;
      if (end < start) {
        newStart = end;
        newEnd = start;
      }
      for (let i = newStart; i < newEnd; i += step) {
        yield i;
      }
    }
  };
}

// forward
for (let element of range(0, 10)) {
    console.log({ element });
}

// and backwards
for (let element of range(0, -10)) {
    console.log({ element });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以编写一个更简单的实现，只需编写生成器函数。

```
function* range(start = 0, end, step = 1) {
  let newStart = start,
    newEnd = end;
  if (end < start) {
    newStart = end;
    newEnd = start;
  }
  for (let i = newStart; i < newEnd; i += step) {
    yield i;
  }
}

// forward
for (let element of range(0, 10)) {
    console.log({ element });
}

// and backwards
for (let element of range(0, -10)) {
    console.log({ element });
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，你**必须**使用新的 for-of 来迭代它。

只要记住函数总是返回一个迭代器。因此，如果需要的话，我们可以很容易地用这种方法得到迭代器。

```
const it = range(0, 5)[Symbol.iterator]();
console.log(it.next()) // { value: 0, done: false }
console.log(it.next()) // { value: 1, done: false } 
```

Enter fullscreen mode Exit fullscreen mode