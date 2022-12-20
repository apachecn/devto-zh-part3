# 创建 Vue.js 组件库:第五部分——向 Netlify 部署文档

> 原文：<https://dev.to/siegerts/creating-a-vue-js-component-library-part-v-deploying-documentation-to-netlify-4263>

文档如果不公开就没有意思。现在，已经配置了组件库来使用 VuePress 进行文档和营销，我们需要部署它。 [Netlify](https://www.netlify.com/) 是这方面的绝佳选择！VuePress 文档很好地记录了[部署选项](https://v1.vuepress.vuejs.org/guide/deploy.html#netlify)。在这个例子中，我们将使用 Netlify。

## 部署在网络上

在[最后一篇](https://dev.to/siegerts/creating-a-vue-js-component-library-part-iv-documentation-with-vuepress-56h5)之后，组件库插件结构应该是下面这样的结构。如果您修改了一些命名约定，那也没关系。

```
.
└─ docs/
  ├── .vuepress/
  │   ├─ components/
  │   │  ├─ examples/
  │   │  │  └─ standard-component-doc.vue
  │   │  ├─ Demo.vue
  │   │  └─ SourceCode.vue
  │   ├─ config.js
  │   └─ enhanceApp.js
  ├─ components/
  │  ├─ README.md
  │  └─ standard-component.md
  ├─ guide.md
  └─ README.md 
```

Enter fullscreen mode Exit fullscreen mode

您可以将您的帐户链接到正确的公共回购，并在项目`push`上建立网站。如果你正在使用 GitHub、GitLab 等，这真的很有效。

根据您的工作流，如果需要修改通用设置，可以配置构建触发器。

完美。文档站点未在 Netlify 分配的 URL 上运行。🍸

[https://vue-component-library-template.netlify.app/](https://vue-component-library-template.netlify.app/)

### 设置自定义域

如果自定义域更适合这个项目呢？补充一下吧。

创建一个`_redirects`文件`.vuepress/public`供 Netlify 在部署过程中获取。任何放置在[公共目录](https://v1.vuepress.vuejs.org/guide/assets.html#public-files)中的文件在构建时都会被复制到生成目录的*根目录*。

```
.
└─ docs/
  ├── .vuepress/
  │   ├─ components/
  │   │  ├─ examples/
  │   │  │  └─ standard-component-doc.vue
  │   │  ├─ Demo.vue
  │   │  └─ SourceCode.vue
  │   ├─ config.js
  │   └─ enhanceApp.js
  ├─ components/
  │  ├─ README.md
  │  └─ standard-component.md
+ ├─ public/
+ │  └─ _redirects
  ├─ guide.md
  └─ README.md 
```

Enter fullscreen mode Exit fullscreen mode

一旦站点部署并配置了自定义域，重定向信息就可用。获取该配置并将其添加到新的`_redirects`文件中。信息在网络控制台的*域管理*部分。

以下示例说明了一个通过 Netlify 启用了 HTTPS 的站点。注意`https://`。

```
# Redirect default Netlify subdomain to primary domain
https://<your-site-name>.netlify.com/* https://www.<your-custom-domain>/:splat 301! 
```

Enter fullscreen mode Exit fullscreen mode

重定向将在回购的下一个`git push`生效。

### Netlify 提供的附加选项

*   片段注入
*   资产优化
*   预渲染
*   部署通知

### vue press 的其他注意事项

*   网站地图(帮助设置谷歌网站管理员工具)
*   带有前置物质的 OpenGraph 标签

## 下一步

[发布到 npm](https://dev.to/siegerts/creating-a-vue-js-component-library-part-vi-publishing-to-npm-3i3a) ！