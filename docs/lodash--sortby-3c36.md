# lodash _。排序比

> 原文：<https://dev.to/mouseannoying/lodash--sortby-3c36>

我需要以一种任意的方式对一组对象进行排序，使用对业务有意义但不一定可排序的字符串。不幸的是，我必须支持 IE11，所以不能做花哨的 JS。谢天谢地，我有机会接触到洛达什。总之，我想到了这个:

```
var statuses = ["one", "two", "three", "four"];

var complexObj = { 
  dataset: [
    { 
      label: "two"
    },{ 
      label: "four"
    },{ 
      label: "three"
    },{ 
      label: "one"
    },
  ]
};

console.table(complexObj.dataset);

complexObj.dataset = _.sortBy(complexObj.dataset, function(n) {
  return _.indexOf(statuses, n.label);
});

console.table(complexObj.dataset) 
```

这很有效，一旦我计时，我需要返回排序后的数组，而不仅仅是排序。