# 使用 DNS 代理服务器从主机解析 docker 主机名

> 原文：<https://dev.to/karlredman/resolve-docker-hostnames-from-host-with-dns-proxy-server-1d08>

*   最后更新:2019 年 3 月 3 日

本文是我之前 HowTo 的延续: [Dnsmasq + NetworkManager +私有网络设置](https://dev.to/karlredman/dnsmasq--networkmanager--private-network-setup-258l)。只需对配置做一些修改，我们就可以添加通过各自的主机名与 docker 容器交互的功能。尽管 [dns 代理服务器项目](https://github.com/mageddo/dns-proxy-server)为我们提供了这一新功能，但我建议`dns-proxy-server`仅用于开发目的。

虽然在开发过程中能够通过主机名引用单个容器是很棒的，但是这个概念并没有很好地扩展。此外， [dns 代理服务器](https://github.com/mageddo/dns-proxy-server) (DPS)存在一些性能问题，在将其用作 docker 服务之前，应该考虑到这些问题。

## 添加到[上的附加功能](https://dev.to/karlredman/dnsmasq--networkmanager--private-network-setup-258l):

*   从 docker 主机引用类似 FQDN 的容器主机名
*   从其他容器中引用类似 FQDN 的容器主机名
*   通过没有端口说明的浏览器引用类似 FQDN 的容器主机名
*   无论您的主机系统在线还是离线，行为都是一致的
*   在混合环境中使用`docker run`和`docker-compose`容器
*   网络管理器功能仍然可行和有效
*   一劳永逸的配置

## 被测系统:

*   MX Linux v18.1
*   Ubuntu v18.10
*   Debian v9.8

## 免责声明:

*   我**强烈建议**首先用一个虚拟机来测试这个过程。如果你搞乱了你的系统，数据等，我不负责..此信息按原样提供，使用风险由您自行承担。
*   本文假设您已经按照文章[DNS masq+Network manager+Private Network Setup](https://dev.to/karlredman/dnsmasq--networkmanager--private-network-setup-258l)中所述配置了您的目标系统。
*   注意:对`dnsmasq`使用`all-servers`设置可能会产生不良行为或者被认为是安全风险——这取决于您的标准。如有疑问，请参见 DNSMASQ 的[手册页了解更多信息。](http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html)

## 如何:

### 允许异步名称服务器查找

*   指令`all-servers`至`/etc/dnsmasq.conf`
*   还要对设置进行完整性检查

```
all-servers         # async call to all nameservers
bind-dynamic        # could use bind-interfaces instead
listen-address=127.0.0.1
listen-address=10.127.127.1
address=/private.home/10.127.127.1
domain=private.home,10.127.127.0/24
domain=docker.devnet,172.17.0.0/24 
```

*   重新启动 dnsmasq

```
sudo service dnsmasq restart 
```

### 将 DPS 配置添加到网络管理器

*   DPS 将始终绑定到地址`172.17.0.2`

```
nmcli con modify 'Wired connection 1' ipv4.dns 172.17.0.2
nmcli con modify 'Wired connection 1 offline' ipv4.dns 172.17.0.2
#
nmcli con up 'Wired connection 1' 
```

### 创建 DPS 配置文件

*   创建目录和文件

```
sudo mkdir -p /opt/dns-proxy-server/conf
sudo touch /opt/dns-proxy-server/conf/config.json 
```

*   选项参考:[运行 dns 代理服务器](https://mageddo.github.io/dns-proxy-server/docs/running.html)

    *   备用 DNS:8.8.8.8
    *   成为辅助 dns 服务器
    *   将日志级别设置为:警告
    *   将日志文件设置为`/opt/dns-proxy-server/dps.log`
    *   允许命令行 ping 容器名称
*   将 DPS 设置添加到配置文件

```
{  "remoteDnsServers":  [  ["8.8.8.8"]  ],  "envs":  [  {  "name":  ""  }  ],  "activeEnv":  "",  "lastId":  0,  "webServerPort":  0,  "dnsServerPort":  0,  "defaultDns":  false,  "logLevel":  "WARNING",  "logFile":  "/opt/dns-proxy-server/dps.log",  "registerContainerNames":  true  } 
```

### 获取并构建 docker 映像进行测试

```
git clone https://github.com/karlredman/lighttpd-docker.git
#
cd lighttpd-docker
#
sudo docker build -t lighttpd . 
```

### 启动 DPS

```
docker run --rm --hostname dns.mageddo --name dns-proxy-server -p 5380:5380 -v /opt/dns-proxy-server/conf:/app/conf -v /var/run/docker.sock:/var/run/docker.sock -v /etc/resolv.conf:/etc/resolv.conf defreitas/dns-proxy-server 
```

### 启动示例容器

```
# start FQDN hostname docker container 1
sudo docker run -d -p 8081:80 -p 4441:443 --rm -t --name docker-container-1-name -h docker-container-1.docker.devnet --net docker.devnet  lighttpd

# start container 2
sudo docker run -d -p 8082:80 -p 4442:443 --rm -t --name docker-container-2-name -h docker-container-2.docker.devnet --net docker.devnet lighttpd

# start container 3
sudo docker run -d -p 8083:80 -p 4443:443 --rm -t --name docker-container-3-name -h docker-container-3.docker.devnet --net docker.devnet lighttpd 
```

### 测试 pings

*   注意:由于循环 dns 解析，脱机模式下的 pings 会很慢

```
ping -c 4 localhost
ping -c 4 [your host name]
ping -c 4 [some system on your network]

ping -c 4 dns.mageddo
ping -c 4 docker-container-1.docker.devnet
ping -c 4 docker-container-3.docker.devnet
ping -c 4 docker-container-2-name       # fails offline mode

sudo docker exec -ti docker-container-1-name sh -c "ping -c 4 host.docker"
sudo docker exec -ti docker-container-1-name sh -c "ping -c 4 a-wildcard.private.home"
sudo docker exec -ti docker-container-2-name sh -c "ping -c 4 docker-container-3-name"
sudo docker exec -ti docker-container-3-name sh -c "ping -c 4 docker-container-1.docker.devnet" 
```

### 查看 DPS 网页(好像对我不起作用)

```
http://localhost:5380
http://dns.mageddo:5380 
```

### 通过浏览器测试地址

*   作品

    *   [http://localhost:8081/hostname . html](http://localhost:8081/hostname.html)
    *   [http://docker-container-1.docker.devnet/hostname.html](http://docker-container-1.docker.devnet/hostname.html)
    *   [http://localhost:8082/hostname . html](http://localhost:8082/hostname.html)
    *   [http://docker-container-2.docker.devnet/hostname.html](http://docker-container-2.docker.devnet/hostname.html)
    *   [http://localhost:8083/hostname . html](http://localhost:8083/hostname.html)
    *   [http://docker-container-3.docker.devnet/hostname.html](http://docker-container-3.docker.devnet/hostname.html)
*   不工作

    *   [http://docker-container-1-name](http://docker-container-1-name)
    *   [http://docker-container-1-name:8081](http://docker-container-1-name:8081)

### 测试 docker-撰写实例

*   启动容器

```
cd lighttpd-docker
sudo docker-compose up -d 
```

*   测试对撰写容器的 ping

```
ping -c 4 docker-container-4.docker.devnet
sudo docker exec -ti docker-container-4-name sh -c "ping -c 4 a-wildcard.private.home"
sudo docker exec -ti docker-container-4-name sh -c "ping -c 4 docker-container-3-name"
sudo docker exec -ti docker-container-4-name sh -c "ping -c 4 docker-container-1.docker.devnet"
#
sudo docker exec -ti docker-container-1-name sh -c "ping -c 4 docker-container-4.docker.devnet" 
```

*   测试浏览器对容器的访问
    *   [http://localhost:8084/hostname . html](http://localhost:8084/hostname.html)
    *   [http://docker-container-4.docker.devnet/hostname.html](http://docker-container-4.docker.devnet/hostname.html)

## 就是这样！！