# 出口违约的问题

> 原文：<https://dev.to/veebuv/the-issue-with-export-default-4nbc>

“等等，`export default`有问题？”-那也是我的反应

`export default`的问题归结于一致性和可伸缩性。当你构建代码为你和你的测试人员服务时，几乎一切都是完美的，但是当事情发展，你的团队发展，你需要确保实践和模式是为规模而构建的。

规模最重要的**模式之一是模块化和组件化，这是 React、Vue 等流行的词汇。无论如何，关注点的解耦和分离的概念在寻求规模的过程中是非常重要的——在我的职业生涯中，在教授和经历了 1000 个拉请求之后，我注意到的一个常见问题是当使用*导出缺省值*来公开一个组件时，缺乏一致性。** 

```
export default (obj) => _.reduce(obj, (accum, val, key) => {
    if (!key) return accum;
    accum[key] = val;
    return accum;
}, {}) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个简单的函数，它删除了一个对象的任何空值。为了这个博客，我们将它放在一个帮助者文件夹中，并将其命名为 *cleanObject.helpers.js*

一切都很好，除了当你开始看到人们在项目中以各种方式导入这个助手的时候

```
// One file
import cleanObject from '@/helpers/cleanObject.helpers'
// Another file
import ObjectCleaner from '@/helpers/cleanObject.helpers'
// One more
import NullChecker from '@/helpers/cleanObject.helpers' 
```

Enter fullscreen mode Exit fullscreen mode

当然，你可以期望人们把它放在文件名的字面上，但是这并不像预期的那样经常发生。

> 那么，对于这一切，有什么神奇的炒作方法呢？

很简单，只需停止默认导出，使用`export const`，因为现在您已经得到了要导出的命名函数，导入器被约束到作者定义的命名约定，这保持了一致性。

或者另一种方法，是在 hashmap/object 中导出函数

```
// ... code
export default { cleanObject: <name of function above> } 
```

Enter fullscreen mode Exit fullscreen mode

这就是让你的代码模式更加一致的好方法💪

[![](img/a456eb4c00a299a6ac3808bcf94dbf26.png)](https://i.giphy.com/media/ikMSB7JPYY1jy/giphy.gif)

* * *

在 LinkedIn || [Twitter](https://twitter.com/veebuv) 上关注我，会有更多文章

如果你有一个你想让我写的话题，发微博给我😀

此外，我总是喜欢这方面的反馈，如果在评论中有帮助的话👇