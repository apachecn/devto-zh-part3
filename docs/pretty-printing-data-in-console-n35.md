# 在控制台中漂亮地打印数据

> 原文：<https://dev.to/nikhilvats/pretty-printing-data-in-console-n35>

如果您是一名前端开发人员，您一定花了大量的时间来研究来自后端的复杂嵌套数据的结构。没有人喜欢去浏览器的控制台点击那些小箭头，只看到数百行杂乱的数据。阅读这篇文章，通过学习两种更好的将数据记录到控制台的方法来提高你的效率。

提示:单击图像放大。

### 1。使用 console.table()

您可以使用`console.table(data)`将数据(对象的数组，数组的数组)打印成表格的格式。

请注意，在**中，当数据只是字符串或数字的数组时(数据不是复合的)，Chrome** `console.table(data)`有时可能无法工作。在这种情况下，只需使用`console.table([data])`。

```
// Syntax - 
console.table(data [, columns]) 
```

Enter fullscreen mode Exit fullscreen mode

该函数有两个参数-

1.  要漂亮打印的数据。[ **强制**
2.  包含要包含在输出中的列的数组。[可选]

例-

```
// an array of objects, logging only firstName

function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

var john = new Person("John", "Smith");
var jane = new Person("Jane", "Doe");
var emily = new Person("Emily", "Jones");

console.table([john, jane, emily], ["firstName"]); 
```

Enter fullscreen mode Exit fullscreen mode

结果-
[![console.table-result](img/97d9ad9b28783d736bc4d59571ac0c4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9YwuiCwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/gczhq6g11x4xkp2bj9bt.png)

您还可以通过单击列标题中的箭头，对表格中的任何字段进行升序或降序排序。第二列右上角的向上箭头表示该表是根据名字的升序排列的。

请记住，IE 不支持`console.table()`和**。**

### 2。将 console.log()与 JSON.stringify()一起使用

您也可以使用`console.log(JSON.stringify(data, undefined, 4))`来美化数据，用空格来增加可读性。

`JSON.stringify()`采用 3 个参数-

*   第一个参数包含数据。[ **强制**
*   第二个是 replacer 参数，可以是数组或函数。该参数用于过滤对象的属性(仅打印某些选定的字段)。[可选]
*   第三个是空格数，通过格式化来提高数据的可读性。最大值可以是 10。[可选]

例-

```
function replacer(key, value) {
  // Filtering out properties
  if (typeof value !== 'string') {
    return undefined;
  }
  return value;
}

var foo = {foundation: 'Mozilla', model: 'box', week: 45, transport: 'car', month: 7};
console.log(JSON.stringify(foo, replacer,4));

// Alternatively we could have just used console.log(JSON.stringify(foo, ['week', 'month'])); 
```

Enter fullscreen mode Exit fullscreen mode

[![JSON.stringify-result](img/4c401edef619453656ef54b103931b50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wFmznYeJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/htmu6rmyt7n4o2wozg2i.png)