# 我犯的 Go 并发错误

> 原文：<https://dev.to/dannypsnl/the-go-concurrency-bug-i-made-3dma>

有一种说法是:

> 我从来没有一片特别大而宽的面包不掉在地板上，而且总是涂有黄油的一面

即使我已经和 Go 一起工作了将近 3 年，我仍然做了这些愚蠢的错误。但是从错误中学习是我们成为专业工程师的原因。因此，我将列出我犯的错误，并说明如何避免它。

### 1 用发电机选择

```
func events() <-chan int {
    ch := make(chan int)
    go func() {
        for {
            ch <- 1
        }
    }()
    return ch
}

func main() {
    for {
        select {
        case i := <- events():
            println("i=", i)
        }
    }
} 
```

我们在这里使用一个通用的生成器模式，`select`也是很正常的情况，这里的问题是 select 将调用`events`不仅仅一次！这个循环将为每个`case`语句创建新的通道！留下无限无人问津的围棋套路！

为了避免这个问题，你必须使用`range` :

```
func main() {
    for i := range events() {
        // ...
    }
} 
```

但是如果你想停止这种循环，也就是说你仍然需要使用`select`，那么就需要将通道存储到其他地方。有许多方法可以做到这一点:

*   在结构上:

```
type eventGenerator struct {
      eventCh chan int
      ctx context.Context
      cancel context.CancelFunc
  }

  func NewEventGenerator(ctx context.Context) *eventGenerator {
      // better to get context from others place, even this is a most up level controller
      // because you can use `context.Background()` as argument if this is the most up level one
      ctx, cancel := context.WithCancel(ctx)
      return &eventGenerator{
          // don't forget to `make` a channel,
          // if you skip it, Go won't give you any warning
          // And anything you try to send to it would be ignored!
          // No Warning!
          eventCh: make(chan int),
          ctx: ctx,
          cancel: cancel,
      }
  }

  func (e *eventGenerator) Start() {
      go func() {
          defer close(e.eventCh)
          for {
              select {
              case _, closed := <- e.ctx.Done():
                  if closed {
                      return
                  }
              default:
                  e.eventCh <- 1
              }
          }
      }()
  }
  func (e *eventGenerator) Events() <-chan int { return e.eventCh }
  func (e *eventGenerator) Close() { e.cancel() } 
```

现在你可以在调用`eg.Start()`后随心所欲地写`case <-eg.Events():`并由`eg.Close()`停止

*   带外部通道的发电机

```
func genEvents(ch chan int) {
      go func() {
          for {
              ch <- 1
          }
      }()
  }
  func main() {
      d := time.Now().Add(50 * time.Millisecond)
      ctx, cancel := context.WithDeadline(ctx, d)
      defer cancel()
      ch := make(chan int)
      genEvents(ch)
      for {
          select {
          case i := <-ch:
              println("i=", i)
          case <-ctx.Done():
              println("main:", ctx.Err().Error())
              close(ch)
              return
          }
      }
  } 
```

### 2 误用上下文。完成()

假设有一个类似`epoll`的函数调用`recv()`，你会从中获取一些东西并处理它，但是它不是基于通道的，这意味着你不能把它作为`select`的`case`，如何处理它？

```
func handlingRecv(ctx context.Context) <-chan interface{} {
    ch := make(chan interface{})
    go func() {
    defer close(ch)
        for {
            data := recv()
            var v interface{}
            err := json.Unmarshal(data, v)
            // ignore error handing
            ch <- v

            select {
            case _, closed := <-ctx.Done():
                return
            }
        }
    }()
    return ch
} 
```

代码好看？不，事实上`select`会被阻塞，直到这个上下文被取消，这意味着你只能从`recv()`得到一条消息，而且没有警告，看起来像是一个**很好的**网络问题，但它实际上是一个代码错误。

这个 bug 很容易修复，事实上，比上一个容易很多。

```
select {
case _, closed := <-ctx.Done():
    return
default:
    // move job to here
} 
```

这么容易，我们只要根据事实，如果没有案子在，它就会做`default`屏蔽工作

### 结论

这里显示的 bug 可能不难解决，但是由于一切都可能出错，所以我还是把它写好了，如果有帮助的话，那就太好了。感谢阅读。