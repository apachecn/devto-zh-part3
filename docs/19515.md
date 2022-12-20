# 样式组件媒体查询

> 原文：<https://dev.to/rlesniak/styled-components-media-queries-----------------27mi>

在公司中，我们希望创建一些样式化组件助手来编写优雅的媒体查询，我们正在争论选择哪种方式:

*   使用插入字符串，如:

```
 styled.div`
        ${media.mobile`
          color: red
        `}
      ` 
```

*   或者只返回@media 字符串片段，如(例如@media(最小宽度:100px):

```
 styled.div`
        ${media.mobile} {
          color: red
        }
      ` 
```

这两种方法的缺点/警告是什么？