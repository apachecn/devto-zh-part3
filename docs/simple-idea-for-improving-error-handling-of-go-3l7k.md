# 改进 Go 错误处理的简单想法

> 原文：<https://dev.to/chenge/simple-idea-for-improving-error-handling-of-go-3l7k>

以下是正常代码，重复太多，不太好。

```
_, err = fd.Write(p0[a:b])
if err != nil {
    return err
}
_, err = fd.Write(p1[c:d])
if err != nil {
    return err
}
_, err = fd.Write(p2[e:f])
if err != nil {
    return err
} 
```

改进代码:

```
type errWriter struct {
    w   io.Writer
    err error
}

func (ew *errWriter) write(buf []byte) {
    if ew.err != nil {
        return
    }
    _, ew.err = ew.w.Write(buf)
}

ew := &errWriter{w: fd}
ew.write(p0[a:b])
ew.write(p1[c:d])
ew.write(p2[e:f])
if ew.err != nil {
    return ew.err
} 
```

现在再加一层看起来更好。

你这样认为吗？

# 结束和引用

[重复中文帖子 Go](https://huoding.com/2019/04/11/728) 中的错误处理。