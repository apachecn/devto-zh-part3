# 一个简单的。NET 核心 API 设计

> 原文：<https://dev.to/cesarcodes/a-simple-net-core-api-design-57of>

有时，我们不需要通过引入大量的层来过度设计应用程序。我最近有机会将一个小型 MVC 应用程序重写为. NET Core REST API，并遵循了以下方法:
应用程序从现有的 SQL Server 数据库和现有的存储过程中提取数据。所以，我的回购看起来像这样: