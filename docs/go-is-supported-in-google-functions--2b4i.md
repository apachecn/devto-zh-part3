# 谷歌功能支持 Go

> 原文：<https://dev.to/renatosuero/go-is-supported-in-google-functions--2b4i>

现在我们可以用 Go 运行 google functions =)。请看一个例子:

```
// Package helloworld provides a set of Cloud Function samples.
package helloworld

import (
        "fmt"
        "net/http"
)

// HelloGet is an HTTP Cloud Function.
func HelloGet(w http.ResponseWriter, r *http.Request) {
        fmt.Fprint(w, "Hello, World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

部署:

```
gcloud functions deploy HelloGet --runtime go111 --trigger-http 
```

Enter fullscreen mode Exit fullscreen mode

[https://www.youtube.com/embed/RbnyUpVRq_4](https://www.youtube.com/embed/RbnyUpVRq_4)