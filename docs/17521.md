# 在 Windows 上生成 SSL 证书的有用文章

> 原文：<https://dev.to/jsn1nj4/useful-article-for-generating-ssl-certs-on-windows-26g8>

这只是我想分享的一篇[文章](https://reactpaths.com/how-to-get-https-working-in-localhost-development-environment-f17de34af046)，我发现它很有用。我想这对那些使用 HTTPS 在 Windows 上查看本地开发的人也是有用的。

在使用 Git Bash 完成这些步骤后，我想提到的唯一区别是，在为本地域颁发证书的步骤 **2 步骤下的步骤 2 中，应该做一些稍微不同的事情:**

这一步提供了下面的代码片段:

```
openssl x509 \
 -req \
 -in demo.local.csr \
 -CA rootSSL.pem -CAkey rootSSL.key -CAcreateserial \
 -out demo.local.crt \
 -days 500 \
 -sha256 \
 -extfile <(echo " \ authorityKeyIdentifier=keyid,issuer\n  \ basicConstraints=CA:FALSE\n  \ keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment\n  \ subjectAltName=DNS:demo.local \ ") 
```

Enter fullscreen mode Exit fullscreen mode

使用进程替换作为`-extfile`选项的参数运行此命令会导致以下错误:

```
error loading the config file  /proc/<pid>/fd/63' 
```

Enter fullscreen mode Exit fullscreen mode

根据我的理解，这是因为在以子进程的`pid`命名的`/proc`中创建了一个临时目录，但是在结果可以作为参数传递给上面的命令之前，子进程关闭并且临时目录被删除。

为了解决这个问题，我将临时配置字符串放在一个类似于`demo.local.cnf`的文件中，然后使用该文件名作为参数来代替上面显示的进程替换。

### P.S

我保证还有其他人对此更有经验；这是我第一次成功地生成本地使用的证书，我还没有机会测试它。

请随时告诉我术语和理解需要纠正/改进的地方。这肯定会帮助我更好地理解，因为我学会了与 HTTPS 当地开发工作。