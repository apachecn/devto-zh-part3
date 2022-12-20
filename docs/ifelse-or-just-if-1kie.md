# 如果/否则还是只是如果？

> 原文：<https://dev.to/tyrw/ifelse-or-just-if-1kie>

今天在一次代码审查中，我们有这样一个声明:

### 语句一

```
if (typeof val === 'string') {
    return 'A'
} else if (val === null || val === undefined) {
    return 'B'
} else {
    return val
} 
```

Enter fullscreen mode Exit fullscreen mode

有人建议改为如下:

### 语句 B

```
if (typeof val === 'string') return 'A'
if (val === null || val === undefined) return 'B'
return val 
```

Enter fullscreen mode Exit fullscreen mode

我不会告诉你我们从哪里出来的😜，但你觉得哪种方式更好？

简洁并避免逻辑的建议是个好建议，还是最初的方法更好？