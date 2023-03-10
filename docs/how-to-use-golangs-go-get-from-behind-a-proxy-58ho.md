# 如何从代理后面使用 Golang 的“go get”。

> 原文：<https://dev.to/david_j_eddy/how-to-use-golangs-go-get-from-behind-a-proxy-58ho>

在一个完美的世界里，雇主会信任他们的员工，政府会关心普通人，猫和狗会和睦相处。不幸的是，现实情况是许多组织必须使用流量代理来保证安全性和遵守联邦法规。幸运的是，Golang 的包管理器有一种方法可以在这样的环境中工作。在本文中，我们将研究如何从企业网络代理后面克隆一个基于 Golang 的程序。

## 我的情况

这个过程是在惠普笔记本电脑上的 [Windows 10](https://www.microsoft.com/en-us/software-download/windows10) (build 1709)上执行的。这里面运行的是 [Ubuntu 18.04](http://releases.ubuntu.com/18.04.1/) 通过[Windows Linux 子系统(WSL)](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 特性。所有这些都在公司网络上，通过 VPN 连接到客户的网络。网络链看起来是这样的(甚至在'互联网'出现之前):本地机器- >企业网络- > VPN 隧道连接- >客户端企业网络。

## 过程

打开 Ubuntu WSL 我收到了典型的命令提示符，并输入了用户密码。登录完成后，我将目录(cd)更改为用户的主目录。

<figure>[![](img/3d589f6b34e629bccb69b3690e4ff034.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N24-EdcY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/golang_get_proxy_step_1.png) 

<figcaption>WSL 不是 100%的 Linux，但它工作，有点，有点。</figcaption>

</figure>

我首先尝试的是自述文件中推荐的命令。

```
go get github.com/mtojek/aws-closest-region
```

但这只是让我犯了一个错误，并不奇怪。

<figure>[![](img/44e1facbd2345cf82c8578b060743917.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z5CzJfE3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/golang_get_proxy_step_2-1.png) 

<figcaption>[为什么这种事总要发生在我身上？](https://www.youtube.com/watch?v=UHRGqJDA1zk)</figcaption>

</figure>

有意思。GiT 无法访问该 URL。所以在我的机器和 GitHub 服务器之间的某个地方，通信路由失败了。我想跟踪流量，看看请求从哪里开始失败。所以我安装并运行了去 github.com 的 traceroute

```
sudo apt-get install -y traceroute
...
traceroute github.com
...
29 * * *
30 * * *
```

traceroute 似乎会失败。接下来我尝试了一个 [curl](https://curl.haxx.se/) 请求:

<figure>[![](img/048da88cef134bc73214a78df185131b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YHBSGWtW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/golang_get_proxy_step_3-2.png) 

<figcaption>[多点多点多点；停止所有的点！](https://www.youtube.com/watch?v=5lo9kmcEw0w)</figcaption>

</figure>

啊！“302 不允许”。看起来 cURL 被重定向到的位置是组织代理网关的**ProxyWarning.html**页面。

这意味着我们需要为 GiT 设置代理设置。我知道几周前我已经为 Ubuntu 的 APT 程序设置了代理值，所以我在 APT 的配置文件中添加了这个值。最后，我运行了一个 [git](https://git-scm.com/) list 命令来确保值被保存。

```
$ tail /etc/apt/apt.conf
Acquire::http::Proxy "http://YOUR_PROXY_URL_HERE:8888";

#source https://stackoverflow.com/questions/128035/how-do-i-pull-from-a-git-repository-through-an-http-proxy/3406766#3406766
$ git config --global http.proxy YOUR_PROXY_URL_HERE:8888

$ git config --global --list
user.email=MY_AUTHOR_NAME@SOME_VALUE.com
user.name=MY_AUTHOR_NAME
http.proxy=YOUR_PROXY_URL_HERE:8888

```

现在设置了 GiT 代理值，我尝试了以下命令...

```
go get github.com/mtojek/aws-closest-region

```

似乎过了几个小时(< 3 minutes) I received a different return message than before: nothing. I was back at the command prompt with no output. Could this mean [返回代码 0](https://www.tldp.org/LDP/abs/html/exit-status.html) ？对~/go/src 目录执行一个 [ls](https://en.wikipedia.org/wiki/Ls) 命令。万岁！这个项目确实被下载了。

<figure>[![](img/1edebd10795b63f24242308941b60724.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QZkzH7-w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/golang_get_proxy_step_4-1.png) 

<figcaption>[超级绿！](https://www.youtube.com/watch?v=rKHh3EIFcZw)</figcaption>

</figure>

## 结论

通过一些故障排除、知识分享和文档阅读，我能够利用 GiT 的配置能力继续使用“go get”来安装应用程序。似乎任何在 80 以外的端口上下载的程序都需要进行配置。我可以调查一下 http_proxt 的全局使用情况吗？

你有任何关于配置程序使用代理传递的知识吗？你认为公司代理人没用吗；还是当今互联网的必需品？在下面评论，让我知道。