# packet_write_wait:连接到 xxx 端口 22:管道损坏

> 原文：<https://dev.to/abdallah/packetwritewait-connection-to-xxx-port-22-broken-pipe-19fd>

几天前 SSH 连接开始左右下降。起初我以为是我的连接出了问题，我们在黎巴嫩贝鲁特的 DSL 连接去年变得好多了。但它还是时不时会有一些怪癖，我把这归咎于电线和糟糕的天气。

但是第二天和第三天又发生了，当我忘记启动屏幕时，乔布斯在执行过程中被杀，这真的很烦人。

长话短说，在我的家用电脑上升级 Ubuntu 后，一些设置似乎被删除或重置了。这是我在/etc/ssh/ssh_config 中添加的(不是 sshd_config！)

```
Host \* ServerAliveInterval 30 ServerAliveCountMax 5 
```

就是这样！

如果您不想编辑系统范围的配置，您可以随时编辑~/。ssh/config 具有相同的类似效果。