# 将 React 应用作为 SPA 部署到 Azure 应用服务

> 原文：<https://dev.to/fabiofi/deploy-react-app-to-azure-app-service-as-a-spa-18p9>

大家好，
我在将我的 React 应用程序部署到 Azure App Service 时遇到了一些问题。
我通过运行 npm run build 创建了“build”，然后我将 build 文件夹的内容上传到“/site/wwwroot”。至少第一步，我已经创建了 web.config 文件来管理 React 应用程序的路由。
但这条路线似乎行不通。因为当我试图到达其他路径时，我看到这个错误:“没有找到请求的 URL/隐式/回调在这个服务器上没有找到。”

非常感谢
问候
法比奥