# 在 javascript 中重构时重命名变量

> 原文：<https://dev.to/stephencweiss/rename-variable-while-destructuring-in-javascript-5g3g>

我最近遇到一种情况，在一个组件中有两个不同的道具，它们有相同的名字(它们在对象的不同部分)。

我想同时使用两者并比较它们，但也想使用析构，这样我就不必钻透对象来找到它们。

这让我想知道我是否能够在析构对象时简单地重命名。

事实证明你可以。

想象如下:

```
const me = {
  name:'stephen',
  family: {
    wife : {
      name: 'kate'
    },
    animal: {
      name : 'finn',
      type: 'dog',
    },
  }
} 
```

所以，我希望能够访问我的名字，我妻子的名字，和我的狗的名字。

我可以用析构来做，就像这样:

```
const { name } = me
const { name: wife } = me.family.wife
const { name: dog } = me.family.dog 
```

我真的希望它能做的唯一一件事*不能做的*是允许在对象内进行额外的重组*并将它合并成一行。也就是说，下面的*不会*工作:`const {name, family.wife.name: wife, family.dog.name: dog} = me`*

哦好吧。

韦斯·博斯在这个话题上有一个有用的帖子。看看这个。