# 以 UTC 而不是本地时区运行节点

> 原文：<https://dev.to/kelkes/running-node-in-utc-instead-of-local-timezone-3gki>

将 env 变量`TZ`设置为`utc`，以 UTC 而不是您的本地时区运行节点流程。这允许在本地调试日期时间问题，因为服务器通常以 UTC 运行。

例如`TZ=utc node index.js`