# 在 Go 中验证客户端证书 San

> 原文：<https://dev.to/living_syn/validating-client-certificate-sans-in-go-i5p>

Go 拥有所有编程语言中最好的 TLS 库之一，因为它是我执行网络任务的首选语言。所以我有点惊讶地得知，默认情况下，当你在一个 [tls 上设置`tls.RequireAndVerifyClientCert`时。Config](https://golang.org/pkg/crypto/tls/#Config) 对象，它不验证客户端证书上的 SAN/CN 字段。它唯一要验证的是它是由配置的根 CA 签名的。

设置 go up 来执行验证并不像我最初认为的那样直观，如果您有和我一样的需求，我希望我可以帮助您。

## 起点

假设您有这样一个通用 tls 配置的起点:

```
serverConf := &tls.Config{
    Certificates: []tls.Certificate{cer},
    MinVersion:   tls.VersionTLS12,
    ClientAuth:   tls.RequireAndVerifyClientCert,
    ClientCAs:    rootCAs,
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们定义了一个服务器证书、一个最低 TLS 版本、要使用的根 CA，并且我们需要验证客户端证书。现在，客户端证书将被验证为由 rootCA 的 CertPool 中的 CA 签名，而不是其他。

## 自定义客户端证书验证

`tls.Config`对象有一个看起来很有希望的回调函数`VerifyPeerCertificate`，它接受一个带有这个签名的方法:

```
func(rawCerts [][]byte, verifiedChains [][]*x509.Certificate) error 
```

Enter fullscreen mode Exit fullscreen mode

问题是它没有传递任何客户端连接信息给我们来验证连接的主机！幸运的是，还有一个回调叫做`GetConfigForClient`。`GetConfigForClient`是对`tls.Config`对象的另一个回调，它给了我们 [tls。ClientHelloInfo](https://golang.org/pkg/crypto/tls/#ClientHelloInfo) 作为参数。并返回一个每客户端的`tls.Config`(或`nil`表示无变化)对象。

答案是使用`GetConfigForClient`调用一个函数，该函数返回一个与`VerifyPeerCertificate`签名匹配的闭包，但使`ClientHelloInfo`对它可用。

我们的服务器 tls。配置现在看起来像:

```
serverConf := &tls.Config{
    Certificates: []tls.Certificate{cer},
    GetConfigForClient: func(hi *tls.ClientHelloInfo) (*tls.Config, error) {
        serverConf := &tls.Config{
            Certificates: []tls.Certificate{cer},
            MinVersion:            tls.VersionTLS12,
            ClientAuth:            tls.RequireAndVerifyClientCert,
            ClientCAs:             rootCAs,
            VerifyPeerCertificate: getClientValidator(hi),
        }
        return serverConf, nil
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

我们剔除的`getClientValidator`函数看起来像:

```
func getClientValidator(helloInfo *tls.ClientHelloInfo) func([][]byte, [][]*x509.Certificate) error {
    return func(rawCerts [][]byte, verifiedChains [][]*x509.Certificate) error {
        return nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们开始时的功能基本上没有变化。客户端仍然连接，并且它们的证书得到验证，但是它们的 SAN 没有得到验证。

## 验证 San

为了在客户端证书上验证 SAN，我们需要修改`getClientValidator`方法。为了避免编写我们自己的验证方法，我们可以利用在 config 对象上指定`tls.RequireAndVerifyClientCert`时默认使用的相同验证器。我们需要做的只是向它的配置对象添加一些额外的选项。

```
func getClientValidator(helloInfo *tls.ClientHelloInfo) func([][]byte, [][]*x509.Certificate) error {
    return func(rawCerts [][]byte, verifiedChains [][]*x509.Certificate) error {
        //copied from the default options in src/crypto/tls/handshake_server.go, 680 (go 1.11)
        //but added DNSName
        opts := x509.VerifyOptions{
            Roots:         rootCAs,
            CurrentTime:   time.Now(),
            Intermediates: x509.NewCertPool(),
            KeyUsages:     []x509.ExtKeyUsage{x509.ExtKeyUsageClientAuth},
            DNSName:       strings.Split(helloInfo.Conn.RemoteAddr().String(), ":")[0],
        }
        _, err := verifiedChains[0][0].Verify(opts)
        return err
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，根据我们在起点指定的根 CA、客户端的证书密钥用法和客户端连接的 DNSName 来验证客户端的证书。`strings.Split`使我们不必在 DNSName 字段中包含`RemoteAddr`端口号，如果它是一个实际的 DNS 名称，也不会搞砸任何事情。

这就是了，希望你觉得这有用！你可以在一台小型服务器上找到所有的代码