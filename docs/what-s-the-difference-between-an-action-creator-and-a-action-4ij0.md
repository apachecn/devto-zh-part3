# 动作创作者和动作有什么区别？

> 原文：<https://dev.to/mcrowder65/what-s-the-difference-between-an-action-creator-and-a-action-4ij0>

一个动作只是一个有两个东西的对象:一个类型和一个有效负载。

动作创建器只是一个函数，它只返回一个动作。

我们为什么要关心动作创作者？我不认为人们应该去纠正那些不区分动作和动作创建者的人，但是让动作创建者成为一件好事，因为它提供了一种可以重用动作的方法，你可以在你的代码库中使用这些动作。

此外，您可以在其中添加逻辑，您可能不希望每次使用动作时都要记住这样做。

我最喜欢的例子是当你必须创建一个唯一生成的 id。

```
import shortid from "shortid";

export const addPost = ({ title, body }) => {
    return {
        title,
        body,
        id: shortid.generate()
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

你喜欢和你的动作创作者做什么？