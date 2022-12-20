# JavaScript 中的数组

> 原文：<https://dev.to/christiangraves/arrays-in-javascript-1cb0>

数组是 JavaScript 语言中最重要的对象之一。它可用于表示和存储您将用于应用程序或网站的大量几乎任何数据类型。在基本层面上，它们可以用来存储事物的列表，比如名字或数字。下面是一个简单的名字数组的例子。

```
let names = ["Bill", "James", "Larry", "Tim"] 
```

上面的名字列表存储在一个名为 names 的变量中。这允许您根据变量声明的范围或位置，在代码的任何部分访问该列表。您可以从左到右按项目的位置号访问数组中的名称。请注意，数组中的位置不是从 1 开始，而是从 0 开始。您还需要使用括号符号来表示位置。

```
let leader;
let names = ["Bill", "James", "Larry", "Tim"]
leader = names[2]; //leader is set to Larry 
```

从上面的例子可以看出，我们通过访问变量 leader 的位置 2，将它设置为数组中的名称 Larry。同样，它不会是 3，因为位置从 0 开始。除了能够存储大量数据之外，真正让数组变得伟大的是内置的数组属性和方法(操作),它们允许您操作数据。我在下面的例子中包含了一些例子。

```
let names = ["James", "Bill", "Tim", "Larry"]
names.length// returns length of array: 4
names.sort(); //items in array are sorted: ["Bill", "James", "Larry", "Tim"]
names.push("Willy");/**item is added to the end of array and the length of array is returned: 
["Bill", "James", "Larry", "Tim", "Willy"] length of 5**/
names.pop();//the last item in the array will be removed and the item will be returned: "Willy" 
```

以上只是您可以在数组上使用的许多方法中的一部分，这些方法有助于以您需要的方式操作数据。可以在 w3schools [这里](https://www.w3schools.com/jsref/jsref_obj_array.asp)找到其他数组方法的很好的参考。