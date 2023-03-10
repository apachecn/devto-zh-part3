# 错误当放置在 react-router 下方时，Boundary 不会重新呈现应用程序

> 原文：<https://dev.to/mydiemho/errorboundary-does-not-re-render-app-when-place-below-react-router-568f>

我正在将 ErrorBoundary 添加到我的应用程序中，但我发现当我在 react-router 下面添加 ErrorBoundary 时，应用程序不会在路由更改时重新呈现，直到我手动强制刷新。

下面是示例代码。我这里用的是 BrowserRouter。我想知道为什么这里的排列顺序很重要。

# App 直到强制刷新才重新渲染

```
<Router>
    <div className="app-shell">
        <ErrorBoundary>
            <Navbar />
            <div className="app-main">
                <Sidebar project={this.props.currentProject} />
                <MainContentRouter />
            </div>
            <ToastContainer />
        </ErrorBoundary>
    </div>
<Router> 
```

# 工作正常

```
<ErrorBoundary>
    <Router>
        <div className="app-shell">
            <Navbar />
            <div className="app-main">
                <Sidebar project={this.props.currentProject} />
                <MainContentRouter />
            </div>
            <ToastContainer />
        </div>
    <Router>
</ErrorBoundary> 
```