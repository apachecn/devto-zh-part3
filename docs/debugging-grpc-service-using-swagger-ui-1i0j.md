# 使用 swagger 调试 gRPC 服务。

> 原文：<https://dev.to/zhangjikai/debugging-grpc-service-using-swagger-ui-1i0j>

## 什么是 gRPC-swagger

gRPC-swagger 是基于 [gRPC 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)开发的 [gRPC](https://github.com/grpc/) 调试工具。使用 [swagger-ui](https://github.com/swagger-api/swagger-ui) 可以方便地列出和调用 gRPC 方法。由于 gRPC-swagger 是基于 gRPC 反射的，您只需要在启动服务时启用反射特性，而不需要修改 proto 文件和相关的代码实现。

## 功能列表

*   易于使用，只需要在启动服务时启用反射，无需修改协议和相关实现。
*   与 swagger-ui 集成，可以方便地看到 gRPC 方法和参数的定义。
*   调用 gRPC 方法很简单。

## 截图

[![](img/09f5e17c44d7d816730bad915bdfee46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TiH59rIS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zst3t00cthsi862syhu6.png)

[![](img/195493710662e994dbb45654f424107b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--smlaX2wQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cyqn9ytqndqjjo210gpr.png)

## 现场演示

[演示](http://ui.grpcs.top)

你可以访问我们的 [github 项目](https://github.com/grpc-swagger/grpc-swagger)了解更多细节。