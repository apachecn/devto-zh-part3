# 故障排除。MacOS 上的 NET 核心开发证书

> 原文：<https://dev.to/cesarcodes/troubleshooting-net-core-dev-certs-on-macos-179d>

如果您在 MacOS 上遇到以下错误，请继续阅读。

`Unable to configure HTTPS endpoint. No server certificate was specified, and the default developer certificate could not be found.`

错误描述建议通过生成证书并信任它来解决错误:

`dotnet dev-certs https`
T1】

如果这解决了你的问题，酷！

如果没有，下一步是尝试清除并重新生成证书:

`dotnet dev-certs https --clean`
`dotnet dev-certs https`
T2】

如果这不能解决您的问题，您可能有一个冲突的证书。要解决这个问题，请从 Spotlight 打开“钥匙串访问”，然后在左侧访问“证书”并删除现有的“本地主机”证书。此时，继续并再次生成/信任:

[![KeyChain Access](img/ccbf5a25b7ba985386b52bd34102d799.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ryJ0b_aB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e1a6lr0ygpj91siye0c5.png)

`dotnet dev-certs https`
T1】

那可能会解决你的问题。希望有帮助！