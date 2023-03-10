# 使用 Solace 的 PubSub+代理进行 MQTT 客户端证书认证

> 原文：<https://dev.to/solacedevs/mqtt-client-certificate-authentication-with-solace-s-pubsub-broker-11fb>

[![](img/a54923098115de00e0d514fdc9d12fb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ur3N0Z4N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ad9f5o4fXEMAmJxCtC6TI9Q.png)

MQTT 是物联网世界中事实上的消息传递协议。当谈到认证和识别成千上万的设备时，您可能会看到提到客户端证书认证，但很少提到如何实际实现这一点。尽管管理证书、撤销列表以及作为证书颁发机构所带来的一切都很复杂，但是开始使用客户端证书身份验证并不复杂。

在本文中，我们将执行开始创建 Node.js MQTT 应用程序所需的所有步骤，该应用程序通过客户端证书进行身份验证。我们将做三件基本的事情:

1.  创建证书颁发机构(CA)和客户端证书
2.  将 CA 证书上传到 MQTT 代理(在本例中是 Solace PubSub+)
3.  配置 JS MQTT 客户机以使用客户机证书进行身份验证

在我们开始之前，您需要一些东西:

1.  OpenSSL
2.  节点. js
3.  NPM
4.  [Solace Cloud PubSub+开发者或启动服务](https://console.solace.cloud)

现在，让我们开始创建物联网解决方案！

首先，我们将创建一个证书颁发机构(CA)来使用 OpenSSL 签署客户端证书。

```
openssl req -nodes -new -x509 -newkey rsa:4096 -keyout root.key -out root.pem -subj "/C=/ST=/L=/O=/OU=/CN=root" 
```

接下来，我们将创建一个客户端证书，MQTT 客户端将使用该证书进行身份验证，并创建一个由 CA 签名的请求。

如何处理客户端证书取决于您的 MQTT 代理。本文将使用 Solace 的 PubSub+ Broker，除了认证客户端之外，它还可以使用通用名称(CN)或主题替代名称(SAN)来识别客户端(更多详细信息，请参见 [Solace 的文档](https://docs.solace.com/Configuring-and-Managing/Managing-Client-Authentication.htm#Username-Source))。默认情况下，CN 将映射到客户端用户名。在本文中，我们使用 CN“演示客户端”。

```
openssl req -nodes -new -newkey rsa:4096 -keyout client.key -out client.csr -subj "/C=/ST=/L=/O=/OU=/CN=demo-client" 
```

最后，我们将使用我们的 CA 签署该证书。

```
openssl x509 -req -in client.csr -CA root.pem -CAkey root.key -CAcreateserial -out client.pem -days 1825 -sha256 
```

我们应该有继续下去所需的所有文件。特别是，我们需要以下文件:

```
root.pem
client.pem
client.key 
```

我们现在需要设置 MQTT 代理。在 PubSub+ Cloud 中，您需要开发人员或启动人员服务来利用客户端证书认证。

客户机证书将作为 TLS 握手的一部分发送给 MQTT 代理。客户端证书的有效性将根据代理的实现来确定。对于 Solace PubSub+ Broker，这是通过上传存储在 root.pem 中的 CA 证书来完成的。

在服务的详细信息页面中，单击管理，然后单击身份验证。

[![](img/6e9918a4394b48825f5f58ff02681ea8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d-qw9zZ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ako0-JybCfuuxNwWbqmOBgA.png)

在服务验证选项卡下，启用客户端证书验证，然后单击保存。

<figure>[![](img/f3e235aeac57ed4d613236dd3b6582c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3yhahDpf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsRjN0pqBxEJHz69Kpk0cpg.png) 

<figcaption>你也可以像截图一样禁用基本认证。</figcaption>

</figure>

回到“管理”选项卡，单击“证书颁发机构”。

[![](img/7ef31beb8393fc45f6109692efae72d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MbxNfBs0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfyV37A4UWgBVYOZ-Wt_9Zw.png)

然后，点击+添加新按钮。选择一个名称，并粘贴根目录的内容。pem 然后单击提交。

[![](img/fa8a696bb4593881ebc56bf79cc17f86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kQlQpFfY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADAhW-_WwqX9tc7pZ8TvaOQ.png)

接下来，必须使用客户端的 CN 创建客户端用户名。在服务详细信息页面的管理下，单击访问控制。

[![](img/a04f4769d3b185c7b702a6c7f4520a41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JvePTx1X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXDaD4c8bHsySvm39dJG5ZQ.png)

你将被直接带到 Solace PubSub+ Broker 的管理界面。单击客户端用户名选项卡，然后单击+客户端用户名按钮。

[![](img/3607673da35709642ee61e9f081a41ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P--qUUWZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay46D1a2CJpCWs3Af4PbSUQ.png)

输入“demo-client”作为客户端用户名。点击启用，然后应用。

[![](img/7213d1d347694ca9a91e93287bcad78e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--825A3s-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A22I2SEWAWByGnP9k25CZjg.png)

您的 Solace PubSub+ Broker 现已配置为使用您的 CA 签署的证书对客户端进行身份验证。

现在，我们终于可以设置我们的客户端并发送和接收消息了。

我们将使用 Node.js 和 [MQTT.js](https://www.npmjs.com/package/mqtt) 来订阅一个主题，向该主题发送消息，并打印出我们通过订阅收到的消息。

在证书文件所在的目录下创建一个名为 demo.js 的文件，内容如下:

```
**const** mqtt = require('mqtt');
**const** fs = require('fs');

**const** client = mqtt.connect('<HOST>', {
  key: fs.readFileSync('client.key'),
  cert: fs.readFileSync('client.pem'),
});

client.on('connect', () => {
  client.subscribe('demotopic');
  client.publish('demotopic', 'Hello mqtt');
});

client.on('message', (topic, message) => {
  console.log(`Received message: ${message.toString()}`);
  client.end();
}); 
```

替换为代理的安全 MQTT 主机(请记住，客户机证书是作为 TLS 握手的一部分发送的，因此不安全的连接将不能用于客户机证书身份验证)。在 Solace PubSub+ Cloud 中，在您的服务的详细页面中，在 Connect 选项卡下，您可以使用 MQTT 部分下的 WebSocket 安全主机。

[![](img/26039753db9d0c1d7b9b5d70e79b98b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZaohvJID--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATpo2BDf6iJWc9aTbFezwCQ.png)

然后在这个目录中运行 npm install mqtt。最后，用 node demo.js 发布并接收一条消息，应该会看到如下输出:

```
Received message: Hello mqtt 
```

我们现在已经有了物联网解决方案的基础。接下来还有更多的工作要做，包括如何提供、管理和撤销客户端证书。虽然这个例子使用了 OpenSSL，但您可能希望研究更复杂的解决方案，如 [CloudFlare 的 CFSSL](https://github.com/cloudflare/cfssl) 、 [Vault](https://www.hashicorp.com/blog/certificate-management-with-vault) ，或者由您最喜欢的云提供商管理的解决方案。

另外，Solace PubSub+对其他协议使用相同的客户端证书认证，因此您可以开始使用相同的客户端证书对 SMF、AMQP 和其他国家进行认证。更多详情参见[官方文档](https://docs.solace.com/Features/Client-Authentication.htm#Client-Certificate)。

我希望这能帮助你一次一个设备地连接世界。

* * *