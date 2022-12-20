# 如果 Gmail 说“我们的系统检测到此邮件不符合 IPv6 关于 PTR 记录的发送指南”

> 原文：<https://dev.to/wincentbalin/if-gmail-says-our-system-has-detected-that-this-message-does-not-meet-ipv6-sending-guidelines-regarding-ptr-records-344h>

本质上， [Google Mail](https://mail.google.com/) 的 SMTP 服务器告诉你，要么你的 SMTP 服务器的 IPv6 地址为的 *PTR 记录不存在，要么这个记录与你的服务器不匹配。*

# 解

1.  查看您的 SMTP 服务器的 DNS 配置。
2.  如果它没有带有该服务器的 *IPv6 地址*的 *PTR 记录*，则添加它。
3.  如果您的配置不允许添加 PTR 记录，请在您的 SMTP 配置中禁用通过 *IPv6* 发送。对于后缀，确保参数`inet_protocols`(例如在文件`/etc/postfix/main.cf`中)看起来像`inet_protocols = ipv4`，即*没有* `ipv6`。

# 来源

*   [https://support . plesk . com/HC/en-us/articles/213936285-Unable-to-send-a-email-to-Gmail-Our-system-has-detected-the-this-message-not-meet-IPv6-sending-guidelines-about-PTR-records](https://support.plesk.com/hc/en-us/articles/213936285-Unable-to-send-an-email-to-Gmail-Our-system-has-detected-that-this-message-does-not-meet-IPv6-sending-guidelines-regarding-PTR-records)
*   [https://support . plesk . com/HC/en-us/articles/213388269-Unable-to-send-mail-using-STARTTLS-authentic ation-4-7-0-TLS-not-available-due-to-local-problem](https://support.plesk.com/hc/en-us/articles/213388269-Unable-to-send-mail-using-STARTTLS-authentication-4-7-0-TLS-not-available-due-to-local-problem)
*   [https://blog.kruyt.org/postfix-and-tls-encryption/](https://blog.kruyt.org/postfix-and-tls-encryption/)