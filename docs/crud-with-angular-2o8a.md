# 有棱角的污垢

> 原文：<https://dev.to/nishugoel/crud-with-angular-2o8a>

[![](img/f70b66695e3929cd5d04749efc4d5e3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GN410jDa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f8ik7e3bdxup4hd8smsk.jpg)

在我们使用的几乎任何应用程序中，首先要做的重要事情是处理数据，大量的数据。驻留在服务器上的数据通过使用 HTTP 操作来处理。我们执行 HTTP GET 和 POST 操作来处理这些数据。

现在，在实际的应用程序中，这些数据存储在服务器上，并通过 API 接收。然而，出于测试的目的，我们可以伪造后端服务器，而不是使用真实的服务器。

使用伪后端服务器的不同方式有:

创建一个文件，对数据进行硬编码，然后返回这些数据。
创建一个本地 JSON 文件并使用它
使用 Angular in-memory-web-api
为了开发和测试的目的，最好的执行 CRUD 操作的方法是使用 Angular in-memory-web-api。使用这个，我们实际上可以模拟一个服务器，并通过 HTTP 请求返回模拟数据。

Angular in-memory-web-api
这个 angular-in-memory-web-api 不是 Angular Core 的一部分，但它是作为 Angular 文档中的一项服务提供的。这会将 HTTP 请求发送到本地内存中的数据存储，而不是远程服务器，使我们的任务变得更加容易。

这篇博文的主要目的是介绍如何使用 angular-in-memory-web-api 来开发一个使用 angular 的 CRUD 应用程序。在这篇博文结束时，您应该能够创建、读取、更新和删除数据。

首先，第一项任务是使用以下命令安装 angular-in-memory-web-api:

NPM install angular-in-memory-we b-API—save-dev
这里使用 save dev 标志来保存我们将用于开发目的的依赖关系。
一旦完成，您将能够在 package.json 文件内的依赖项中看到它。

点击此处阅读全文:

[https://medium . com/@ nishu 0505/crud-with-angular-5d 8f 39805 c 49](https://medium.com/@nishu0505/crud-with-angular-5d8f39805c49)