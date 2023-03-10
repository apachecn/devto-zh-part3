# Golang REST APIs 的日志请求体

> 原文：<https://dev.to/nandotheessen/logging-request-bodies-for-golang-rest-apis-9hh>

Go 本身并不支持 JSON，所以我们的第一步应该是学习如何解码 JSON 格式。谢天谢地，Go 附带了“json”包，使得解码变得简单方便。

## 一个基本例子

这个简化的例子来自 [Go 文档](https://golang.org/pkg/encoding/json/#Decoder):

```
const jsonStream = `{"Name": "Ed", "Text": "Knock knock."}`

type Message struct {
    Name, Text string
}
var m Message

dec := json.NewDecoder(strings.NewReader(jsonStream))
err := dec.Decode(&m)
if err != nil && err != io.EOF {
    // handle that error
}

fmt.Println(m.Name, m.Text) 
```

在将我们的 JSON 流交给阅读器之后，我们使用`json.NewDecoder()`和`dec.Decode(&m)`将数据“翻译”并读取为之前定义的`Message`格式。

## 请求正文

当处理 HTTP 请求时，我们已经在做一些非常类似的事情，这有助于我们跳过这里的一个步骤。我还将向您展示一个漂亮的工具，我们将使用它进行日志输出！

```
func (h *handlerShared) doSomething(w http.ResponseWriter, r *http.Request) {
    // we still need a type definition for the receiving struct! 

    type Request struct {
        CompanyName string
    }
    // create a pointer to said struct
    req := &Request{}

    // create Decoder to "translate" our data
    // you probably spotted the step we can skip already!
    // if you haven't: the request body already comes in a Reader,
    // we don't have to hand it to one!
    decoder := json.NewDecoder(r.Body)

    err := decoder.Decode(&req)
    if err != io.EOF {
        // handle the error 
    }

    // At this point, we can a) access the data we read into our Request
    // struct: 
    fmt.Println(req.CompanyName)

    // As well as log it using the logrus logger I've included on my handlers                    
    h.log.WithFields(logrus.Fields{
        "host":     r.URL.Host,
        "path":     r.URL.Path,
        "header":   r.Header,
        // as you can see, there is a lot the logger can do for us
        // however "body": r.Body will not work, and always log an empty string!
        "body":     req
        // this is why we'll log our crated struct instead.
    })
    // send a response!
} 
```

这是一个非常简单的例子，但是应该可以让您很好地了解如何为您的端点实现日志记录。

我见过一些例子，它们更进一步，将主体读入输入缓冲区，然后将其转换为字符串。那是不必要的。

如果你知道一个更简单的方法来实现这一点，或者有任何其他反馈，请让我知道。我会更新这篇文章，并且总是渴望学习！