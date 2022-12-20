# 用一个真正的 Rest API 开发前端代码，而不用放慢速度要求后端开发。

> 原文：<https://dev.to/sirtimbly/develop-front-end-code-with-a-real-rest-api-without-slowing-down-to-ask-for-back-end-development-377j>

你有没有遇到过这种情况？您正在收集用户故事的需求，您正在审查线框和设计，看起来您已经了解了在前端代码中实现某个功能所需的一切...但是 API 开发团队还没有准备好同时开始开发这个特性？

你可以把任务推迟到下一次冲刺。

您可以在项目中针对静态数据负载进行开发。

您可以在 express 中为您的本地开发编写一个测试 api 端点。(这对于本地测试也非常方便。)

总的来说，第三种选择可能是最好的，但是如果你真的不想处理后端代码呢？我建议在你的前端项目旁边运行一个 [Strapi 应用](https://strapi.io/)。Strapi 让您几乎不用配置就可以设置一个新的 api 服务器，并且它有一个非常用户友好的管理可视化界面来管理 API 的所有细节。

什么是 Strapi？我认为它是 Rails、Laravel 或 ASP.Net Core 等 MVC 框架和 content ful 等无头 CMS 的结合。它允许您在可视化编辑器中构建数据库模式，然后您可以点击 rest 端点来完成所有基本的 CRUD 以及对这些新模式的排序、过滤和分页。它支持模型之间的高级关系。它还有一个内置的用户账户管理和认证系统。它也有电子邮件和文件上传插件，只需要很少的配置就可以启动和运行。

可视化 api 编辑器的速度和灵活性非常适合快速原型开发，当后端开发人员需要查看最终的 API 时，您可以将他们指向您在 Strapi 中构建的模式，或者共享您从该 Strapi 项目中导出的 TypeScript 定义。(稍后将详细介绍。)

## 入门

首先，我会克隆这个回购[https://github.com/strapi/strapi-docker](https://github.com/strapi/strapi-docker)并使用它作为你自己项目的起点，或者复制你其他前端项目的相关部分。

您将需要已经在本地运行的 [docker](https://www.docker.com) ,我强烈推荐这一点，因为它使您的开发环境可移植且易于复制。下载您的 repo 的其他开发人员只需几个命令就可以进入当前的本地开发环境。

一旦你得到了那个回购，你就可以在目录中运行这个命令。

```
docker-compose up 
```

它将在`./strapi-app`中创建您的新 strapi 应用程序，并将您的 mongodb 数据存储在`./data`中。

可以在[http://localhost:1337/admin](http://localhost:1337/admin)访问管理 UI。

如果你想定制你的容器的名字或者它们被安装到的目录的名字，我建议你打开`docker-compose.yml`文件并通读它。我将“db”移动到“data/mongodb/”中，并将“strapi-app/”移动到“cms/”中。

我将这两个文件夹添加到我的`.gitignore`文件中。

```
data/*
cms/* 
```

当您在 Strapi admin 中创建新的模式和端点时，它实际上会创建新的文件和文件夹，您需要将它们签入 Git。因此，将这一行添加到您的`.gitignore`文件中。

```
!cms/api 
```

## 数据播种

您可能会问的另一件事是，我如何从本地 mongodb 获取服务配置和数据，并让其他开发人员全部导入？

您可以创建脚本，将数据从 mongodb 导出到 json，然后在 docker 创建容器后再导入回来。

我将在 mongodb 配置中挂载一个额外的文件系统点。

```
volumes:
      - ./data/mongodb:/data/db
      - ./seed:/data/seed 
```

这是我的`export.sh`剧本

```
#!/bin/sh
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoexport -d strapi -c users-permissions_permission -o /data/seed/permission.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoexport -d strapi -c users-permissions_role -o /data/seed/role.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoexport -d strapi -c users-permissions_user -o /data/seed/user.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoexport -d strapi -c panel -o /data/seed/panels.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoexport -d strapi -c panelitem -o /data/seed/panelItems.json" 
```

运行它，然后用 Git 签入对任何`/seed/*.json`文件的更改，这样其他人就可以共享您的数据。

当您设置项目或需要同步数据时，您可以运行类似于此`import.sh`脚本的脚本。

```
#!/bin/sh
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoimport -d strapi -c users-permissions_permission /data/seed/permission.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoimport -d strapi -c users-permissions_role /data/seed/role.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoimport -d strapi -c users-permissions_user /data/seed/user.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoimport -d strapi -c panel /data/seed/panels.json"
docker exec project-dev-cms_mongodb_1 /bin/sh -c "mongoimport -d strapi -c panelitem /data/seed/panelItems.json" 
```

即使你不依赖于其他团队来开发你的 API，也许知道如何做到这一点是很好的，这样你就可以为不需要大规模后端开发的较小项目做自己的后端开发人员。

## 为您的 Strapi 模式获取类型脚本定义

我总是推荐使用 TypeScript。如果您想快速地将所有模式元数据放入您的前端代码，您可以直接从 api 模型中提取定义。

这个项目—[strapi-to-typescript](https://www.npmjs.com/package/strapi-to-typescript)—将扫描您的 strapi 项目中的 settings.json 文件并创建。ts 文件的接口相匹配，无论你想。

这就转了:

```
{  "connection":  "default",  "collectionName":  "panel",  "info":  {  "name":  "panel",  "description":  ""  },  "options":  {  "timestamps":  true  },  "attributes":  {  "Title":  {  "default":  "",  "type":  "string"  },  "Instructions":  {  "default":  "",  "type":  "string"  },  "Minifiable":  {  "default":  false,  "type":  "boolean"  }  }  } 
```

成这样:

```
 /**
 * Model definition for panel
 */
export interface IPanel {
  id: string;
  Title?: string;
  Instructions?: string;
  Minifiable?: boolean;
} 
```

维奥拉。🎉

* * *

有什么问题吗？让我在这里的评论中知道它是如何进行的。