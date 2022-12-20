# 您更喜欢哪种通过 Flex 居中的方式？

> 原文：<https://dev.to/niorad/which-way-of-centering-via-flex-do-you-prefer-1i2d>

假设你想让一个元素水平居中。你喜欢哪一个？

```
.parent {
  display: flex;
  flex-direction: column;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
.parent {
  display: flex;
  justify-content: space-around;
} 
```

Enter fullscreen mode Exit fullscreen mode