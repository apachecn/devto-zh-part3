# 验证 Rails 中的 Twitch Webhooks

> 原文：<https://dev.to/coreyja/verifying-twitch-webhooks-in-rails-2i3a>

我最近在为 dev.to 添加一些 Twitch 嵌入支持！其中一部分是添加 webhook 支持。

Twitch 建议做的事情之一，也是我想做的事情，就是验证 webhooks 来自 Twitch，而不是恶意的第三方。这是通过在注册 webhook 时传递一个秘密，并使用该秘密和主体的内容来创建一个散列，并验证有效载荷来自 Twitch。
他们的 Webhook 指南的底部提到了验证有效载荷，但并没有给出很多如何实际做到这一点的上下文，[https://dev.twitch.tv/docs/api/webhooks-guide/](https://dev.twitch.tv/docs/api/webhooks-guide/)

起初，我认为我需要做一个“普通的”SHA256，将秘密数据和有效载荷数据连接在一起。但这是不正确的，我需要做一个 SHA256 HMAC，用这个秘密作为 HMAC 的秘密，有效载荷作为主体的整体。我在网上阅读了一些问题，发现了这一点，但它们都是 JS 格式的，所以我必须找出正确的 Ruby 方法来使用，幸运的是，这并不太难。

下面是使用 OpenSSL 库在 Ruby 中实现这一点的长期方法

```
digest = OpenSSL::Digest::SHA256.new
secret = "SECRET_SENT_TO_TWITCH_AT_WEBHOOK_REGISTRATION"
hmac = OpenSSL::HMAC.new(secret, digest)
hmac << "BODY_OF_PAYLOAD_HERE"
hmac.hexdigest 
```

还有一个捷径的方法是这样的！

```
secret = "SECRET_SENT_TO_TWITCH_AT_WEBHOOK_REGISTRATION"
OpenSSL::HMAC.hexdigest("SHA256", secret, "BODY_OF_PAYLOAD_HERE") 
```

在我参与的 Rails 项目中，这实际上是这样的，用来确定请求是否有效。这假设 Twitch 秘密存储在一个 ENV 变量中。

```
def webhook_verified?
  twitch_sha = request.headers["x-hub-signature"]
  digest = OpenSSL::HMAC.hexdigest("SHA256", ENV["TWITCH_WEBHOOK_SECRET"], request.raw_post)

  twitch_sha == "sha256=#{digest}"
end 
```

我在我的控制器动作中使用了这个方法来决定采取什么动作来响应 webhook！如果 webhook 没有被验证，我决定简单地回复一个 204 No Content，并且不做任何局部修改。这样，如果一些恶意演员试图模仿 Twitch，我不会立即提醒他们的请求失败。