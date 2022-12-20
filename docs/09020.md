# 通过订阅在您的 Vue 应用中配置实时更新

> 原文：<https://dev.to/hasurahq/configure-real-time-updates-in-your-vue-app-with-subscriptions-2lnj>

在这一课中，我们将通过添加由 Hasura engine 自动生成的 GraphQL 订阅来为我们的应用程序添加实时功能。我们将重新配置我们的 Apollo 客户端，从使用简单的解决方案 apollo-boost 到更复杂的订阅设置。我们将学习如何在我们的 apollo 提供程序中使用$subscribe，并将了解数据如何实时更新。