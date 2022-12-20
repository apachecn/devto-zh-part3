# ...(休息和伸展)尽可能简单地解释

> 原文：<https://dev.to/phocks/-explained-as-simply-as-i-humanly-can-427p>

三个点做两件不同的事情，取决于你如何使用它们。

### 传播:

`...`前面一个数组去掉了外面的`[]`。

```
const x = [1, 2, 3];
const y = [0, ...x, 4, 5, 6];
console.log(y); // [ 0, 1, 2, 3, 4, 5, 6 ] 
```

Enter fullscreen mode Exit fullscreen mode

`...`前面的一个物体去掉了外面的`{}`。

```
const x = { one: 1, two: 2, three: 3 };
const y = { ...x, four: 4, five: 5, six: 6 };
console.log(y); // { one: 1, two: 2, three: 3, four: 4, five: 5, six: 6 } 
```

Enter fullscreen mode Exit fullscreen mode

### 休息:

`...`当[析构](https://hacks.mozilla.org/2015/05/es6-in-depth-destructuring/)一个数组时，分配剩余的元素。

```
const x = [1, 2, 3];
const [one, ...rest] = x
console.log(one); // 1
console.log(rest); // [2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

当析构一个对象时，分配其余的属性。

```
const x = { one: 1, two: 2, three: 3 };
const { one, ...rest } = x;
console.log(one); // 1
console.log(rest); // { two: 2, three: 3 } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！