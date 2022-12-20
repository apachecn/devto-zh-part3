# TypeScript 中微服务的标准化运行状况检查

> 原文：<https://dev.to/denolfe/standardized-health-checks-for-microservices-in-typescript-4e9p>

> 最初发布在我的博客上，网址为[https://elliotdenolf . com/posts/standardized-health-checks-in-typescript](https://elliotdenolf.com/posts/standardized-health-checks-in-typescript)

使用微服务时，评估整个系统的健康状况至关重要。服务启动并运行并不一定意味着它能够成功地为请求提供服务。进入**健康检查**。健康检查提供了一种评估服务是否*的方法，不仅是启动和运行，而且为服务请求做好了充分准备。每个服务公开一个端点，该端点揭示其自身的健康状况以及任何下游依赖关系。其他微服务、数据库连接或服务自身的配置都是可能依赖的例子。如果服务被认为是不健康的，流量可以被路由到其他地方，服务可以被转出。*

这篇文章将介绍如何以一种易于重复的方式为其他类型的依赖关系实现一个标准化的下游依赖关系健康检查。

让我们首先定义一个抽象类，它必须由所有健康指示器和一个代表每个资源健康状况的`ResourceHealth`枚举来实现。

```
// health-indicator.ts
export abstract class HealthIndicator {
  abstract name: string;
  status: ResourceHealth = ResourceHealth.Unhealthy;
  details: string | undefined;

  abstract checkHealth(): Promise<void>;
}

// resource-health.enum.ts
export enum ResourceHealth {
  Healthy = 'HEALTHY',
  Unhealthy = 'UNHEALTHY'
} 
```

Enter fullscreen mode Exit fullscreen mode

每个健康指标:

*   默认情况下从`Unhealthy`状态开始，直到可以验证为`Healthy`
*   必须实现`checkHealth()`函数，该函数具有修改`status`的能力

我们将要验证的下游依赖项是一个 JSON api，它公开了一个`/ping`端点。下面是实现:

```
// some-service.check.ts
export class SomeServiceCheck extends HealthIndicator {
  name: string = 'Some Service';

  async checkHealth(): Promise<void> {
    let result: AxiosResponse<any>;
    try {
      const pingURL = `http://localhost:8080/ping`;
      result = await axios(pingURL);

      if (result.status === 200) {
        this.status = ResourceHealth;
      } else {
        this.status = ResourceHealth.Unhealthy;
        this.details = `Received status: ${result.status}`;
      }
    } catch (e) {
      this.status = ResourceHealth.Unhealthy;
      this.details = e.message;
      console.log(`HEALTH: ${this.name} is unhealthy.`, e.message);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`checkHealth()`实现使用`axios`库对`/ping`端点执行`GET`请求，然后评估状态。如果是 200，状态将被设置为`Healthy`。如果返回其他代码或发生错误，状态将被设置为`Unhealthy`并设置 details 属性。

接下来，让我们看看如何实现一个健康检查服务，它将管理所有不同类型的健康指标并执行它们。

```
// health.service.ts
export class HealthService {
  private readonly checks: HealthIndicator[];
  public overallHealth: ResourceHealth = ResourceHealth.Healthy;

  constructor(checks: HealthIndicator[]) {
    this.checks = checks;
  }

  async getHealth(): Promise<HealthCheckResult> {
    await Promise.all(
      this.checks.map(check => check.checkHealth())
    );

    const anyUnhealthy = this.checks.some(item =>
      item.status === ResourceHealth.Unhealthy
    );
    this.overallHealth = anyUnhealthy
      ? ResourceHealth.Unhealthy
      : ResourceHealth.Healthy;

    return {
      status: this.overallHealth,
      results: this.checks
    };
  }
}

type HealthCheckResult = {
  status: ResourceHealth,
  results: HealthIndicator[]
}; 
```

Enter fullscreen mode Exit fullscreen mode

`HealthService`做以下事情:

*   接收要在其构造函数中运行的所有健康指标
*   在`Promise.all()`语句中执行所有健康检查
*   报告系统的整体运行状况。如果所有下游依赖关系都是`Healthy`，则设置为`Healthy`。如果*任一*单个依赖为`Unhealthy`，则整个生命值将设置为`Unhealthy`。总体健康状况和所有下游依赖关系在`HealthCheckResult`响应中返回。

最后一部分将从我们服务上的一个`/health`路由调用这个服务。对于这个例子，我们将从一个可以通过`app.use(healthRoutes)`装载的 express 路由器调用服务。

```
// health.routes.ts
const healthRoutes = Router();

healthRoutes.get('/health', async (req, res) => {
  const healthService = new HealthService(
    [
      new SomeServiceCheck(),
      // Add more checks here...
    ]
  );

  const healthResults = await healthService.getHealth();

  res.status(healthResults.status === ResourceHealth.Healthy ? 200 : 503)
    .send({
      status: healthResults.status, dependencies: healthResults.results
    });
});

export { healthRoutes }; 
```

Enter fullscreen mode Exit fullscreen mode

当点击此路径时，将创建带有任何必要健康指标的健康服务，然后通过`getHealth()`运行所有检查。响应的顶层`status`将是`ResourceHealth`类型，或者是`Healthy`或者是`Unhealthy`，带有相关的 HTTP 状态代码- `200`表示健康，或者是`503`表示不健康。它还将有一个`results`属性，按名称显示每一个依赖项以及检查产生的健康状况。

对该路线执行`curl`将返回:

```
{  "status":  "HEALTHY",  "dependencies":  [  {  "name":  "Some Service",  "status":  "HEALTHY"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

此示例之外的进一步改进:

*   只需创建一个实现我们的`HealthIndicator`抽象类的类，然后传递给`HealthService`，就可以添加额外的健康指标。
*   如果需要使用 HTTP GET 请求实现进一步的检查，可以从`SomeServiceCheck`中取出另一个基类，以便重用。
*   如果要从代码的其他部分调用,`HealthService`应该作为单例来实现。

链接:

*   **[本例完整源代码](https://github.com/denolfe/blog-code-samples/tree/master/posts/standardized-health-checks)**
*   [微服务. io:健康检查 API](https://microservices.io/patterns/observability/health-check-api.html)