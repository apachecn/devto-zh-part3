# 角度通用，像哨兵一样添加错误记录器。

> 原文：<https://dev.to/raghavgarg1257/angular-universal-add-error-logger-like-sentry-1hdd>

本文主要关注如何捕捉**角度万能** app 的生产误差。我们将使用**哨兵**作为捕获平台**客户端**和**服务器**上的错误的示例模块。

这里要重点说的是 Sentry 为客户端和服务器提供的 SDK 是不一样的。此外，**服务器的 SDK 不应该加载到客户端**上，因为它将无法在浏览器上找到任何节点模块。

为了加载**客户端 SDK** ,我们将使用 app.module.ts(该文件在两个平台上都运行)

为了加载**服务器 SDK** ，我们将使用 app.server.module.ts(该文件仅在服务器上运行，因此我们将确保我们的服务器 SDK 仅在服务器上加载)

这是我们自己的通用**错误记录器**的实现。

```
// error-logger.ts

import { ErrorHandler } from '@angular/core';
import { environment } from '../environments/environment';

export class ErrorLogger implements ErrorHandler {

    static initWith(sentry: any) {
        return () => new ErrorLogger(sentry);
    }

    constructor(private sentry: any) {
        if (environment.production) {
            this.sentry.init({ dsn: environment.sentryDsn });
        }
    }

    handleError(error: any): void {
        if (environment.production) {
            this.sentry.captureException(error.originalError || error);
        }
        throw error; // for default behaviour rather than silentely dying
    }
} 
```

现在，为了覆盖 Angular 错误处理的默认行为，我们需要添加一个提供者`ErrorHandler`和我们自己的定制实现。我们将使用`useFactory`来创建我们的`ErrorLogger`的实例，并使用`*.module.ts`提供的正确 SDK，如下所示:

```
// app.module.ts

import { ..., ErrorHandler } from '@angular/core';
import * as SentryBrowser from '@sentry/browser'; // browser SDK

import { ErrorLogger } from './error-logger';

...
providers: [
  ...
  { provide: ErrorHandler, useFactory: ErrorLogger.initWith(SentryBrowser) }
] 
```

```
// app.server.module.ts

import { ..., ErrorHandler } from '@angular/core';
import * as SentryNode from '@sentry/node'; // server SDK, should not load on browser

import { ErrorLogger } from './error-logger';

...
providers: [
  ...
  { provide: ErrorHandler, useFactory: ErrorLogger.initWith(SentryNode) }
] 
```

静态的`initWith`方法将作为一个工厂，并返回我们定制的 ErrorLogger 的实例，但带有正确的 Sentry，SDK 根据平台启动。

希望，它能帮助别人。:)