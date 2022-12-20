# Netlify CMS 在带有 Gatsby 的文件系统上

> 原文：<https://dev.to/arcath/netlify-cms-on-the-filesystem-with-gatsby-2lge>

我正在为我正在建设的一个网站寻找 [Netlify CMS](https://www.netlifycms.org/) ,我想在我把代码放入回购之前测试它。不幸的是，默认情况下，Netlify CMS 只会让你使用 git 作为后端，一旦网站启动，这是很好的，但当我想测试 CMS 或动态更改配置时，唯一的测试方法将是提交代码，T2 应该工作，然后希望。这给我留下了不好的印象，如果代码命中了回购协议，它应该通过测试，所以让一些理论上应该有效的东西运行对我来说并不合适。

Netlify CMS 通常通过提交 git repo 直接编辑*源*文件。我需要一种直接对本地文件系统进行更改的方法。

在修改配置之前，我需要安装[gatsby-plugin-Netlify-CMS](https://www.npmjs.com/package/gatsby-plugin-netlify-cms)，它添加了使用 Netlify CMS 和 Gatsby 所需的一切，并创建了`static/admin/config.yml`。

本文文件的重要部分是`backend`，对我来说是:

```
backend:
  name: github
  repo: Arcath/... 
```

# FS 后端

输入 [netlify-cms-backend-fs](https://www.npmjs.com/package/netlify-cms-backend-fs) 。

为了在 Gatsby 中实现这一点，我需要对我的`gatsby-config.js`进行一些编辑，以加载 fs-api 并配置`gatsby-plugin-netlify-cms`，使其使用 fs 后端。

首先，`plugins`数组中的条目需要改为:

```
{
  resolve: `gatsby-plugin-netlify-cms`,
  options: {
    modulePath: `${__dirname}/src/cms/init.js`, // Or another path if you don't want to create /src/cms/init.js
    enableIdentityWidget: false,
    publicPath: 'admin',
    htmlTitle: 'Content Manager',
    manualInit: true,
  },
} 
```

之后，我需要从后端请求文件系统 api。

```
let fsApi = require('netlify-cms-backend-fs/dist/fs/fs-express-api') 
```

Gatsby 配置需要中间件加载，这可以通过向配置对象添加`developMiddleware: fsApi`来完成。

为了在开发中使用它，我在我的`config.yml`中添加了一个`development_overrides`键:

```
development_overrides:
  backend:
    name: file-system
    api_root: 'http://localhost:8000/api' 
```

现在我已经准备好了所有的配置，我只需要用`src/cms/init.js`
把它们绑在一起

```
import CMS, { init } from 'netlify-cms'
import {FileSystemBackend} from 'netlify-cms-backend-fs'

// If running in development
if(process.env.NODE_ENV === 'development') {
  window.CMS_ENV = 'development_overrides' // Set the CMS_ENV to the development_ overrides.
  CMS.registerBackend('file-system', FileSystemBackend) // Register the FileSystemBackend.
}

// Start NetlifyCMS
init() 
```

让我在本地机器上测试和使用 Netlify CMS 对我来说非常有效。

***原贴于我的博客[此处](https://arcath.net/2019/01/netlify-cms-on-the-filesystem-with-gatsby)*T5】**