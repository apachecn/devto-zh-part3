# bash 中的网络重定向

> 原文：<https://dev.to/anmolsarma/network-redirections-in-bash-40i1>

几个月前，在阅读`recvmmsg()`的手册页时，我看到了这个片段:

```
$ while true; do echo $RANDOM > /dev/udp/127.0.0.1/1234;
     sleep 0.25; done 
```

正如宣传的那样，它每 250 毫秒向端口 1234 发送一个包含随机数的 UDP 数据报。我不记得曾经见过一个`/dev/udp`，所以对它的工作感到有点惊讶。碰巧的是，`ls`无法访问我刚刚写入的文件:

```
ls: cannot access '/dev/udp/127.0.0.1/1234': No such file or directory 
```

困惑和好奇，我`echoe` d *Foo Bar Baz* 到`/dev/udp/127.0.0.1/1337`，伸手拿`strace` :

```
...
2423 socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP) = 4
12423 connect(4, {sa_family=AF_INET, sin_port=htons(1337), sin_addr=inet_addr("127.0.0.1")}, 16) = 0
12423 fcntl(1, F_GETFD) = 0
12423 fcntl(1, F_DUPFD, 10) = 10
12423 fcntl(1, F_GETFD) = 0
12423 fcntl(10, F_SETFD, FD_CLOEXEC) = 0
12423 dup2(4, 1) = 1
12423 close(4) = 0
12423 fstat(1, {st_mode=S_IFSOCK|0777, st_size=0, ...}) = 0
12423 write(1, "Foo Bar Baz\n", 12) = 12
... 
```

表面上，一个普通的 UDP 套接字被创建并使用常规 sycall 接口写入。这驳斥了我最初的怀疑，即涉及内核支持的某种特殊文件。但是实际上是谁在创建套接字呢？

对 Bash 代码的一瞥回答了这个问题:

**是。c:**

```
/* A list of pattern/value pairs for filenames that the redirection
   code handles specially. */
static STRING_INT_ALIST _redir_special_filenames[] = {
#if !defined (HAVE_DEV_FD)
  { "/dev/fd/[0-9]*", RF_DEVFD },
#endif
#if !defined (HAVE_DEV_STDIN)
  { "/dev/stderr", RF_DEVSTDERR },
  { "/dev/stdin", RF_DEVSTDIN },
  { "/dev/stdout", RF_DEVSTDOUT },
#endif
#if defined (NETWORK_REDIRECTIONS)
  { "/dev/tcp/*/*", RF_DEVTCP },
  { "/dev/udp/*/*", RF_DEVUDP },
#endif
  { (char *)NULL, -1 }
}; 
```

因此，Bash <sup id="fnref:1">[1](#fn:1)</sup> 专门处理涉及`/dev/udp/`的重定向，它使用 BSD Sockets API 创建一个套接字:

**lib/sh/netopen.c:**

```
/*
 * Open a TCP or UDP connection to HOST on port SERV. Uses the
 * traditional BSD mechanisms. Returns the connected socket or -1 on error.
 */
static int
_netopen4(host, serv, typ)
     char *host, *serv;
     int typ;
{
  struct in_addr ina;
  struct sockaddr_in sin;
  unsigned short p;
  int s, e;

  if (_getaddr(host, &ina) == 0)
    {
      internal_error (_("%s: host unknown"), host);
      errno = EINVAL;
      return -1;
    }

  if (_getserv(serv, typ, &p) == 0)
    {
      internal_error(_("%s: invalid service"), serv);
      errno = EINVAL;
      return -1;
    }

  memset ((char *)&sin, 0, sizeof(sin));
  sin.sin_family = AF_INET;
  sin.sin_port = p;
  sin.sin_addr = ina;

  s = socket(AF_INET, (typ == 't') ? SOCK_STREAM : SOCK_DGRAM, 0);
  if (s < 0)
    {
      sys_error ("socket");
      return (-1);
    }

  if (connect (s, (struct sockaddr *)&sin, sizeof (sin)) < 0)
    {
      e = errno;
      sys_error("connect");
      close(s);
      errno = e;
      return (-1);
    }

  return(s);
} 
```

这意味着我们实际上可以使用 Bash:
发出 HTTP 请求

```
exec 3<> /dev/tcp/checkip.amazonaws.com/80
printf "GET / HTTP/1.1\r\nHost: checkip.amazonaws.com\r\nConnection: close\r\n\r\n" >&3
tail -n1 <&3 
```

不需要`curl`！/jk

除了 Bash，在 Ubuntu 18.04 打包的版本和配置中，只有`ksh`支持网络重定向——`ash`、`csh`、`dash`、`fish`、`zsh`不支持。

我不认为我真的会对网络重定向有任何用处，但这是一个有趣的小兔子洞。

**注意:**来自 Bash 的代码片段在 GPLv3 许可下，来自手册页的代码片段在[以不同的方式许可](http://man7.org/linux/man-pages/man2/recvmmsg.2.license.html)

* * *

1.  至少在 Linux 上，bash 处理的其他特殊模式如 [`/dev/fd`](http://www.informit.com/articles/article.aspx?p=99706&seqNum=15) 和`/dev/stdint`实际上是内核支持的特殊文件。 [Bash 手册](https://www.gnu.org/software/bash/manual/html_node/Redirections.html)提到它可能在不支持它们的平台上内部模拟它们。<sup>【返回】</sup>