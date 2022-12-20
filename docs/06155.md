# 可怕的代码片段——用 Spread 操作符更新嵌套数组

> 原文：<https://dev.to/samrocksc/horrible-snippet-updating-an-nested-array-with-the-spread-operator-4918>

有时候我喜欢坐在那里，想一些奇怪的方法来完成事情，有时候我会想到这样的想法，我认为这可以应用到一些功能性的 JS 中

```
// I Really dig this one
const foo = {
  bar: {
    baz: [{ changeMe: 'asdf', notMe: 'asdf' }, { changeMe: 'ffff', notMe: 'asdf' }],
  },
};

const newArr = foo.bar.baz.map(baz => ({ ...baz, changeMe: 'swee' }));

const newData = {
  ...foo,
  bar: {
    baz: newArr,
  },
};

console.log('newData', newData.bar.baz); 
```