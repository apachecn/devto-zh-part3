# 的。every()和。JS 中的一些()数组方法:不受欢迎的一对

> 原文：<https://dev.to/nelsongutidev/the-every-and-some-array-methods-in-js-the-unpopular-pair-ahj>

ES5 出来的时候，引入了很多方便的方法，比如 forEach，map，reduce，filter。的。every()和。有些()也在当时推出，但没有那么受欢迎。我会用一些实际的例子和你分享他们能为你做什么。

## array . prototype . every()方法

该方法将回调函数作为参数，并检查数组中的每个元素是否都通过了在提供的函数中实现的测试。它对数组中的每个元素执行一次回调函数，直到找到一个返回 falsy 值的元素。如果发现一个假值，该方法立即返回*假值*。另一方面，如果回调函数只返回真值，则。every()方法返回 *true* 。

### 举例:

假设我们想知道来自`data`数组的每个人是否都大于 18 岁，以及每个人是否都有高级级别。我们可以使用。every()方法为此:

```
const data = [
  {
    name: 'John',
    gender: 'M',
    age: 25,
    level: 'mid',
  },
  {
    name: 'Jane',
    gender: 'F',
    age: 24,
    level: 'senior',
  },
  {
    name: 'Grace',
    gender: 'F',
    age: 30,
    level: 'senior',
  },
  {
    name: 'Mike',
    gender: 'M',
    age: 19,
    level: 'senior',
  },
]

console.log(data.every(i => i.age > 18)) // true

console.log(data.every(i => i.level === 'senior')) // false 
```

Enter fullscreen mode Exit fullscreen mode

### 与其他方法相结合

例如，如果您想知道是否所有女性都有“高级”级别，您可以将。带有的 every()方法。filter()方法。您可以首先过滤出对应于女性的对象，然后使用。every()方法:

```
console.log(data.filter(i => i.gender === 'F').every(i => i.level === 'senior')) // true 
```

Enter fullscreen mode Exit fullscreen mode

### 有趣的事实

如果。every()方法用于空数组，无论给定什么条件，它都将始终返回 true。

```
console.log([].every(i => i === 'hello')) // true

console.log([].every(i => i)) // true 
```

Enter fullscreen mode Exit fullscreen mode

## array . prototype . some()方法

类似于。every()方法，该方法对数组中的每个元素执行回调函数。与这个不同的是，每当它找到一个真值，它将立即返回 *true* 。因此，如果没有一个元素是真的，它将返回*假的*。

使用与之前相同的`data`,让我们使用。some()方法来确定在`data`数组中是否有“中级”人员和“低级”人员。

```
console.log(data.some(i => i.level === 'mid')) // true
console.log(data.some(i => i.level === 'junior')) // false 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将检查`data`数组中是否有 20 岁以下的人:

```
console.log(data.some(i => i.age < 20)) // true 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！希望你觉得这个有用。