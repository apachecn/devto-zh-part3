# React 组件来度量 QA 的代码覆盖率

> 原文：<https://dev.to/vijayst/react-component-to-measure-code-coverage-of-qa-43nm>

为了测量代码覆盖率，首先使用 babel-plugin-istanbul 测试构建。

然后在 React 应用程序中，添加来自 [npm 包](https://www.npmjs.com/package/istanbul-coverage-display)的 React 组件，以显示代码覆盖率结果。

```
npm install istanbul-coverage-display 
```

Enter fullscreen mode Exit fullscreen mode

在所有页面中添加摘要组件。它被添加到屏幕左下角的固定位置。

```
import { CoverageSummary } from 'istanbul-coverage-display' 
```

Enter fullscreen mode Exit fullscreen mode

或者，在应用程序的特定路线中添加细节组件，比如/coverage。

```
import { CoverageDetail } from 'istanbul-coverage-display' 
```

Enter fullscreen mode Exit fullscreen mode

通过在 CoverageSummary 中提供 onNavigate 属性，我们可以导航到具有 CoverageDetail 组件的路线。

```
<CoverageSummary onNavigate={navigateToDetail} /> 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多信息，为什么我们需要它来进行 QA 测试，请查看我的博客。

这个组件还有一个[演示](https://reactbook.netlify.com/)。