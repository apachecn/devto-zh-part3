# TIL: Datalist 元素可以有值和标签

> 原文：<https://dev.to/stefanjudis/til-datalist-elements-can-have-values-and-labels-289l>

今天我在看[一期](https://webplatform.news/issues/2019-04-10)的[时光视频](https://twitter.com/simevidas?lang=en)大[网络平台新闻](https://webplatform.news/)。他描述了一些对我来说很新鲜的东西——`datalist`元素不仅提供了可以选择的值，还提供了一种显示标签的机制。

```
<label>
 Animal:
 <input name=animal list=animals>
</label>
<datalist id=animals>
  <option value="Dog">🐶
  <option value="Cat">🐱
  <option value="Duck">🦆
</datalist> 
```

Enter fullscreen mode Exit fullscreen mode

上述标记会导致以下结果:

[![Input field with auto-completion including values and labels](img/0398e156be0a48a337e1fcded061fd5d.png)](//images.ctfassets.net/f20lfrunubsq/3qmuEeNOdRPOrmMq4M3KKW/f2d0ad766ec446161165c5aa2ebb7e57/datalist.jpg)

在我看来这很酷。:)

**但是要注意:在你开始使用它之前，一定要阅读[ime 关于它的文章](https://webplatform.news/issues/2019-04-10)。目前，有一个火狐漏洞极大地困扰了用户体验。**

如果你想玩玩它，看看它是如何工作的，你可以看看这个代码笔。