# 在 Safari 和 macOS 中清除存储的 HSTS 大头针

> 原文：<https://dev.to/tuacker/clear-saved-hsts-pinnings-in-safari-and-macos-9hd>

所以今天我试着用本地主机让 SSL 在我的机器上工作。在这个过程中，我设置了 HSTS 头，告诉浏览器永远不要访问不安全的`http://`版本的本地主机。因此，当 HSTS 告诉浏览器只访问`https://`时，访问`http://localhost:4000`总是会失败。

以下是如何重置 Safari 为 HSTS pinnings 设置的缓存，以便再次访问您的本地`http://localhost`。

打开终端，按照以下步骤操作:

```
sudo killall nsurlstoraged

# or edit file, search for "localhost", remove it and then save
rm -f ~/Library/Cookies/HSTS.plist

launchctl start /System/Library/LaunchAgents/com.apple.nsurlstoraged.plist 
```

之后您可能需要重新启动 Safari。