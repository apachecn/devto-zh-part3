# 通过 http 流式传输 cli 输出

> 原文：<https://dev.to/pratham/stream-cli-output-over-http-258i>

### 隧道

一个通过 http 传输任何命令行输出的程序。当我们想在客户端显示命令输出时，这很有用。一个这样的用例可以是在服务器端的容器内运行的浏览器中显示实时日志。

#### 安装:

*   使用 docker(推荐)

```
 git clone https://github.com/VeritasOS/runnel.git
  cd runnel

  # build docker image
  docker build -t runnel:latest .

  # start runnel server
  docker run --name runnel -d -p 127.0.0.1:9090:9090 runnel:latest 
```

Enter fullscreen mode Exit fullscreen mode

*   使用二进制(不推荐，因为系统依赖关系在 docker 文件中代表你)

```
 git clone https://github.com/VeritasOS/runnel.git
  cd runnel

  ./bin/linux_64/runnel_server -p localhost:9090 
```

Enter fullscreen mode Exit fullscreen mode

#### 用法:

*   触发以下命令以获取实时流

```
 # Fire command with curl
  curl -X POST -H 'Content-Type: application/json' -d '{"cmd":"ping -c 3 google.com"}' http://localhost:9090/command

  # Get live output stream
  # replace uuid with one which you get from above command
  while true; do curl -sS -H 'Content-Type: application/json' 'http://localhost:9090/stream/fd4b1a38-94f4-4eba-80e7-50578ac4baae' | jq '.response'; done 
```

Enter fullscreen mode Exit fullscreen mode

#### 包:

*   相关性:安装重定向服务器
*   用作 golang 包

```
 # Get lib
  go get github.com/veritasos/runnel/runnel

  # Fire command
  client := runnel.NewClient()
  key, err := client.RunCommand("ping", "-c 2 google.com")

  # Get output stream
  client := runnel.NewClient()
  output, err := client.Stream(key, 10) 
```

Enter fullscreen mode Exit fullscreen mode

#### 详细文档:

*   这里提供了更详细的文档
***   [**随时贡献回来**](https://github.com/VeritasOS/runnel)**