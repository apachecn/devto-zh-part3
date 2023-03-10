# OpenSSH:基于主机的配置等。

> 原文：<https://dev.to/nabbisen/openssh-configurations-by-hosts-h1g>

# 总结

[OpenSSH](https://www.openssh.com/) 提供了基于主机等定义配置的方法。同关键字， [`Host`](https://man.openbsd.org/ssh_config#Host) 和 [`Match`](https://man.openbsd.org/ssh_config#Match) 。

### 环境

*   SSH 客户端:OpenSSH 7.9

<center>✿ ✿ ✿</center>

# 依据

打开 [`ssh_config`](https://man.openbsd.org/ssh_config) 文件。

```
$  nvim ~/.ssh/config 
```

Enter fullscreen mode Exit fullscreen mode

然后写定义。

##### 例子

这里是一个多重条件的例子。
每个条件的定义直到下一个`Host`或`Match`关键字都有效。

```
# [~/.ssh/config] 
Host %host-name%
    %Parameter-Name%    %parameter-value%

Host %multiple-hosts-name-1% %multiple-hosts-name-2%
    %Parameter-Name%    %parameter-value%

Match {host,originalhost,user,localuser,exec,canonical,final} "%criteria%"
    %Parameter-Name%    %parameter-value%

... 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个单个主机中多个参数的示例。
当然，在任何主机中定义比其他主机更多或更少的参数都没问题。(注意默认值。)

```
# [~/.ssh/config] 
Host %nickname%
    Hostname     %real-hostname%
    Port         %specified-port%
    User         %specified-user%
    IdentityFile ~/.ssh/specified-user_id_rsa
    ServerAliveInterval 60 
```

Enter fullscreen mode Exit fullscreen mode

**注意:缩进只是为了便于查看，实际上并没有必要。*

<center>✿ ✿ ✿</center>

# 灵活定义

### #1: [图案](https://man.openbsd.org/ssh_config#PATTERNS)

| *图案*符号 | 使用 |
| --- | --- |
| `*` | 匹配零个或多个字符。 |
| `?` | 恰好匹配一个字符。 |
| `!` | 否定目标。 |

##### 例子

```
# [ `*` keyword ]
# any hosts Host *
# any .com domains Host *.com

# [ `?` keyword ]
# 192.168.0.[0-9] Host 192.168.0.?

# [ `!` keyword ]
# any except specified domain Host * !cool-website.com
# any except specified domain and subdomains Host * !cool-website.com !*.cool-website.com
# any 192.168.* except 192.168.0.* Host 192.168.* !192.168.0.* 
```

Enter fullscreen mode Exit fullscreen mode

**注意:`!`关键字需要匹配的目标和否定的目标:*

*   NG: `Host !some.domain.com`
*   好:`Host * !some.domain.com`

### #2: [`Match`](https://man.openbsd.org/ssh_config#Match) 代替 [`Host`](https://man.openbsd.org/ssh_config#Host)

| 关键字 | 使用 |
| --- | --- |
| `host` | 要登录的真实主机名。 |
| `originalhost` | 在命令行中指定的主机名。 |
| `user` | 远程主机上的目标用户名。 |
| `localuser` | 运行`ssh`的本地用户的名称。 |

##### 例子

```
# a single condition with a single option Match host "some-domain.com"
# which equals to:
# Host some-domain.com
    IdentityFile ~/.ssh/default_id_rsa

# a single condition with multiple options Match host "specified-domain.com,some.specified-domain.com"
# which equals to:
# Host specified-domain.com some.specified-domain.com
    IdentityFile ~/.ssh/specified-domain_id_rsa

# multiple conditions Match host "specified-domain.com" user "specified-user"
    IdentityFile ~/.ssh/specified-user_id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

**注意:不要在多个选项之间加空格:*

*   NG: `Match host "specified-domain.com, some.specified-domain.com"`
*   好:`Match host "specified-domain.com,some.specified-domain.com"`

##### (可选)更多控件同`Match`

| 关键字 | 使用 |
| --- | --- |
| `exec` | 在用户的外壳下执行指定的命令。如果命令返回零退出状态，则认为条件为真。自变量可以定义为[记号](https://man.openbsd.org/ssh_config#TOKENS)。 |
| `canonical` | 仅当在[主机名规范化](https://man.openbsd.org/ssh_config#CanonicalizeHostname)后重新解析配置文件时匹配。 |
| `final` | 请求重新解析配置(不管 [CanonicalizeHostname](https://man.openbsd.org/ssh_config#CanonicalizeHostname) 是否启用)，并且仅在最后一次通过时匹配。如果启用了 CanonicalizeHostname，则在同一个过程中，canonical 和 final 匹配。 |

<center>✿ ✿ ✿</center>

# 用途

### #1:指定主机名的配置

```
Host some.cool-website.com
    User     %user-specified%

Host *
    User     %user-default% 
```

Enter fullscreen mode Exit fullscreen mode

### #2:子域配置

```
# specified domain Host cool-website.com
    # write configuration... 
# specified subdomain Host www.cool-website.com
    # write configuration... 
# all subdomains Host *.cool-website.com
    # write configuration... 
# specified domain and subdomains Host cool-website.com *.cool-website.com
# or:
# Match host "cool-website.com,*.cool-website.com"
    # write configuration... 
```

Enter fullscreen mode Exit fullscreen mode

### #3:主机名别名

```
Host %nickname%
    HostName    real.very-long-hostname.com 
```

Enter fullscreen mode Exit fullscreen mode

### #4:端口切换

```
Host %host-with-unique-port%
    Port    %real-port% 
```

Enter fullscreen mode Exit fullscreen mode

### #5:使用身份文件

```
Host www.cool-website.com
    IdentityFile    ~/.ssh/specified_id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

为🕊服务愉快