# WPA 和开放系统认证

> 原文：<https://dev.to/ngschmidt/wpa-and-open-system-authentication-47co>

您是否知道，在您对无线网络进行鉴定之前，它使用的安全机制与开放 Wi-Fi 相同？

使用 TLS，众所周知(大多数)密码套件是如何实现 Diffie-Hellman 交换来提供合理有效的前向保密的。ECC Diffie-Hellman 已经在很大程度上取代了 RSA，但是实现它的根本原因仍然是一样的——在建立加密会话之前，保密性是不存在的。最终的解决方案是带外交换 pad，但这在技术上是不可行的。总会有牺牲密码交换的折衷来实现前向保密。

这是一个非常有帮助的视频，它形象地描述了迪菲-海尔曼的交换:

【https://www.youtube.com/watch?v=YEBfamv-_do】T2