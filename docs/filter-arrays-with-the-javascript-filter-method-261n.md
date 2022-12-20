# 用 JavaScript Filter()方法过滤数组

> 原文：<https://dev.to/sarah_chima/filter-arrays-with-the-javascript-filter-method-261n>

过滤掉不需要的元素来减少数组的大小很有趣，对吗？JavaScript 数组过滤方法使它更加有趣。

在我的上一篇博文中，我写了 JavaScript 中的数组映射方法。如果你错过了，这是文章的[链接。在这里的一个例子中，我们看到我们可以在回调函数中使用逻辑表达式来应用于数组中的元素。下面是代码片段:](https://dev.to/sarah_chima/the-javascript-array-map-method-44m0) 

```
 const numbers = [4, 9, 36, 49];

    let oddSquareRoots = numbers.map((num) => {
       if(num % 2 != 0) {
           return Math.sqrt(num)     
       }
       return num;
    })

    console.log(oddSquareRoots); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是可能的，但并不理想。更有效的方法是使用过滤方法。现在，让我们来回答这个问题:什么是过滤方法，我们如何使用它？

## 了解过滤方法

根据 MDN 文档:

> filter()方法创建一个新数组，其中所有元素都通过了由提供的函数实现的测试。

让我们通过一个例子来进一步简化这一点。

如果你有一组随机数字(比如人的年龄)，并且你只需要大于某个数字(比如 18)的数字。如何使用普通的 for 循环来实现这一点呢？

```
 const ages = [11, 34, 8, 9, 23, 51, 17, 40, 14];

    let olderThan18 = [];

    for (let i = 0; i < ages.length; i++) {
        if(ages[i] > 18){
            olderThan18.push(ages[i])
        }
    }

    console.log(olderThan18); // [34, 23, 51, 40] 
```

Enter fullscreen mode Exit fullscreen mode

使用如下所示的过滤方法会简单得多:

```
 const ages = [11, 34, 8, 9, 23, 51, 17, 40, 14];

    let olderThan18 = ages.filter((age) => age > 18);

    console.log(olderThan18); //[34, 23, 51, 40] 
```

Enter fullscreen mode Exit fullscreen mode

页（page 的缩写）学生:我们在示例中使用了箭头函数。如果你不明白它的语法，请参考[这个帖子上的](https://dev.to/sarah_chima/arrow-functions-in-es6-24)。

我们不用 for 循环也能得到同样的结果，而且更容易使用。

如果我们决定在使用 filter 方法之前声明回调函数，我们可以这样做:

```
 const ages = [11, 34, 8, 9, 23, 51, 17, 40, 14];
    const isOlderThan18 = (age) => age > 18;

    let olderThan18 = ages.filter(isOlderThan18);

    console.log(olderThan18); // [34, 23, 51, 40] 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看过滤方法的语法。

## Filter()方法的语法

过滤方法的基本语法如下:

```
 let newArray = array.filter(callback); 
```

Enter fullscreen mode Exit fullscreen mode

在哪里

*   newArray -返回的新数组
*   array -调用 filter 方法的数组
*   callback -应用于数组中每个元素的回调函数

回调函数可以接受三个参数

*   元素-正在处理的当前元素
*   index -正在处理的当前元素的索引
*   array -调用过滤器的原始数组

记住这些参数，我们也可以将语法写成:

```
 let newArray = array.filter(callback(element, index, array));

   or

   let newArray = array.filter((element, index, array) => {
        //filter 'em elements
    }) 
```

Enter fullscreen mode Exit fullscreen mode

##### 以下是关于 filter 方法的一些注意事项。

1.  它返回一个新的数组。

2.  它不会改变调用它的原始数组，即原始数组保持不变。

3.  filter 方法处理的元素范围是在第一次调用之前设置的。如果在映射开始后向数组中添加新元素，回调将不会处理它。

## 何时使用过滤方法

当您只需要数组中满足所需条件的项目时。

## 使用筛选方法的示例

这里有两个例子来说明我们可以用 filter 方法做什么。

#### 示例 1:过滤对象数组

如果您有一组国家，并且您只想要某个大陆上的国家。这是一个使用数组过滤方法的例子。

```
 const countries = [
        { name: 'Nigeria', continent: 'Africa'},
        { name: 'Nepal', continent: 'Asia'},
        { name: 'Angola', continent: 'Africa'},
        { name: 'Greece', continent: 'Europe'},
        { name: 'Kenya', continent: 'Africa'},
        { name: 'Greece', continent: 'Europe'}
    ]

    let asianCountries = countries.filter(country => {
        return country.continent === 'Asia';
    })

    console.log(asianCountries); // [{name: "Nepal", continent: "Asia"}] 
```

Enter fullscreen mode Exit fullscreen mode

#### 示例 2:在数组中搜索特定的字母

给定一组单词，你想找出哪个单词包含特定的字母。例如，你想让女性的名字包含特定的字母，你可以这样做。

```
 const names = ['Victoria', 'Pearl', 'Olivia', 'Annabel', 'Sandra', 'Sarah'];
    const filterItems = (letters) => {
        return names.filter(name => name.indexOf(letters) > -1);
    } 

    console.log(filterItems('ia')); // ["Victoria", "Olivia"] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

所以在这篇文章中，我们看到了 JavaScript Filter 方法过滤数组中的元素是多么容易。

还记得本文开头提到的例子吗？你能试着用过滤的方法达到同样的效果吗？

有什么问题或补充吗？请留言评论。

感谢您的阅读:)

#### 无耻的插头🙈

如果你想了解我更多，这里有一个链接到我的网站。