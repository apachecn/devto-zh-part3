# 面向角度延迟加载的网络感知预加载策略

> 原文：<https://dev.to/lacolaco/network-aware-preloading-strategy-for-angular-lazy-loading-4hae>

这篇文章解释了如何使*网络感知*预加载策略用于角度路由器的延迟加载。

无论用户的网络状况如何，它都可以通过延迟加载来改善用户体验。

[![](img/001550550a50c7aec296a8122689c4ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BInkreHU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6o2d6hv9v3jjbtx5pkl.png)

# 什么是预压？

**预加载**是角路由器懒加载的一个重要特性。这是从 2.1.0 开始提供的。

默认情况下，当应用程序通过`loadChildren`使用延迟加载时，分块的延迟模块将按需加载。它可以减少初始包的大小，但是用户必须等待在转换时加载块。

预加载改变了这一点。通过预加载，应用程序将在需要模块之前开始加载分块模块**。它可以通过平滑过渡来改善用户体验。**

这是一篇关于 Victor Savkin 的 Angular 预加载的最好的文章。他是这个特写的作者。

[角路由器:预加载模块](https://vsavkin.com/angular-router-preloading-modules-ba3c75e424cb)

# 预压策略

角度路由器支持使用`PreloadingStrategy`功能定制预加载行为。有两种内置策略；`PreloadAllModules`和`NoPreloading`。

`NoPreloading`是不预加载任何模块的默认行为。

引导后立即加载所有的懒惰模块。换句话说，这是“尽快”策略。

```
import { RouterModule, NoPreloading, PreloadAllModules } from '@angular/router';

@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: PreloadAllModules, // or NoPreloading
    }),
  ],
})
class AppRoutingModule {} 
```

Enter fullscreen mode Exit fullscreen mode

`PreloadingStrategy`是一个简单的类对象，实现了一个`preload`方法。因此，我们可以像下面这样轻松地定制预加载策略。

`preload`方法有两个参数；`route`和`load`。`route`是你在`routes`数组中声明的一个路由对象。`load`是触发加载模块的功能。

```
// custom-preloading-strategy.ts
import { PreloadingStrategy, Route } from '@angular/router';
import { Observable, EMPTY } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class CustomPreloadingStrategy implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    if (shouldPreload(route)) {
      return load();
    } else {
      return EMPTY;
    }
  }
}

// app-routing.module.ts
@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: CustomPreloadingStrategy,
    }),
  ],
})
class AppRoutingModule {} 
```

Enter fullscreen mode Exit fullscreen mode

# 预压问题:联网成本

预加载可以改善用户体验，但这只是在设备在足够快的网络中使用的情况下。有时，移动设备具有窄带网络连接。如果应用程序试图尽可能快地预加载所有模块，它会以一种不好的方式影响其他连接，比如 AJAX。

对于拥有强大网络的用户来说，预加载是一个合适的解决方案。如果没有，按需加载更好。但是这种情况可以非常动态地改变，所以应用程序必须在运行时获得网络信息并打开/关闭预加载。

我称之为“**网络感知预加载策略**”。

# 使用网络信息 API

[**网络信息 API**](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation) 是新的 Web 标准 API 提案。网络信息 API 提供关于系统连接的信息。

[https://www.youtube.com/embed/jO8iVc4hEe8](https://www.youtube.com/embed/jO8iVc4hEe8)

整个 API 由添加的`NetworkInformation`接口和添加到`Navigator`接口的一个属性组成:`Navigator.connection`。因为这个 API 还不是一个标准，所以 TypeScript 没有它的类型定义。所以我把它创建为`network-information-types`包，并在下面的所有示例代码中使用。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [拉科拉科](https://github.com/lacolaco) / [网络-信息-类型](https://github.com/lacolaco/network-information-types)

### 网络信息 API 的类型定义

<article class="markdown-body entry-content container-lg" itemprop="text">

# 网络信息类型

[![npm version](img/5d5e4fb15fdc7b4a9139cbec0e6347e4.png)](https://badge.fury.io/js/network-information-types)

[网络信息 API](https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API) 的类型定义

## 警告

在 TypeScript 将此 API 作为内置类型添加支持之前，这是一个临时解决方案。参见[https://github.com/Microsoft/TypeScript/issues/27186](https://github.com/Microsoft/TypeScript/issues/27186)。

## 使用

*   通过 npm 安装软件包
*   编辑您的 tsconfig.json
*   现在你得到了`navigator.connection`和它的类型！

### 安装

```
$ yarn add -D network-information-types
```

Enter fullscreen mode Exit fullscreen mode

### tsconfig.json

`network-information-types`是修改全局`navigator`类型的*环境*类型，所以**必须添加到`types`T6 中。**

用`typeRoots`解析`types`数组中的包名。默认情况下，`typesRoots`只是`./node_modules/@types`解析`network-information-types`包，直接添加相对路径如下。

```
{
  "compilerOptions": {
    ...
    "types": [
        "./node_modules/network-information-types"
    ]
  }
}
```

Enter fullscreen mode Exit fullscreen mode

### 使用类型

现在您可以将`navigator.connection`属性作为`NetworkInformation`对象来访问。

`navigator.connection`和它的属性都是可选的，因为浏览器对每一个的支持都是独立的，参见[https://developer . Mozilla . org/en-US/docs/Web/API/network information # Browser _ compatibility](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation#Browser_compatibility)。

```
// Example: http://wicg.github.io/netinfo/#example-1
if (navigator.connection)
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/lacolaco/network-information-types)

# 制定网络感知预加载策略

让我们用网络信息 API 制定网络感知预加载策略吧！以下代码定义了在上述`CustomPreloadingStrategy`示例中使用的`shouldPreload`函数。

`navigator.connection`登陆有限浏览器。所以我们必须检测这个特征。在这种情况下，

```
export function shouldPreload(route: Route): boolean {
  // Get NetworkInformation object
  const conn = navigator.connection;

  if (conn) {
    // With network information
  }
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 检测“保存数据”模式

首先，“保存数据”模式应该被优先考虑。这意味着用户非常关心其成本或性能限制的有效载荷大小。使用`NetworkInformation.saveData`属性并返回`false`。

```
export function shouldPreload(route: Route): boolean {
  // Get NetworkInformation object
  const conn = navigator.connection;

  if (conn) {
    // Save-Data mode
    if (conn.saveData) {
      return false;
    }
  }
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 检测“2G”连接

网络信息 API 可以识别网络的有效连接类型；4G，3G，2G，慢 2G。

在这个示例中，当用户在 2G 网络中时，应用程序禁用预加载。

```
export function shouldPreload(route: Route): boolean {
  // Get NetworkInformation object
  const conn = navigator.connection;

  if (conn) {
    // Save-Data mode
    if (conn.saveData) {
      return false;
    }
    // 'slow-2g', '2g', '3g', or '4g'
    const effectiveType = conn.effectiveType || '';
    // 2G network
    if (effectiveType.includes('2g')) {
      return false;
    }
  }
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

网络信息 API 还有其他几个属性，如`rtt` (RTT，连接的往返时间)。您可以为您的应用程序添加更多检查。

# 结论

*   从 2.1.0 开始，角路由器支持**预加载**功能。
*   您可以创建自己的自定义预加载策略
*   预加载仅对拥有快速网络的用户有效。
*   **网络信息 API** 在几个浏览器中都有。
*   制定网络感知预加载策略非常容易。

感谢您的阅读！