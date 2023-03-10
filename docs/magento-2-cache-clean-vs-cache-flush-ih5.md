# Magento 2:缓存清理与缓存刷新

> 原文：<https://dev.to/rafaelcg/magento-2-cache-clean-vs-cache-flush-ih5>

在开发过程中，我们不断使用 Magento 功能来清理或刷新缓存，但你知道区别吗？看看基于 DevDoc 的这种差异。

**php bin/magento 缓存:清理**

清除缓存类型只会从启用的 Magento 缓存类型中删除所有项目。换句话说，这个选项不会影响其他进程或应用程序，因为它只清理 Magento 使用的缓存。

> 禁用的缓存类型不会被清除。

**php bin/magento 缓存:刷新**

刷新缓存类型会清除缓存存储，这可能会影响正在使用同一存储的其他进程应用程序。

**参考:** [DevDocs 管理缓存](https://devdocs.magento.com/guides/v2.3/config-guide/cli/config-cli-subcommands-cache.html)