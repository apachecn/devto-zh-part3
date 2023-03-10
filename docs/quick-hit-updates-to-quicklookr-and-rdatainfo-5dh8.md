# 快速链接:QuickLookR 和{rdatainfo}的更新

> 原文：<https://dev.to/hrbrmstr/quick-hit-updates-to-quicklookr-and-rdatainfo-5dh8>

我在这里使用 GitUgh 链接 b/c[问题](https://github.com/hrbrmstr/QuickLookR/issues/2)在那里提交。那些不希望被微软监视的人可以在 SourceHut 和 GitLab 中找到 macOS QuickLook 插件项目和`{rdatainfo}`项目(分别是`~hrbrmstr`和`hrbrmstr`账号)。

自从 [2016](https://rud.is/b/2016/08/06/quicklookr-a-macos-quicklook-plugin-for-r-data-files/) 以来，我根本没有碰过 [`QuickLookR`](https://github.com/hrbrmstr/QuickLookR) 或 [`{rdatainfo}`](https://github.com/hrbrmstr/rdatainfo) ，因为它实际上只是一个概念验证的例子。然而让它处理 R markdown ( `Rmd`)文件的[建议](https://github.com/hrbrmstr/QuickLookR/issues/2)感觉很有用，所以我更新了`{rdatainfo}`以更好地处理`rds`、`rdata`和`rda`文件扩展名的数据加载，并对 macOS `{QuickLookR}` QuickLook 扩展项目做了一个小小的更新，将`Rmd`文件视为文本文件，可以用您(或您的应用程序)为`Rmd`文件设置的默认 Finder 扩展退出程序预览和编辑。

只有当您需要/想要 R 数据文件预览支持时，才需要`{rdatainfo}`包(即 R 降价文件不需要它)。只需解压插件版本，放入`~/Library/QuickLook`。以下是四种文件类型的示例(在`saveRDS()`和`save()`下的示例代码用于生成这些数据文件，R markdown 文件是默认文件):

[![file icons](img/3fe0f44bdc216cee496006811ef050f9.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/05/icons.png?ssl=1)

文件图标

[![Rmd preview](img/9c4bc0f9b9e7cb33ab39b6e549c88c84.png)](https://i2.wp.com/rud.is/b/wp-content/uploads/2019/05/rmd.png?ssl=1)

Rmd 预览

[![rds preview](img/1c94bac3b6159c0eddf12971cc73cbbb.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/05/rds.png?ssl=1)

rds 预览

[![rdata preview](img/96a272d60c3ba3627bc601568187b8fb.png)](https://i1.wp.com/rud.is/b/wp-content/uploads/2019/05/rdata.png?ssl=1)

rdata 预览

### 鳍

这是我在 macOS 10.14 下构建的第一个 Xcode 应用程序，所以如果你在安装或编译时遇到问题，肯定会有文件问题(有一些新的共享库“问题”，我不*认为*适用于这个 Xcode 项目，但可能适用)。