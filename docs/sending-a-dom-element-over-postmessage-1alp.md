# 通过 postmessage 发送 DOM 元素

> 原文：<https://dev.to/chinchang/sending-a-dom-element-over-postmessage-1alp>

我有一个要求，我想通过 [`postMessage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) 发送一个 DOM 元素。如果您以前尝试过，您会知道这是不可能的，因为 DOM 元素是不可克隆或不可序列化的，无法发送`postMessage`。所以我开始写一个函数，把一个 **DOM 元素**转换成类似于 DOM 元素的东西**，我可以把它发送给`postMessage`。**

**注意**:对于我的用例，我最终想要一个对象，在其上我可以访问琐碎的事情，比如`tagName`、`childNodes`、`nodeType`等等。所以，如果我在我的假 DOM 元素上得到这些属性，我是好的。

## 小功能小功能

```
function getFakeDomElement(node) {
    const fakeNode = {
        nodeName: node.nodeName,
        nodeType: node.nodeType,
        tagName: node.tagName,
        childNodes: [...node.childNodes].map(child => getFakeDomElement(child)),
        textContent: node.textContent
    }
    if(node.attributes) {
        fakeNode.attributes = [...node.attributes].map(attribute => ({name:attribute.name, value:attribute.value}))
    }
    return fakeNode;
} 
```

让我简单介绍一下这是做什么的。

*   首先，这是一个递归函数。也就是说，它为子节点调用自己。参考线`[...node.childNodes].map(child => getFakeDomElement(child))`。
*   我们为每个节点创建一个新对象，并简单地在新节点上复制所有需要的属性。
*   `childNodes`和`attributes`需要特殊处理，因为不能简单复制。这就是我们的 DOM 元素最初不可克隆的原因。
*   对于`childNodes`来说，很简单。我们只是在每个子节点上运行我们的`getFakeDomElement`函数来得到一个假的子节点。
*   对于属性，如果我们找到了，我们把它映射到一个对象数组中。每个对象的结构都是`{name, value}`——类似于访问属性的方式。

我们完了。我们最终得到了一个假的 DOM 元素，我们可以像 DOM 元素一样迭代和访问它，也是在`postMessage`之上。

[在 twitter 上关注我](https://twitter.com/chinchang457)以获得更多类似的帖子和我作为独立产品制造商的旅程。