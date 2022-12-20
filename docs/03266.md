# Cypress.io 中的复杂配置

> 原文：<https://dev.to/novaloop/complex-configuration-in-cypress-io-bm6>

在我们的设置中，多个站点部署了相同的代码库。因此，对所有站点重用大量测试代码是有意义的。

为此，我们决定重用同一个 Docker 容器，并通过 entrypoint 脚本提供所需的配置变量。

我们在 cypress 文件夹中有一个 config 文件夹，并且正在从所有的片段中构建最终的配置文件。

为此，我们使用 plugins 文件夹中的`index.js`文件:
(我们还安装了 merge-json npm 包)

```
const fs = require('fs-extra');
const mergeJSON = require('merge-json');
const path = require('path');

async function getConfigurationByFile(division, environment, locale) {

    let defaultConfig = {
        "env": {"localePrefix": locale}
    };

    const filePaths = [
        path.resolve(`cypress/config/${division}`, 'defaults.json'),
        path.resolve(`cypress/config/${division}`, 'pages.json'),
        path.resolve(`cypress/config/${division}`, 'seo.json'),
        path.resolve(`cypress/config/${division}`, `env-${environment}.json`)
    ];

    for (let filePath of filePaths) {
        const config = await fs.readJson(filePath);
        if (config.env) {
            Object.keys(config.env).forEach((key) => {
                config.env[key] = JSON.stringify(config.env[key]);
            });
            defaultConfig = mergeJSON.merge(defaultConfig, config);
        }
    }

    return new Promise((resolve, reject) => {
        resolve(
            defaultConfig
        );
    })
}

module.exports = (on, config) => {
    const division = config.env.division;
    const environment = config.env.environment;
    const locale = config.env.locale;

    return getConfigurationByFile(division, environment, locale);
}; 
```

Enter fullscreen mode Exit fullscreen mode

Cypress 只允许存储配置的键/值对`env`。因此，我们对它们应用`JSON.stringify()`,稍后在规范文件中，我们将它们解析回 JSON 对象`JSON.parse(Cypress.env('<env_key>'))`

我们已经用 Gitlab CI/CD 配置了我们的 Cypress.io 测试套件。可以为生产和准备环境手动触发管道，并且每天自动运行一次。

## 需要更多细节？

如果您对更多细节感兴趣，请告诉我们！