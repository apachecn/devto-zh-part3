# Nginx 作为负载平衡器(第三部分)

> 原文：<https://dev.to/rshiva/nginx-as-a-load-balancer-part-iii-39ad>

Nginx 作为负载平衡器，这是该系列的第三部分([第一部分](https://dev.to/rshiva/welcome-to-nginx-part-i-58mn)和[第二部分](https://dev.to/rshiva/welcome-to-nginx-part-ii-32kmn))。扩展服务器的第一步。让我们保持简单。

## **什么是负载均衡器？**

负载平衡器是一种用于在服务器之间分配流量的方法。在我们详细讨论负载平衡之前，有两种方法可以扩展服务器。首先是垂直扩展，在这种情况下，我们增加了更多的计算能力，即通过增加现有服务器的 RAM 和内核，但这在某一点后会很昂贵。第二，水平扩展是添加更多相同或不同配置的服务器，并安装所需的应用程序来运行我们的 web 应用程序。下图给出了垂直和水平缩放的要点

[![Vertical vs horizontal](img/5996631680deec49c391ddaf143a83ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pNjAvmdu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zjpwj28f1lptdfz6w8ej.png)

## **为什么需要它？**

提高应用程序性能和可用性，并在面临高流量时减少延迟。当我们的服务器无法处理太多的请求或者访问我们网站的用户数量增加时。我们的应用程序会变得很慢，用户会很恼火，因为完成任何任务所需的时间都会增加。这是糟糕的用户体验。这是我们扩展服务器的时候。

## **Nginx 作为负载均衡器**

Nginx 是一个软件负载平衡器。通过将请求分布在多个应用服务器上，它可以有效地用作 HTTP 负载平衡器，这将提高应用可用性(即使一个服务器关闭)、性能和可伸缩性。有些硬件负载平衡器非常昂贵，需要单独的资源来维护和调试，这可能会成为一项非常乏味的任务。成功的[案例研究](https://www.nginx.com/success-stories/)使用 nginx 作为负载均衡器的公司，以及一些用 nginx 替换其硬件负载均衡器的公司。

首先，我们需要一个专门的服务器，在那里我们只运行 nginx，它的工作只是向上游转发请求。让我们再添加三台应用服务器。我们现在将域或首次登陆请求指向 nginx 负载平衡器，而不是将应用指向我们的单个服务器。Nginx 将根据下面提到的技术决定请求应该转发到哪里。

[![Load balancer architecture](img/e0c55d5e739f374816f795d94887f299.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3azDLKPz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gfa0sb591p49r7sz3fb7.png) 
形象地展示了我们的建筑

### **按照下面的步骤开始-**

首先，让我们在内部创建一个名为 load_balancer.conf 的配置文件

```
 cd /etc/nginx/conf
    sudo touch load_balancer.conf 
```

Enter fullscreen mode Exit fullscreen mode

打开 load_balancer.conf 并添加以下配置。

```
 http {
        upstream wayne_tech {
            server server1.com;
            server server2.com;
            server server3.com;
        }

        server {
            listen 80;

            location / {
                proxy_pass http://wayne_tech;
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

让我们保存这个配置文件，并将它包含在我们的主配置文件中，然后重新启动服务器。如果你需要知道如何做，请参考第二部分的帖子。

server1.com、server2.com 和 server3.com，需要在/etc/hosts 中添加 IP 配置。就这么办吧。

```
 sudo vi /etc/hosts

    192.168.1.101   server1.com
    192.168.1.102   server2.com
    192.168.1.103   server3.com 
```

Enter fullscreen mode Exit fullscreen mode

现在所有的配置都完成了。当任何请求到达 web 应用程序时，它会落在负载平衡器上，并决定将请求转发到哪里。

nginx 支持不同的负载平衡技术。

### **1)循环赛(默认)**

默认情况下，使用循环算法，其中每个请求按顺序发送给上游服务器。即第一个请求将到达服务器 1，第二个请求将到达服务器 2，依此类推。

```
 upstream wayne_tech {

        server server1.com;

        server server2.com;

        server server3.com;

    } 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
**2)最少连通**

在这种技术中，nginx 不会使已经在处理大量请求的服务器过载；相反，它将请求定向到不太忙的服务器。Nginx 会将每个活动的连接与服务器进行比较，并将请求发送给最不活跃的连接。我们只需要在上游配置中添加一个 least_conn 指令

```
 upstream wayne_tech {

        least_conn;

        server server1.com;

        server server2.com;

        server server3.com;

    } 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
**3) ip 哈希**

如果您需要将一个客户机映射到一个特定的应用服务器，即保持客户机的持久性或维护一个状态，可以使用 ip-hash。请求是基于客户机的 IP 地址分发的，该 IP 地址被用作向服务器提供请求的散列密钥。这将确保相同的客户端请求被定向到相同的服务器，除非该服务器关闭。

```
 upstream wayne_tech {

        ip_hash;

        server server1.com;

        server server2.com;

        server server3.com;

    } 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
**4)加权负载均衡**

在这种方法中，我们给服务器加权。当我们的服务器在配置上不一致时，我们可以使用这个。例如，服务器 1 有 16GB RAM 和 8 核处理器，而其他两台服务器有 8GB 和 4 核处理器。下面的配置显示了权重{6，2，2}，这表明对于每 10 个请求，60%的请求被提供给服务器 1，20%的请求被定向到服务器 2，剩余的 20%被定向到服务器 3。

```
 upstream wayne_tech {

        server server1.com weight=6;

        server server2.com weight=2;

        server server3.com weight=2;

    } 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
**结论:**

这是 nginx 系列的最后一篇帖子。既然您已经知道了如何设置和安装服务器。如果您的应用程序接收流量负载，并使用这些技术之一在服务器上分配负载。我鼓励你去钻研 nginx 文档，去发掘它的威力，有一个 nginx plus 的企业版给了你更多的特性。我使用 Nginx 已经快十年了，我从来没有感觉到需要企业版，尽管我们每天每台服务器都要处理数百万个请求。如果你需要任何帮助，请联系我。这是所有的乡亲。