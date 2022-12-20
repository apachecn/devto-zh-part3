# 构建 2MB REST API - Scratch Images 第三集

> 原文：<https://dev.to/tonymet/build-a-2mb-rest-api-scratch-images-episode-iii-1p9k>

📓[要旨](https://gist.github.com/tonymet/40e97d5d0bb0e79435b9925769bbe8b0)

最后一集， [100kB Docker 镜像和静态 ELF 二进制文件](https://dev.to/tonymet/build-100kb-docker-images-from-scratch-4ll5)大受欢迎。在继续之前，检查一下。

在这里，我们将通过构建一个简单的 Go REST API 代理来做一些更具说明性和有用的事情，该 API 被打包到一个微小的 **2MB** 中。这将表明我们可以做的不仅仅是 hello world，并说明如何将其他依赖项添加到暂存映像中。

## 其余 API

这个代理在`url`获取资源并返回它。这是缓存远程资源的常见模式。

例如:

```
$ curl  'http://localhost:8080/?url=https%3A%2F%2Fwww.httpbin.org%2Fget' \
| jq .headers
{
  "Accept-Encoding": "gzip",
  "Host": "www.httpbin.org",
  "User-Agent": "Go-http-client/1.1"
} 
```

`main.go`

```
package main

import (
    "fmt"
    "io"
    "log"
    "net/http"
    "os"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        url := r.FormValue("url")
        if url == "" {
            w.WriteHeader(http.StatusBadRequest)
            w.Write([]byte("\"url\" param is required"))
            return
        }
        resp, err := http.Get(url)
        if err != nil {
            w.WriteHeader(http.StatusInternalServerError)
            w.Write([]byte(fmt.Sprintf("Error fetching url [%s]: %s", url, err.Error())))
            return
        }
        // stream the resp body to our HTTP response, w
        writtenCount, err := io.Copy(w, resp.Body)
        if err != nil || writtenCount == 0 {
            w.WriteHeader(http.StatusInternalServerError)
            w.Write([]byte("Response was empty from url  " + url))
            return
        }
    })
    if port := os.Getenv("PORT"); port != "" {
        http.ListenAndServe(":"+port, nil)
    } else {
        log.Panic("PORT not set")
    }
} 
```

在高层次上，我们实现了一个传递给`HandleFunc`的处理程序，它调用`url`查询参数上的`http.Get(url)`，然后将`url`的主体传输到我们的客户端。

最后我们听环境传递的`PORT`。

## docker file

```
FROM golang:stretch AS build
WORKDIR /build
RUN apt-get update && \
    apt-get install -y xz-utils
ADD https://github.com/upx/upx/releases/download/v3.95/upx-3.95-amd64_linux.tar.xz /usr/local
RUN xz -d -c /usr/local/upx-3.95-amd64_linux.tar.xz | \
    tar -xOf - upx-3.95-amd64_linux/upx > /bin/upx && \
    chmod a+x /bin/upx
COPY . .
RUN GO111MODULE=off CGO_ENABLED=0 GOOS=linux \
    go build  -a -tags netgo -ldflags '-w -s'  main.go && \
    upx main

FROM scratch
COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=build /build/main /main
WORKDIR /
CMD ["/main"] 
```

这里我们展示几个新概念:

1.  对于 go，静态构建需要`-tags netgo`。如果你的构建以其他动态库结束，你可以使用`CGO_ENABLED=1 ... -extldflags "-static"`——这需要 C 工具链。
2.  `upx`—[前任终极打包器](https://github.com/upx/upx/releases)用于减少 70%左右
3.  当我们发出 https 请求时(在`http.Get()`调用期间)，我们将需要我们的客户端证书包`ca-certificates.crt`，它允许我们认证 https 服务器的证书。

## 建立形象

```
docker build . -t go-http-proxy 
```

```
docker images |grep go-http-proxy | awk '{print $7}'   
2.16MB 
```

比 100kb 多一点，但是 2MB 也不错！它比我们一直使用的 *806MB* *拉伸*图像小 99.5%。

```
docker images |grep c0167164f9fa | awk '{print $7}' 
806MB 
```

## 运行和测试

奔跑...

```
docker run -ePORT=8080 -p8080:8080 go-http-proxy 
```

试验...

```
curl  'http://localhost:8080/?url=https%3A%2F%2Fwww.httpbin.org%2Fget' | jq .headers
{
  "Accept-Encoding": "gzip",
  "Host": "www.httpbin.org",
  "User-Agent": "Go-http-client/1.1"
} 
```

## 里面有什么

这张图片有两层(每层一层`COPY` ) -让我们来看看

```
$ mkdir go-http-proxy &&  cd go-http-proxy 
$ docker save go-http-proxy |tar -x
$ find . -iname layer.tar|xargs -n 1 tar -tf
main
etc/
etc/ssl/
etc/ssl/certs/
etc/ssl/certs/ca-certificates.crt 
```

...仍然只有这两个文件。

## 下一步

在这一点上，我们需要把我们的游戏提升到像 nodejs 这样的脚本语言——在你的应用和运行时都包含更多的依赖。

➡️，让我们听听你缩小 docker 图片的建议。你希望看到什么样的平台从零开始构建？