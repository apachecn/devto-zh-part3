# 使用 OpenSSL 获取远程服务器的 TLS 证书到期日期

> 原文：<https://dev.to/benjaminblack/determine-server-cert-expiry-with-openssl-4hd8>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

确实应该有一个`s_client`选项来做这件事。取而代之的是，使用`s_client`来读取证书，并通过管道将其发送给`x509`命令。

```
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null | openssl x509 -noout -dates 
```

Enter fullscreen mode Exit fullscreen mode