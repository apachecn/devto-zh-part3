# 如何从带有值的 JSON 对象中获取索引:javascript

> 原文：<https://dev.to/anjan/how-to-get-index-from-a-json-object-with-value-javascript-35h1>

在现代浏览器中，您可以使用 findIndex:

```
var students = [
 {id: 100 },
 {id: 200},
 {id: 300},
 {id: 400},
 {id: 500}
];
var index = students.findIndex(std=> std.id === 200); 
```

但是这个功能并不被一些不太老的浏览器以及 IE 所支持(EDGE 支持)。下面是一个使用 [javascript](https://anjandutta.com/javascript-es6-default-parameter/) :
的变通方法，你可以使用`Array.forEach`或者`Array.find`或者`Array.filter`

```
var students = [
 {id: 100 },
 {id: 200},
 {id: 300},
 {id: 400},
 {id: 500}
];
var index = -1;
var needle = 200;
var filteredRes = students.find(function(item, i){
 if(item.id === needle){
 index = i;
 return i;
 }
});
console.log(index, filteredRes);
/*Result: 1 Object { id: 200 }*/ 
```

这种方法需要更多的开销，因为它遍历整个对象来搜索匹配。因此，对于冗长的 JSON 数据，不建议使用这种方法(即使它完成了工作)。