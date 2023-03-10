# 通过 ILO 管理惠普服务器的 Docker 容器

> 原文：<https://dev.to/sshnaidm/docker-container-for-hp-servers-management-with-ilo-48c8>

[![HP ILO](img/f3b53cdeb89d495772d514ef80b58cb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQFpMMlJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://dev-random.net/wp-content/uploads/2015/06/ilo.png)

嗯，你可能想知道——为什么我要使用 docker 容器来达到这样的目的？照常进入 ILO 的 web 界面并管理服务器会出现什么问题？当我有几台旧服务器需要重新配置时，我也有同样的想法。服务器位于不同的大洲，我唯一的界面是国际劳工组织的网络界面。当我不得不通过虚拟控制台输入一些手动命令时，我发现这几乎是不可能的。

对于各种类型的服务器虚拟控制台(惠普和戴尔)，通常使用 Java web 小程序。但是 Firefox 和 Chrome 不再支持它们，而且最新的 IcedTea 也不能与旧系统兼容。所以我有几个选择:

1.  在我的系统上并行安装旧版本的浏览器和 Java，试图找到所需的组合。这个选项被过滤掉了，因为我不想仅仅因为几个控制台命令就污染我的系统。
2.  用旧系统创建一个虚拟机，安装 Java 6，像以前一样使用虚拟控制台。
3.  与第 2 点相同，但使用容器，而不是虚拟机。由于我的一些同事也遇到了同样的问题，我宁愿给他们一个 bash 命令来运行虚拟控制台，而不是共享虚拟机磁盘、密码等等。(说实话，第三点我是在第二点之后才提出来的)。第三点是我们今天要实现的。

我的灵感主要来自这两个项目:

*   [坞站-base image GUI](https://github.com/jlesage/docker-baseimage-gui)
*   [码头-firefox-java](https://github.com/ktelep/docker-firefox-java)

实际上，第一个项目 docker-baseimage-gui 已经包含了在一个容器内的浏览器中启动桌面应用程序所需的所有配置和工具。通常你定义特定的环境变量，你的应用程序将通过浏览器(websocket)或 VNC 访问。在我们的例子中，我们从 Firefox 和 VNC 开始，websocket 并没有很好地工作。

首先，让我们安装所需的包:Java 6 和 IcedTea:

```
RUN echo “deb http://archive.ubuntu.com/ubuntu precise main universe” > /etc/apt/sources.list && \
    apt-get update && \
    apt-get -y upgrade && \
    apt-get -y install firefox \
    nano curl \
    icedtea-6-plugin \
    icedtea-netx \
    openjdk-6-jre \
    openjdk-6-jre-headless \
    tzdata-java 
```

现在让我们在 Firefox 中打开 ILO 界面的网页，并在那里输入凭证。启动火狐:

```
RUN bash -c ‘echo “exec openbox-session &” >> ~/.xinitrc’ && \
    bash -c ‘echo “firefox \${HILO_HOST}”>> ~/.xinitrc’ && \
    bash -c ‘chmod 755 ~/.xinitrc’ 
```

变量`HILO_HOST`是我们 ILO 接口的 URL，例如`https://myhp.example.com`。

对于自动化，让我们添加身份验证。ILO 登录是通过简单的 POST 请求执行的，作为响应，您将获得 session_key 值，然后在 get 请求中传递该值。如果定义了环境变量`HILO_USER`和`HILO_PASS`:
，让我们用 curl 发现 session_key

```
export HOME=/config
export HILO_HOST=${HILO_HOST%%/}
SESSION_KEY=””
data=”{\”method\”:\”login\”,\”user_login\”:\”${HILO_USER}\”,\”password\”:\”${HILO_PASS}\”}”
if [[ -n “${HILO_USER}” && -n “${HILO_PASS}” ]]; then SESSION_KEY=$(curl -k -X POST “${HILO_HOST}/json/login_session” -d “$data” 2>/dev/null | grep -Eo ‘“session_key”:”[^”]+’ | sed ‘s/”session_key”:”//’)
fi echo “SESSION_KEY=$SESSION_KEY”
echo $SESSION_KEY > /session_key 
```

在容器中编写了 session_key 之后，我们可以启动 VNC 服务器:

```
exec x11vnc -forever -create 
```

现在只需将 VNC 客户端连接到本地主机的 5900 端口(或您在选择中定义的端口),然后进入惠普服务器的虚拟控制台。代码位于 git 库 docker-ilo-client 中。

连接到 ILO 虚拟控制台的整行命令:

```
docker run -d --rm --name ilo-client \
    -p 5900:5900 \
    -e HILO_HOST=https://ADDRESS_OF_YOUR_HOST \
    -e HILO_USER=SOME_USERNAME \
    -e HILO_PASS=SOME_PASSWORD \
    sshnaidm/docker-ilo-client 
```

其中，`ADDRESS_OF_YOUR_HOST`是 ILO 的主机名，`SOME_USERNAME`是登录名，`SOME_PASSWORD`是 ILO 的密码。

接下来就去找任何一个 VNC 的客户来解决`vnc://localhost:5900`。
欢迎提出请求和评论。

连接到戴尔 IDRAC 服务器的类似项目在这里: [docker-idrac6](https://github.com/DomiStyle/docker-idrac6) 。