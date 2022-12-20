# 如何用 http 和 https 为 gitlab 页面设置 google 域

> 原文：<https://dev.to/benteoneb/how-to-setup-google-domain-for-gitlab-pages-with-http-and-https-36lk>

如果像我一样，你想通过**谷歌域名**将一个 **gitlab 页面**网站重定向到你自己的域名，这是给你的。
我在这里找到了 **github pages** [的信息，但是在寻找 gitlab 的细节时，却不容易找到。](https://dev.to/trentyang/how-to-setup-google-domain-for-github-pages-1p58)

### 先决条件

*   购买了一个[谷歌](https://domains.google.com)域名，如【example.com】T2
*   建立一个 [gitlab 页面](https://docs.gitlab.com/ee/user/project/pages/)网站，可在 gitlab 项目链接上获得

# 设置

### Gitlab 配置

*   1.进入*设置* / *页面*，取消勾选*强制 HTTPS(需要有效证件)*
*   2.进入*新域*，输入您的域名如【example.com】T2，然后确认*创建新域*
*   3.还是在*设置* / *页面*，进入你新添加的域名**example.com**的*详情*
*   4.*复制到剪贴板*生成的*验证状态*字符串，看起来像:`_gitlab-pages-verification-code.example.com TXT gitlab-pages-verification-code=1234abcd`

### 谷歌域名配置

**警告**:如果你刚刚购买了你的谷歌域名，你可能还看不到以下操作的效果。DNS 传播可能需要几个小时。

*   5.进入你的谷歌域名账户*管理*你的域名，例如【example.com】T2
*   6.转到左侧窗格的 *DNS* 并向下滚动到*自定义资源记录*
*   7.添加第一条记录，默认名称`@`，默认类型`A`，默认 [TTL](https://www.dnsknowledge.com/whatis/time-to-live-ttl/) `1H`，IPV4 地址`35.185.44.232`。这是 gitlab 页面的 IP，你可以在这里查看
*   8.添加第二个默认名称为`@`的记录，键入`TXT` default [TTL](https://www.dnsknowledge.com/whatis/time-to-live-ttl/) `1H`，并将您的 gitlab 验证码粘贴到此处

在这一步，您可以通过 ping 您的域来检查记录是否设置良好。您可以在您的终端中尝试使用`ping example.com`或`dig example.com`，gitlab 页面 IP 应该会显示出来。如果没有，检查上面的步骤。

*   9.回到你的 gitlab，*设置* / *页面*，转到你的域**example.com**的*详情*，检查*验证状态*，如果需要的话重试验证。

**瞧，**，现在当你去`http://example.com`你应该看到你的 gitlab 页面。如果您看到来自 Gitlab 的 404 错误，不要太担心，它可能需要几分钟才能传播。

你可以停在这里或者继续解锁 **www** 和 **https** 。

#### 万维网前缀

如果您想让 **www** 前缀也能正常工作，请遵循与之前相同的步骤，但使用`www.example.com`:

*   10.进入 gitlab *设置* / *页面*
*   11.进入*新域*，用 **www** 像**T5【www.example.com】T6**输入你的域名，然后用*确认创建新域*
*   12.还是在*设置* / *页面*，转到您新添加域名**[www.example.com](http://www.example.com)**的*详情*
*   13.*复制到剪贴板*生成的*验证状态*字符串，看起来像:`_gitlab-pages-verification-code.example.com TXT gitlab-pages-verification-code=1234abcd`
*   14.再次进入你的谷歌域名账户，然后*管理*你的域名，例如【example.com】T2
*   15.转到左侧窗格的 *DNS* 并向下滚动到*自定义资源记录*
*   16.添加第三条记录，默认名称`www`，默认类型`A`，默认 [TTL](https://www.dnsknowledge.com/whatis/time-to-live-ttl/) `1H`，IPV4 地址`35.185.44.232`。
*   17.添加第二个默认名称为`www`的记录，键入`TXT` default `1H`，并将您的 gitlab 验证码粘贴到此处
*   18.回到你的 gitlab，*设置* / *页面*，转到你的域**[www.example.com](http://www.example.com)**的*详情*，检查*验证状态*，如有需要重试验证。

**瞧，**，现在当你去`http://www.example.com`你应该看到你的 gitlab 页面。同样，如果您看到来自 Gitlab 的 404 错误，不要太担心，它可能需要几分钟才能传播。

#### HTTPS

*   19.那些官方的 gitlab 指令对我来说非常有效

请确保同时认证您的`example.com`和`www.example.com`域，方法是将这两个域放入 certbot 命令:
 `sudo certbot certonly -a manual -d example.com -d www.example.com --email name@email.com`

就这样，您现在应该可以通过以下方式访问您的网站:

*   `http://www.example.com`
*   `http://example.com`
*   `https://www.example.com`
*   `https://example.com`