# SSL::VERIFY_NONE - NEVER！

> 原文：<https://dev.to/scottw/sslverifynone---never-2cpo>

我把这个小东西看作是(Ruby) API 访问推荐代码:

`http.verify_mode = OpenSSL::SSL::VERIFY_NONE`

这实际上禁用了 SSL 检查，任何人都不应该建议这样做。