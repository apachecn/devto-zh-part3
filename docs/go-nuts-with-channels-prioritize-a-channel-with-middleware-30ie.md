# 疯狂使用渠道:优先使用中间件的渠道

> 原文：<https://dev.to/tonymet/go-nuts-with-channels-prioritize-a-channel-with-middleware-30ie>

**更新**加了一个截止日期

通道很棒，因为你可以很容易地将它们从 fifos 重新连接到多路复用器、漏斗和扇出。

在这种情况下，我们希望实现一个优先级 FIFO-P 可以是一个 TTL 或值指示器。

不需要太多，只是一个自定义的 impl。基于您的订购字段`P` -优先。然后把`inChan`读入 buf，排序，并写到`outChan`

```
package main

import (
    "fmt"
    "math/rand"
    "sort"
    "time"
    "strconv"
    "context"
)

type PriorityStruct struct {
    P    int
    name string
}

const BUFSIZE = 10

type PriorityChan chan PriorityStruct
type PriorityBuf []PriorityStruct

func (s PriorityBuf) Len() int {
    return len(s)
}
func (s PriorityBuf) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}
func (s PriorityBuf) Less(i, j int) bool {
    return s[i].P < s[j].P
}

func Sender(pchan PriorityChan) {
    for {
        randNum := rand.Intn(3)
        pchan <- PriorityStruct{
            P:    randNum,
            name: "Bob-" + strconv.Itoa(randNum),
        }
        time.Sleep(10 * time.Millisecond)
    }
}

func Prioritize(ctx context.Context, inChan PriorityChan, outChan PriorityChan) {
    for {
        buf := make(PriorityBuf, BUFSIZE)
        var i int
        for i = range buf {
            select{
            case buf[i] = <-inChan:
            case <-ctx.Done():
                break
            }
            buf[i] = <-inChan
        }
        sort.Sort(buf)
        for j:= 0; j <= i; j++ {
            outChan <- buf[j]
        }
    }
}

func Receiver(rchan PriorityChan) {
    for {
        fmt.Printf("%v\n", <-rchan)
    }
}

func main() {
    inChan := make(PriorityChan)
    outChan := make(PriorityChan)

    ctx, cancel := context.WithDeadline(context.Background(), time.Now().Add(50 * time.Millisecond))
    defer cancel()
    go Sender(inChan)
    go Receiver(outChan)
    go Prioritize(ctx, inChan, outChan)
    <- time.After(5 * time.Second)
} 
```

### 运行它

```
go run priority-channel.go |head -n 15
{0 Bob-0}
{0 Bob-0}
{0 Bob-0}
{1 Bob-1}
{1 Bob-1}
{1 Bob-1}
{2 Bob-2}
{2 Bob-2}
{2 Bob-2}
{2 Bob-2}
{0 Bob-0}
{0 Bob-0}
{1 Bob-1}
{1 Bob-1}
{2 Bob-2} 
```

来看看:[https://play.golang.org/p/wWgi5rPcIwb](https://play.golang.org/p/wWgi5rPcIwb)

如果你还没有上 [go-nuts](https://groups.google.com/forum/#!forum/golang-nuts) -这是一个不错的 go-users 社区。

[![](img/9d1667b9018c155b092587bcc966e173.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9HUstYFc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pdnr8avjdauq93cddb0f.png)