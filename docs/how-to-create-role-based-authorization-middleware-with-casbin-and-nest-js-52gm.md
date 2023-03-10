# 如何用 Casbin 和 Nest.js 创建基于角色的授权中间件

> 原文：<https://dev.to/dwipr/how-to-create-role-based-authorization-middleware-with-casbin-and-nest-js-52gm>

*读上[我的网站](https://antaraksi.com/casbin-midleware-for-nest-js-with-restful-policy/)* 。

在本文中，我将向您展示如何使用 Casbin 和 Nest.js 创建基于角色的授权中间件。Casbin 是一个授权库，支持访问控制模型，如 ACL、RBAC、Golang 的 ABAC、Java、PHP 和 Node.js，但在本文中，我们将使用 Node.js

Nest 是一个渐进式 Node.js 框架，用于构建高效、可靠和可伸缩的服务器端应用程序。它使用 Typescript 语言并支持多核服务器，如 Express、Koa 或 Fastify。

如果你更喜欢用视频格式观看:
[https://www.youtube.com/embed/mWlPNrCgVdE](https://www.youtube.com/embed/mWlPNrCgVdE)

以下是文本格式:

在我们创建项目之前，首先安装 Nest。

```
npm i -g @nestjs/cli 
```

然后我们用 Nest cli
创建一个项目

```
nest new theproject 
```

然后，打开项目目录并安装 Casbin

```
cd theproject
npm install --save casbin 
```

在此之后，我们进入编码阶段。

首先，为 Casbin 创建策略配置。

在 src 中，创建一个名为 casbin_conf 的目录。

```
cd src
mkdir casbin_conf 
```

在 casbin_conf 中创建 model.conf 和 policy.csv

文件 model.conf:

```
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = r.sub == p.sub && keyMatch(r.obj, p.obj) && (r.act == p.act || p.act == "*") 
```

文件 policy.csv:

```
p, admin, /*, GET
p, notadmin, /, POST 
```

在该策略中，我们定义了两条规则，一条是针对具有管理员角色的用户，另一条是针对没有管理员角色的用户。

之后，我们创建嵌套中间件。

在 src 中创建一个名为 authorization.middleware.ts 的中间件

文件授权.中间件. ts:

```
import { Enforcer } from 'casbin';

export class BasicAuthorizer {
    private req: any;
    private enforcer: any;

    constructor(req, enforcer) {
        this.req = req;
        this.enforcer = enforcer;
    }

    getUserRole() {
        const { user } = this.req;
        const { role } = user;
        return role;
    }

    checkPermission() {
        const { req, enforcer } = this;
        const { originalUrl: path, method } = req;
        const userRole = this.getUserRole();
        return enforcer.enforce(userRole, path, method);
    }
}

// the authorizer middleware
export function authz(newEnforcer: () => Promise<Enforcer>) {
    return async (req, res, next) => {
        const enforcer = await newEnforcer();

        // user sample
        req.user = {role: 'admin'};

        if(!(enforcer instanceof Enforcer)) {
            res.status(500).json({500: 'Invalid enforcer'});
            return;
        }

        const authorizer = new BasicAuthorizer(req, enforcer);
        if(!authorizer.checkPermission()) {
            res.status(403).json({403: 'Forbidden'});
            return;
        }

        next();
    }
}; 
```

这就是授权中间件逻辑，Casbin 将帮助检查权限。

然后我们将中间件集成到 Nest 项目引导中。

找到要点，在 app 中集成授权中间件:

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

import { newEnforcer } from 'casbin';
import { authz } from './authorization.middleware';

import { join } from 'path';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // authorization middleware that is registered as global middleware
  app.use(authz(async() => {
      const enforcer = await newEnforcer(join(__dirname, 'casbin_conf/model.conf'), join(__dirname, 'casbin_conf/policy.csv'));
      return enforcer;
  }));
  await app.listen(3000);
}
bootstrap(); 
```

这就是在应用程序中注册为全局中间件的授权中间件。

启动项目进行测试:

```
npm run start:dev 
```

如果您想进行试验，请在 authz 中修改示例用户，如果您想将其投入生产，请删除它。

这里是项目[仓库](https://github.com/x1yy/theproject)。