# JavaScript 对象传播解释

> 原文：<https://dev.to/mcrowder65/javascript-object-spread-explained-415h>

如果您想在创建新对象的同时更新对象的一部分，那么可以使用扩展语法。

当你看到`...`的时候，你应该只是想到展平物体。

当你看到这个:

```
const metadata = {
  first: "Matt",
  last: "Crowder"
};

const updatedMetadata = {
  ...metadata,
  last: "Jenkins"
}; 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到`updatedMetadata`是这样的:

```
const updatedMetadata = {
  first: "Matt",
  last: "Crowder",
  last: "Jenkins"
}; 
```

Enter fullscreen mode Exit fullscreen mode

键/值分配是从上到下读取的，因此最后一个键的值将优先设置值。

现在`updatedMetadata`的值为:

```
{
  first: "Matt",
  last: "Jenkins"
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，无论您将对象放在哪里，跨页都会将其展平，因此，如果我们将跨页元数据*放在* `last: "Jenkins"`之后，我们将不会得到更新！

```
const metadata = {
  first: "Matt",
  last: "Crowder"
};

const updatedMetadata = {
  last: "Jenkins",
  ...metadata
};

// results in 

const updatedMetadata = {
  last: "Jenkins",
  first: "Matt",
  last: "Crowder"
};

// which gives us nothing 
```

Enter fullscreen mode Exit fullscreen mode

所以在放置 spread 语法时要小心！