# 快速点击:使用 googledrive 自动上传 R Markdown 文档中的产品图形

> 原文：<https://dev.to/hrbrmstr/quick-hit-automating-production-graphics-uploads-in-r-markdown-documents-with-googledrive-3hp9>

作为一个将测量互联网上各种事物作为其日常工作的一部分的人，我可以颇有权威地说，大量组织正在使用云服务，如 Google Apps、Dropbox 和 Office 365，作为其业务流程工作流的一部分。对我来说，接触“云”的一个常规组件是当我必须与我们漂亮的制作团队共享 R 生成的图表，用于报告、演示和其他一般通信时。

这些通常是基于项目的任务，数据科学团队成员通常使用基于 git 和 AWS 的工作流来收集数据、执行分析和生成输出。虽然 git 在共享代码和确保我们分析的历史完整性方面做得很好，但我们并不期望生产团队成员成为 git 专家来使用我们的输出。他们生活在 Google Drive 中，由于有了 [`googledrive`](https://googledrive.tidyverse.org/) 包，我们只需要几行 R 代码就可以在代码和输出之间架起一座桥梁。

我们使用 [R 项目](https://support.rstudio.com/hc/en-us/articles/200526207-Using-Projects)来组织事情，或者使用[可旋转的](https://deanattali.com/2015/03/24/knitrs-best-hidden-gem-spin/) R 脚本或者这些项目中的 R markdown 文档来收集、清理和分析数据。

对于 2019 年，我们将使用新的、针对具体工作的 R markdown 模板，其中有一个新的 YAML 标题参数:

```
params:
  gdrive_folder_url: "https://drive.google.com/drive/u/2/SOMEUSELESSHEXSTRING" 
```

Enter fullscreen mode Exit fullscreen mode

它只是定义了云中最终输出目录的 Google Drive 文件夹 URL。

接下来是一个新的预配置的`knitr`块调用，位于这些生成图表的文档的开头:

```
knitr::opts_chunk$set(
  message = FALSE,
  warning = FALSE, dev = c("png", "cairo_pdf"),
  echo = FALSE,
  fig.retina = 2,
  fig.width = 10,
  fig.height = 6,
  fig.path = "prod/charts/"
) 
```

Enter fullscreen mode Exit fullscreen mode

因为制作团队需要 PDF，所以他们可以在工具中使用它。我们的测试表明,`cairo_pdf`产生了最好/最一致的输出，但是 png 在复合 HTML 文档中显示得更好，所以我们故意使用这个顺序。

真正的变化是对`fig.path`目录的一致命名。通过这样做，我们所要做的就是在文档的底部添加几行(同样是自动生成的)，让所有的输出自动进入正确的 Google Drive 文件夹:

```
# Upload to production ----------------------------------------------------

googledrive::drive_auth()

# locate the folder
gdrive_prod_folder <- googledrive::as_id(params$gdrive_folder_url)

# clean it out
gdrls <- googledrive::drive_ls(gdrive_prod_folder)
if (nrow(gdrls) > 0) {
  dplyr::pull(gdrls, id) %>%
    purrr::walk(~googledrive::drive_rm(googledrive::as_id(.x)))
}

# upload new
list.files(here::here("prod/charts"), recursive = TRUE, full.names = TRUE) %>%
  purrr::walk(googledrive::drive_upload, path = gdrive_prod_folder) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们再也不需要记得将文档拖到浏览器中，也不需要将侵入性的谷歌应用程序加载到我们的系统中，以确保正确的人在正确的时间拥有正确的文件。我们只需要使用新的 R markdown 文档类型来生成一个包含所有必需的样板文件的 starter analysis 文档。另外，`.httr-oauth`文件在`.gitignore`中被自动忽略，所以没有信息泄漏到共享的 git 存储库中。

### 鳍

如果你想尝试一下，你可以在 [sr.ht](https://git.sr.ht/~hrbrmstr/markdowntemplates) 、 [GitLab](https://gitlab.com/hrbrmstr/markdowntemplates) 或 [GitHub](https://github.com/hrbrmstr/markdowntemplates) 的`markdowntemplates`包中找到一个[预配置的模板](https://git.sr.ht/%7Ehrbrmstr/markdowntemplates/tree/master/inst/rmarkdown/templates/gdriveupload/skeleton/skeleton.Rmd)。

如果您安装了该软件包，您将能够直接从“新建文档”对话框中选择此输出类型:

[![](img/6fbcf466ba0ff3c8cfbaac577911fbfe.png)](https://rud.is/b/2019/01/24/quick-hit-automating-production-graphics-uploads-in-r-markdown-documents-with-googledrive/gdrive-auto-upload/)

和新的模板将准备好去没有复制，剪切或粘贴。

另外，由于 Google Drive 文件夹 URL 是一个 R markdown 参数，所以您也可以在脚本自动化中使用它(前提是您已经为这些脚本正确连接了 oauth)。