# Microsoft Edge 和 Safari 中的地址栏欺骗

> 原文：<https://dev.to/prathaprathod/address-bar-spoofing-in-microsoft-edge-and-safari--471c>

第二个位置栏嘲讽事件是由巴基斯坦科学家 Rafay Baloch 发现的，他在不同的会议上发言，例如 Blackhat，讨论他对程序安全性的探索。他发现的位置嘲讽方法影响了微软 Edge 和 Safari 程序。

当一个网站把它的访客转移到另一个关闭了端口的网站时，攻击者可以从中斡旋，以他们喜欢的任何方式改变当前网站页面的内容。

由于 URL 栏先前展示了具有关闭端口的区域的位置，客户被说服他们正在仔细查看真实的站点而不是攻击者控制的站点，并且被说服进入他们的认证。

在他的想法的证据中，在将客户端转移到具有关闭端口的站点之前，Baloch 解码了 Gmail 登录页面的 base64 编码再现，然后将其添加到 DOM 中。因此，网址中的位置([http://gmail.com:8080](http://gmail.com:8080))和钓鱼网页看起来格外有说服力。Baloch 想出了如何通过使用 setinterval()工作来保持被模仿的位置稳定，setinterval()工作试图像时钟一样转移客户端。

### 用于欺骗网页浏览器地址栏的代码

Baloch 使用了下面的代码来实现前面提到的漏洞。

```
function spoof()
{
var gmail = 'PCFET0NC8+KArOK.........ZHk+PC9odG1sPg=='; // The base64 encoded version of the Gmail page
x=document.body.innerHTML=atob(gmail);
document.write("Gmail");
document.write("x");
window.location.assign("https://www.Gmail.com:8080");
}
setInterval(spoof(),100000);

```

上面的概念验证是在微软 Edge 浏览器上运行的。