# 我非常喜欢围棋的一点是

> 原文：<https://dev.to/kilgaloon/one-thing-i-really-like-about-go-2egf>

我已经很喜欢用 GO 工作了，最近我在改进从 unix 套接字切换到 HTTP 的 API，我实现了一个非常简单的东西，当我发现要改进什么，改变什么，什么可以做得更好时，我想到了这个。然后这个想法出现在我的脑海中，给原始类型添加方法，就像这样:

```
type Cmd string

func (c Cmd) agent() string {
    s := strings.Fields(string(c))
    return s[0]
}

func (c Cmd) command() string {
    s := strings.Fields(string(c))
    return s[1]
}    

func (c Cmd) args() []string {
    s := strings.Fields(string(c))
    return s[2:]
} 
```

我希望从你们那里听到更多关于这方面的信息，想法，实现等等...