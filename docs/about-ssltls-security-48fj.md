# 关于 SSL/TLS 安全性

> 原文：<https://dev.to/terceranexus6/about-ssltls-security-48fj>

当我们通过网络传输敏感数据时，我们希望感到安全。为此，我们使用 HTTPS 流量，这是通过 SSL/TLS 保护的 HTTP 协议。它检查机密性和认证，最后一项是通过[数字证书](https://dev.to/terceranexus6/security-sprint-week-4-ca-pairs-creation-with-openssl-eia)完成的。让我们来探讨如何测试 HTTP 协议传输的漏洞。我们要用什么？

**Openssl** 的缩写形式

正如[官方网站](https://www.openssl.org/)所解释的，openssl 是一个软件库，用于保护计算机网络上的通信免受窃听或需要识别另一端的应用程序。它通常包含在 linux 系统中，但是——在基于 debian 的系统中——可以使用`sudo apt install openssl`安装。

**nmap**

nmap 是一种安全扫描器，用于发现计算机网络上的主机和服务。在 debian 中，你可以使用`sudo apt install nmap`来安装它。这是一个广泛使用的安全工具，所以如果你没有它，并且你正在使用 cybersec，它是一个必须安装的。

好吧，我们开始吧。测试 SSL/TLS 安全性时，我们要寻找什么？首先，我们将检查是否有弱密码或协议。正如在 [OWASP wiki](https://www.owasp.org/index.php/Testing_for_Weak_SSL/TLS_Ciphers,_Insufficient_Transport_Layer_Protection_(OTG-CRYPST-001)%20in%20which%20this%20article%20is%20based) 中关于这个主题的解释，匿名 Diffie-Hellmann 是认证漏洞或 SSLv2 协议的一个很好的例子。使用 nmap，我们可以扫描主机，例如使用`nmap [target]`；a ping only 扫描 usign`nmap -sn [target]`；使用`nmap -PS [target]`的 TCP SYN ping。我们将使用标准服务扫描`-sV`。

```
nmap -sV www.example.com 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用其他自定义选项，如`-PN`、`--top-ports`等。无论如何，使用最后一个命令，我们能够识别 SSL 服务。现在我们可以检查每个端口的证书信息、弱密码和 SSLv2。我们在示例中检查 443，465，993，995，但是这应该在每种情况下进行定制。

```
nmap --script ssl-cert,ssl-enum-ciphers -p 443,465,993,995 www.example.com 
```

Enter fullscreen mode Exit fullscreen mode

最后，openssl 用于手动测试 SSL/TLS，尝试通过客户端使用 openssl 连接到服务器来启动重新协商。

[![](img/deaf16dbfcbfaee25d6281fe3fc9ab60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n25p7Vxp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F13oH6nnW52LUFG%252Fgiphy.gif%26f%3D1)T3】

```
openssl s_client -connect www.example.com:443 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个简单的[要点](https://gist.github.com/terceranexus6/420cf35fdcbb87633c7aa8a5731ef26f)来使用默认配置和定制的主机自动完成所有这些。它将输出保存在三个不同的文件中以供分析。代码如下:

```
#!/bin/bash

BLUE='\033[0;36m'
RED='\033[0;31m'
NC='\033[0m' # No Color

#based in OWASP wiki

if [[ -z "$1" || -z "$2" ]]; then
   echo "./tls.sh host directory"

else

    mkdir $2
    cd $2

    echo -e "${BLUE}Checking for Client-initiated Renegotiation and Secure Renegotiation via openssl...${NC}"
    openssl s_client -connect $1:443  > negotiations

    if grep -q 'Secure Renegotiation IS NOT supported' "negotiations"; then
      echo -e "${RED}Secure Renegotiation IS NOT supported.${NC}"
    else
      echo -e "${BLUE}Certificate validity ensured.${NC}" 
    fi

    echo -e "${BLUE}Checking for Certificate information, Weak Ciphers and SSLv2 via nmap...${NC}"
    nmap --script ssl-cert,ssl-enum-ciphers -p 443,465,993,995 $1 > ciphernmap

    if grep -q SSLv2 "ciphernmap"; then
      echo -e "${RED}Weak protocol found (SSLv2).${NC}"
    else
      echo -e "${BLUE}No weak protocol found.${NC}" 
    fi

    echo -e "${BLUE}SSL service recognition via nmap...${NC}"
    nmap -sV --reason -PN -n --top-ports 100 $1 > nmapsslservice

    echo -e "${BLUE}Done.${NC}"

    echo -e "${RED}Don't forget to manually check the files created in case of doubt. Check OWASP wiki for more information.${NC}"

fi 
```

Enter fullscreen mode Exit fullscreen mode

更多信息请查看 OWASP 的惊人的 [wiki。](https://www.owasp.org/index.php/Testing_for_Weak_SSL/TLS_Ciphers,_Insufficient_Transport_Layer_Protection_(OTG-CRYPST-001))