# 使用反射用 Goroutine 动态包装回调

> 原文：<https://dev.to/wallyqs/wrapping-callbacks-dynamically-with-a-goroutine-using-reflect-46dg>

最近，一位 NATS 用户在寻找一种通用的方法，能够
[增加异步 NATS 处理器订阅](https://github.com/nats-io/go-nats/pull/451)的并发性，默认情况下
按顺序处理消息。这意味着
如果一条消息需要很长时间来处理，那么下一条消息
的处理会有一些延迟，即使服务器已经
将它发送给客户端:

```
nc, err := nats.Connect("demo.nats.io")
if err != nil {
        log.Fatalf("Failed to create a connection: %v\n", err)
}

nc.Subscribe("foo", func(m *nats.Msg) {
        log.Printf("[Received on '>'  ] %+v", string(msg.Data))
        time.Sleep(1 * time.Second) // Blocks each message for 1 second
}) 
```

Enter fullscreen mode Exit fullscreen mode

绕过行阻塞的一个简单方法是
简单地添加一个 Goroutine:

```
nc.Subscribe("foo", func(m *nats.Msg) {
    log.Printf("[Received on '>'  ] %+v", string(msg.Data))
        go func(){
        // ... Processing ...
        time.Sleep(1 * time.Second) 
    }()
}) 
```

Enter fullscreen mode Exit fullscreen mode

这适用于常规的 NATS 连接和订阅，所以这是我经常推荐的方法。但是有一个例外，
是当使用 NATS 编码的连接时，函数
的类型可以是多种类型，NATS 客户端在内部
处理有效负载的序列化/反序列化:

```
package main

import (
    "log"
    "time"

    "github.com/nats-io/go-nats"
)

func init() {
    log.SetFlags(log.LstdFlags | log.Lmicroseconds)
}

func main() {
    nc, err := nats.Connect("demo.nats.io")
    if err != nil {
        log.Fatalf("Failed to create a connection: %v\n", err)
    }
    ec, err := nats.NewEncodedConn(nc, nats.JSON_ENCODER)
    if err != nil {
        log.Fatalf("Failed to create an encoded connection: %v\n", err)
    }
    defer ec.Close()

    type Event struct {
        Message string `json:"message"`
    }

    ec.Subscribe(">", func(b []byte) {
        log.Printf("[Received on '>'  ] %+v", string(b))
        time.Sleep(1 * time.Second)
    })

    ec.Subscribe("foo", func(s string) {
        log.Printf("[Received on 'foo'] %+v", s)
        time.Sleep(1 * time.Second)
    })

    ec.Subscribe("bar", func(e *Event) {
        log.Printf("[Received on 'bar'] %+v", e)
        time.Sleep(1 * time.Second)
    })

    payload := []byte("Hello World!")
    for i := 0; i < 3; i++ {
        ec.Publish("foo", payload)
        ec.Publish("bar", &Event{Message: string(payload)})
    }
    select {
    case <-time.After(2 * time.Second):
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，Goroutine 的简单变通方法不起作用了，因为
必须为多种函数类型实现它。这就是
,因为在内部，NATS 客户端使用`reflect`来找出如何
使用解码后的有效载荷正确地调用回调。

为了解决这个问题，我[挖掘出一条路](https://www.youtube.com/watch?v=_znElks13UA)
使用更多的反射来动态地重新包装一个函数，这样
就可以在它自己的例程
中运行

```
type subscriber interface {
    Subscribe(subject string, cb nats.Handler) (*nats.Subscription, error)
    QueueSubscribe(subject, group string, cb nats.Handler) (*nats.Subscription, error)
}

func Subscribe(ec subscriber, subject string, cb nats.Handler) (*nats.Subscription, error) {
    parallelize := func(f, fptr interface{}) {
        // Original function.
        oV := reflect.ValueOf(f)

        // Pointer to the function that will be replaced.
        ffn := reflect.ValueOf(fptr).Interface()
        fn := reflect.ValueOf(ffn).Elem()

        // Type of original function used to create new one
        // that will replace the original.
        typ := reflect.TypeOf(f)

        // Rewrap original function.
        handler := func(in []reflect.Value) []reflect.Value {
            go oV.Call(in)
            return nil
        }
        v := reflect.MakeFunc(typ, handler)
        fn.Set(v)
    }
    parallelize(cb, &cb)
    return ec.Subscribe(subject, cb)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，用户可以使用这个助手函数，它有一个与最初的
NATS 客户端 subscribe 类似的 API，尽管在内部，它在自己的 Goroutine:
中处理每个
消息

```
Subscribe(ec, ">", func(b []byte) {
        log.Printf("[Received on '>'  ] %+v", string(b))
        time.Sleep(1 * time.Second)
})

Subscribe(ec, "foo", func(s string) {
        log.Printf("[Received on 'foo'] %+v", s)
        time.Sleep(1 * time.Second)
})

Subscribe(ec, "bar", func(e *Event) {
        log.Printf("[Received on 'bar'] %+v", e)
        time.Sleep(1 * time.Second)
}) 
```

Enter fullscreen mode Exit fullscreen mode

完整示例如下:

```
package main

import (
    "log"
    "reflect"
    "time"

    "github.com/nats-io/go-nats"
)

type subscriber interface {
    Subscribe(subject string, cb nats.Handler) (*nats.Subscription, error)
    QueueSubscribe(subject, group string, cb nats.Handler) (*nats.Subscription, error)
}

func Subscribe(ec subscriber, subject string, cb nats.Handler) (*nats.Subscription, error) {
    parallelize := func(f, fptr interface{}) {
        // Original function.
        oV := reflect.ValueOf(f)

        // Pointer to the function that will be replaced.
        ffn := reflect.ValueOf(fptr).Interface()
        fn := reflect.ValueOf(ffn).Elem()

        // Type of original function used to create new one
        // that will replace the original.
        typ := reflect.TypeOf(f)

        // Rewrap original function.
        handler := func(in []reflect.Value) []reflect.Value {
            go oV.Call(in)
            return nil
        }
        v := reflect.MakeFunc(typ, handler)
        fn.Set(v)
    }
    parallelize(cb, &cb)
    return ec.Subscribe(subject, cb)
}

func init() {
    log.SetFlags(log.LstdFlags | log.Lmicroseconds)
}

func main() {
    nc, err := nats.Connect("demo.nats.io")
    if err != nil {
        log.Fatalf("Failed to create a connection: %v\n", err)
    }
    ec, err := nats.NewEncodedConn(nc, nats.JSON_ENCODER)
    if err != nil {
        log.Fatalf("Failed to create an encoded connection: %v\n", err)
    }
    defer ec.Close()

    type Event struct {
        Message string `json:"message"`
    }

    Subscribe(ec, ">", func(b []byte) {
        log.Printf("[Received on '>'  ] %+v", string(b))
        time.Sleep(1 * time.Second)
    })

    Subscribe(ec, "foo", func(s string) {
        log.Printf("[Received on 'foo'] %+v", s)
        time.Sleep(1 * time.Second)
    })

    Subscribe(ec, "bar", func(e *Event) {
        log.Printf("[Received on 'bar'] %+v", e)
        time.Sleep(1 * time.Second)
    })

    testBytes := []byte("Hello World!")
    ec.Publish("foo", testBytes)
    ec.Publish("foo", testBytes)
    ec.Publish("foo", testBytes)
    ec.Publish("bar", &Event{Message: string(testBytes)})
    ec.Publish("bar", &Event{Message: string(testBytes)})
    ec.Publish("bar", &Event{Message: string(testBytes)})
    ec.PublishRequest("bar", "inbox", &Event{Message: string(testBytes)})
    select {
    case <-time.After(2 * time.Second):
    }

} 
```

Enter fullscreen mode Exit fullscreen mode