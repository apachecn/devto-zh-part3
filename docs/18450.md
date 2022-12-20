# 无论如何都要试着运行 libp2p(js-libp2p)。

> 原文：<https://dev.to/sot528/try-to-run-libp2pjs-libp2p-anyway-5b4h>

我试着运行 js-libp2p 的 Javascript 实现 [libp2p](https://github.com/libp2p) 。

# 做什么。

我试着运行[传输](https://github.com/libp2p/js-libp2p/tree/0b75f99d752f7960ce4377d54b098be914600200/examples/transports)。

# 怎么办。

我把上面的过程做了一个 [docker 镜像](https://hub.docker.com/r/sot528/libp2pjstest/)。

```
docker run -it sot528/libp2pjstest 
```

在容器中运行`3.js`。

```
root@73b031df5d57:~/work# node 3.js 
```

然后，3 个节点将运行并相互连接。

```
node 1 is listening on:
/ip4/127.0.0.1/tcp/32907/ipfs/QmVSd89zgVLqC3cUVzN3R3dpA8xextxEFBuACd8uSMXXLM
/ip4/172.17.0.4/tcp/32907/ipfs/QmVSd89zgVLqC3cUVzN3R3dpA8xextxEFBuACd8uSMXXLM
node 2 is listening on:
/ip4/127.0.0.1/tcp/40709/ipfs/QmWe7Md6CyDDXaApaXPijKuXYhpK3uktmMamuw5MzW3zWu
/ip4/172.17.0.4/tcp/40709/ipfs/QmWe7Md6CyDDXaApaXPijKuXYhpK3uktmMamuw5MzW3zWu
/ip4/127.0.0.1/tcp/10000/ws/ipfs/QmWe7Md6CyDDXaApaXPijKuXYhpK3uktmMamuw5MzW3zWu
node 3 is listening on:
/ip4/127.0.0.1/tcp/20000/ws/ipfs/QmeKhkwXHPQ29BzbRJuqzMdkKDzWVwwFreWKRRJDLRvw1H
node 3 failed to dial to node 1 with: Circuit not enabled and all transports failed to dial peer QmVSd89zgVLqC3cUVzN3R3dpA8xextxEFBuACd8uSMXXLM!
node 1 dialed to node 2 successfully
node 2 dialed to node 3 successfully 
```

你可以在编辑`3.js`时尝试你想要的。