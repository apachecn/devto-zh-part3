# 在 javascript 中保持对象和数组不变的最佳实践。

> 原文：<https://dev.to/antonio_pangall/best-practices-to-keep-objects-and-arrays-immutable-in-javascript-3nmm>

# 永恒性

不变性是函数式编程的核心原则。在这篇文章中，我将列出一些在不使用任何第三方库的情况下保持 JavaScript 中数据结构不变的最佳实践。

## 那么 Javascript 中哪些数据类型是不可变的呢？

像字符串、数字和布尔这样的基本数据类型在默认情况下是不可变的，它们不能被改变，所以你不需要使用任何“魔法”来保持它们的不可变。我们不能对默认可变的对象和数组说同样的话，因为它们只是引用。接下来，我们将经历一些操作，这些操作将帮助我们保持对象和数组不变。

### 对数组的操作。

#### 克隆原始数据类型数组。

```
const sourceArray = [1,2,3];
const clonedArray = [...sourceArray];
// or you can do
const clonedArray = sourceArray.slice(0); 
```

Enter fullscreen mode Exit fullscreen mode

### 克隆一个对象数组，其中 props 是原始数据类型。

```
const sourceArray = [{ a: 1}, { b: 2 }, { c: 3}];
const clonedArray = sourceArray.map(item => ({...item})); 
```

Enter fullscreen mode Exit fullscreen mode

#### 向数组中添加新元素。

```
const sourceArray = [1,2,3];
const newArray = [...sourceArray, 4];
const sourceArray = [{ a: 1}, { b: 2 }, { c: 3}];
const newArray = [...sourceArray, { d: 4}]; 
```

Enter fullscreen mode Exit fullscreen mode

#### 从数组中删除一个元素。

```
const itemToRemove = 3;
const sourceArray = [1,2,3];
const newArray = sourceArray.filter(item => item !== itemToRemove); 
```

Enter fullscreen mode Exit fullscreen mode

#### 将一个元素替换成一个数组。

```
const itemToAdd = { id: 2, a: 4 };
const sourceArray = [{id: 1, a: 1}, {id: 2, a: 2}, {id: 3, a: 3}];

// replacing without caring about position
const newArray = [...sourceArray.filter(item => item.id !== itemToAdd.id), itemToAdd];

// replacing caring about position
const indexOldElement = sourceArray.findIndex(({ id }) => id == itemToAdd.id);
const newArray = Object.assign([...sourceArray], {[indexOldElement]: itemToAdd});

// or you can do
const newArray = [...sourceArray.slice(0, indexOldElement), itemToAdd, ...sourceArray.slice(indexOldElement + 1)] 
```

Enter fullscreen mode Exit fullscreen mode

### 对物体的操作。

#### 添加新道具。

```
const sourceObj = { a: 1, b: 2};
const newProp = { c: 3 };
const newObj = { ...sourceObj, ...newProp};
// or you can do
const c = 3;
const newObj = { ...sourceObj, c};
// newObj = { a: 1, b: 2, c: 3}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 卸下一件道具。

```
const sourceObj = { a: 1, b: 2, c: 3};
const { b, ...newObj } = sourceObj;
// console.log(newObj) => { a: 1, c: 3}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 更新一个嵌套的对象，该对象的道具是图元。

```
const sourceObj = { a: 1, b: 2, c: { d: 3, e :4 } };
const c = { ...sourceObj.c, f: 5 }
const newObj = { ...sourceObj, c }; 
```

Enter fullscreen mode Exit fullscreen mode

#### 更新一个道具不是图元的嵌套对象。

```
const sourceObj = { a: 1, b: 2, c: { d: [1, 2, 3 ], e :4 } };
const d = [ ...sourceObj.c.d, 4 ];
const c = { ...sourceObj.c, d }
const newObj = { ...sourceObj, c }; 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，将不可变更新正确应用到嵌套对象的过程很容易变得冗长和难以阅读。

## 表演上话不多？

创建一个新对象在时间和内存消耗方面更昂贵，但是在很多情况下，这些缺点比优点要小。

在开始编码之前，您应该考虑这样的优势，例如能够快速比较两个不可变的对象，只依赖于 identity/strict equality 操作符检查`oldObject === newObject`，或者减少在跨不同组件共享数据结构的应用程序中可能发生的令人讨厌的错误的可能性。

## 结论

如果您目前正在使用或学习任何基于不变性的库，上述所有操作都可以帮助您。我希望你喜欢看这篇短文，欢迎反馈！