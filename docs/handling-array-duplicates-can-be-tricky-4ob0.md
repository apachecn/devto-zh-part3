# 处理数组重复可能很棘手

> 原文：<https://dev.to/proticm/handling-array-duplicates-can-be-tricky-4ob0>

> 最初发布于[我的博客](https://www.devinduct.com/blogpost/17/handling-array-duplicates-can-be-tricky)。查看更多最新内容

让我们从定义一个简单的数组开始:

```
const cars = [
    'Mazda', 
    'Ford', 
    'Renault', 
    'Opel', 
    'Mazda'
] 
```

如你所见，第一项和最后一项是一样的。考虑到我们有一个原始类型的项目数组，找到这个重复项是很简单的。为了实现这一点，我们可以简单地在提供的回调中使用方法`filter`和方法`indexOf`。

```
const unique = cars.filter((car, idx) => cars.indexOf(car) === idx);
console.log(unique); // outputs ['Mazda', 'Ford', 'Renault', 'Opel'] 
```

注意，`indexOf`方法将返回数组中第一个出现的条目。这就是为什么我们可以在每次迭代中将`indexOf`方法返回的索引与当前索引进行比较，看当前项是否重复。

## 查找对象重复项

这是棘手的部分。对象通过引用而不是值或结构进行比较。这意味着如果我们比较两个完全相同的物体，它们不会匹配。我们不能简单地做类似`obj1 === obj2`的事情，因为他们被比较的方式。

```
const obj1 = {
   name: 'John',
   surname: 'Doe'
}

const obj2 = {
   name: 'John',
   surname: 'Doe'
}

const match = obj1 === obj2;
console.log(match) // outputs false 
```

现在，如果我们有一个对象副本数组呢？我们如何过滤掉这些呢？考虑到我们刚刚读到的，不可能使用像`indexOf`这样简单的东西。

示例数组:

```
const names = [{
   name: 'John',
   surname: 'Doe'
}, {
   name: 'Muhamed',
   surname: 'Ali'
}, {
   name: 'Mike',
   surname: 'Tyson'
}, {
   name: 'John',
   surname: 'Doe'
}, {
   name: 'John',
   surname: 'Doe'
}, {
   name: 'Mike',
   surname: 'Tyson'
}, {
   name: 'Mike',
   surname: 'Tyson'
}]; 
```

如你所见，我们有几个重复的项目。让我们实现查找重复项的函数。

## 加长版

在这种方法中，我们将手动遍历源数组(`forEach`方法)，并使用`find`方法检查结果数组中是否存在每一项。
考虑到我们有一个对象数组，我们必须比较当前对象的每个属性，以确保项目是相同的。该过程分解为几个步骤，如下所示:

1.  获取对象属性
2.  定义结果数组(`unique`和`duplicates`)
3.  遍历源数组
4.  尝试在`unique`数组中定位当前项目
5.  如果找到该项，将其推入`duplicates`数组，否则推入`unique`数组

```
const findDuplicates = (source) => {
    const keys = Object.keys(source[0]);
    let unique = [], duplicates = [];

    source.forEach((item, idx) => {

        if(idx == 0) {
            unique.push(item);
            return;
        };

        const resultItem = unique.find(resultItem => {
            let notFound = true;

            keys.forEach(key => {
                notFound = notFound && 
                    item[key] != resultItem[key];
            });

            return !notFound;
        });

        (!resultItem ? unique : duplicates).push(item);

    });

    return { unique: unique, duplicates: duplicates };
};

const result = findDuplicates(names);
console.log(result.unique, result.duplicates);

// expected output

// unique items

// 0: {name: "John", surname: "Doe"}
// 1: {name: "Muhamed", surname: "Ali"}
// 2: {name: "Mike", surname: "Tyson"}

// duplicate items

// 0: {name: "John", surname: "Doe"}
// 1: {name: "John", surname: "Doe"}
// 2: {name: "Mike", surname: "Tyson"}
// 3: {name: "Mike", surname: "Tyson"} 
```

## 稍短的版本

我们可以使用`reduce`方法来达到同样的目的。这是一个非常强大的方法，可以用来将数组转换成想要的结果。它接受回调作为参数，对数组中的每一项执行该参数。回调的返回值是每次迭代中修改的给定累加器。考虑到这不是一篇关于`reduce`方法的文章，请查阅官方 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

好了，回到我们的代码。`findDuplicates`方法的修改版本如下:

```
const findDuplicates = (source) => {
    const keys = Object.keys(source[0]);
    return source.reduce((acc, item) => {
        const resultItem = acc.unique.find(x => {
            let notFound = true;

            keys.forEach(key => {
                notFound = notFound && 
                    item[key] != x[key];
            });

            return !notFound;
        });

        (!resultItem ? acc.unique : acc.duplicates).push(item);
        return acc;
    }, {
        unique: [],
        duplicates: []
    })
}; 
```

修改后的版本应该返回与以前相同的结果数组。

```
// unique items

// 0: {name: "John", surname: "Doe"}
// 1: {name: "Muhamed", surname: "Ali"}
// 2: {name: "Mike", surname: "Tyson"}

// duplicate items

// 0: {name: "John", surname: "Doe"}
// 1: {name: "John", surname: "Doe"}
// 2: {name: "Mike", surname: "Tyson"}
// 3: {name: "Mike", surname: "Tyson"} 
```

就是这样。感谢您的阅读，下一篇文章再见。

# 进一步阅读

参见[这份备忘单](https://devinduct.com/cheatsheet/8/array-operations)，它将指导您了解数组操作的最常见用例。