# 介绍 Syncfusion 的 JavaScript PDF 浏览器 Web 版

> 原文：<https://dev.to/syncfusion/introducing-syncfusion-s-javascript-pdf-viewer-for-web-26n9>

Syncfusion 很高兴地告诉您，用于 web 框架的 [JavaScript PDF Viewer](https://www.syncfusion.com/javascript-ui-controls/js-pdf-viewer) 组件的生产版本将从 2019 Volume 1 release(版本 17.1.0.38)开始提供。

在当前版本中，我们在查看体验和新功能方面做了各种改进，如从右到左支持、可访问性和文本标记注释。PDF 查看器组件可与其他第三方框架互操作，如 **Angular** 、 **React** 和 **Vue.js** 。

PDF 查看器旨在提供各方面的高性能，但它在以下方面尤其强大:

*   **即时加载**–即时加载数千页的 PDF 文件。
*   **精确渲染**–准确无误地渲染 PDF 页面。
*   **虚拟化页面**–按需呈现页面，帮助减少处理大型 PDF 文件时的初始加载时间。

在这篇博客中，您将了解如何开始使用 PDF 查看器及其功能集、用途和不同的模块，以及路线图的详细信息。

## 关键特性

*   受 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 和 [RC4](https://en.wikipedia.org/wiki/RC4) 加密保护的普通和 PDF 文件都可以打开和显示。
*   **JavaScript PDF Viewer** 支持核心交互，如滚动、缩放、平移和页面导航。
*   内置工具栏。
*   从 PDF 文件中选择和复制文本。
*   可以轻松地在 PDF 文档中搜索文本。
*   借助书签、缩略图、超链接和目录，轻松导航。
*   提供两种视图模式:适合页面和适合宽度。
*   可以直接从浏览器打印整个文档、特定页面或自定义范围的页面。
*   可以借助文本标记注释(如突出显示、下划线和删除线)来突出显示文本。

## 在哪里使用 PDF 查看器？

以下是 JavaScript PDF 查看器组件的一些常见用法:

*   在云/文件存储上预览 PDF 文件。
*   在您的 web 应用程序中显示 PDF 发票或报告。
*   检查 PDF 文件。

## **创建 PDF 查看器**

为了配置 PDF Viewer，我们必须在服务器端和客户端执行更改。

### Web API 服务创建

JavaScript PDF 查看器具有从 PDF 文档获取细节的服务器端依赖性。首先，让我们看看为服务器端处理创建 ASP.NET 核心 Web API 服务的步骤。

1.  在 Visual Studio 菜单栏中选择“文件”>“新建”>“项目”。![Create New VS Project](img/bc02e960ae321243033585b42b70845c.png)

*创建新的 VS 项目*

1.  选择 ASP.NET 核心 Web 应用程序，更改应用程序名称，然后单击确定。![Choose ASP.NET Core Web Application](img/79aebb52ef34613679c17ab50d925488.png)

*选择 ASP.NET 核心网络应用*

1.  选择 API，然后单击确定。已经使用默认的 ASP.NET 核心模板![Select Web API Template](img/28ed22c425b282c0ca39dc8abc5570cd.png)创建了 web 应用程序项目

*选择 Web API 模板*

1.  创建项目后，添加 **Syncfusion。EJ2 . pdf viewer . aspnet . core . windows**通过使用 NuGet 包管理器![Install Syncfusion.EJ2.PdfViewer.AspNet.Core.Windows nuget package](img/6128d032525729bfc981fb89fcb6cb90.png)依赖于您的项目

安装 Syncfusion。EJ2 . pdf viewer . aspnet . core . windows

*注意:对于 [Linux](https://www.linux.org/pages/download/) 和 [macOS](https://www.apple.com/macos) 操作系统，分别使用下面的 [NuGet](https://www.nuget.org/) 包:*

*   *Syncfusion。EJ2 . pdf viewer . aspnet . core . Linux*
*   *Syncfusion。EJ2 . pdf viewer . aspnet . core . OS x*
    1.  将 PDF 文件放在 App_Data 文件夹中。
    2.  在 Controllers 文件夹中，将 ValuesController.cs 重命名为 PdfViewerController.cs，并添加以下代码。

```
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json;
using Syncfusion.EJ2.PdfViewer;
using System;
using System.Collections.Generic;
using System.IO;

namespace ej2_pdfviewer_service.Controllers
{
    public class PdfViewerController : Controller
    {
        private IHostingEnvironment _hostingEnvironment;
        public PdfViewerController(IHostingEnvironment hostingEnvironment)
        {
            _hostingEnvironment = hostingEnvironment;
        }
        [AcceptVerbs("Post")]
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for Loading the PDF documents 
        public IActionResult Load([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            MemoryStream stream = new MemoryStream();
            object jsonResult = new object();
            if (jsonObject != null && jsonObject.ContainsKey("document"))
            {
                if (bool.Parse(jsonObject["isFileName"]))
                {
                    string documentPath = GetDocumentPath(jsonObject["document"]);
                    if (!string.IsNullOrEmpty(documentPath))
                    {
                        byte[] bytes = System.IO.File.ReadAllBytes(documentPath);
                        stream = new MemoryStream(bytes);
                    }
                    else
                    {
                        return this.Content(jsonObject["document"] + " is not found");
                    }
                }
                else
                {
                    byte[] bytes = Convert.FromBase64String(jsonObject["document"]);
                    stream = new MemoryStream(bytes);
                }
            }
            jsonResult = pdfviewer.Load(stream, jsonObject);
            return Content(JsonConvert.SerializeObject(jsonResult));
        }

        [AcceptVerbs("Post")]
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for processing the bookmarks from the PDF documents 
        public IActionResult Bookmarks([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            var jsonResult = pdfviewer.GetBookmarks(jsonObject);
            return Content(JsonConvert.SerializeObject(jsonResult));
        }
        [AcceptVerbs("Post")]
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for processing the PDF documents. 
        public IActionResult RenderPdfPages([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            object jsonResult = pdfviewer.GetPage(jsonObject);
            return Content(JsonConvert.SerializeObject(jsonResult));
        }
        [AcceptVerbs("Post")]
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for rendering the ThumbnailImages
        public IActionResult RenderThumbnailImages([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            object result = pdfviewer.GetThumbnailImages(jsonObject);
            return Content(JsonConvert.SerializeObject(result));
        }
        [AcceptVerbs("Post")]
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for unloading and disposing the PDF document resources 
        public IActionResult Unload([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            pdfviewer.ClearCache(jsonObject);
            return this.Content("Document cache is cleared");
        }
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for downloading the PDF documents
        public IActionResult Download([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            string documentBase = pdfviewer.GetDocumentAsBase64(jsonObject);
            return Content(documentBase);
        }
        [HttpPost]
        [Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")]
        //Post action for printing the PDF documents
        public IActionResult PrintImages([FromBody] Dictionary jsonObject)
        {
            PdfRenderer pdfviewer = new PdfRenderer();
            object pageImage = pdfviewer.GetPrintImage(jsonObject);
            return Content(JsonConvert.SerializeObject(pageImage));
        }
        //Gets the path of the PDF document
        private string GetDocumentPath(string document)
        {
            string documentPath = string.Empty;
            if (!System.IO.File.Exists(document))
            {
                var path = _hostingEnvironment.ContentRootPath;
                if (System.IO.File.Exists(path + "\\Data\\" + document))
                    documentPath = path + "\\Data\\" + document;
            }
            else
            {
                documentPath = document;
            }
            return documentPath;
        }
    }
} 
```

1.  将 launchSettings.json 文件中的 launchUrl 更改为 **pdfviewer** (控件的名称)，如下所示。

```
{  "iisSettings":  {  "windowsAuthentication":  false,  "anonymousAuthentication":  true,  "iisExpress":  {  "applicationUrl":  "http://localhost:58767/",  "sslPort":  0  }  },  "profiles":  {  "IIS Express":  {  "commandName":  "IISExpress",  "launchBrowser":  true,  "launchUrl":  "pdfviewer",  "environmentVariables":  {  "ASPNETCORE_ENVIRONMENT":  "Development"  }  },  "ej2_pdfviewer_service":  {  "commandName":  "Project",  "launchBrowser":  true,  "launchUrl":  "pdfviewer",  "environmentVariables":  {  "ASPNETCORE_ENVIRONMENT":  "Development"  },  "applicationUrl":  "http://localhost:62978/"  }  }  } 
```

1.  使用以下代码在 ConfigureService 方法中的 application Startup.cs 处配置一个 [CORS 策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。

```
public void ConfigureServices(IServiceCollection services)
        {
            services.AddMemoryCache();
            services.AddMvc();
            services.AddCors(o => o.AddPolicy("MyPolicy", builder =>
            {
                builder.AllowAnyOrigin()
                .AllowAnyMethod()
                .AllowAnyHeader();
            }));
            services.Configure(options => options.Level = System.IO.Compression.CompressionLevel.Optimal);
            services.AddResponseCompression();
        } 
```

1.  Builder 中的 [CoresPolicyBuilder](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.cors.infrastructure.corspolicybuilder) 允许您根据需要配置策略。现在，您可以使用此策略名称应用于控制器和操作，如下面的代码示例所示。

```
[Microsoft.AspNetCore.Cors.EnableCors("MyPolicy")] 
```

您可以从以下位置下载相同的 ASP.NET 核心 Web API 服务应用程序。

[ASP.NET 核心 Web API 服务应用](http://www.syncfusion.com/downloads/support/directtrac/general/ze/ej2-pdfviewer-web-service-1084227506)

_ 注:同样，您可以使用以下链接提供的步骤为 PDF Viewer 创建 ASP.NET MVC Web API 服务，_

[https://ej2 . sync fusion . com/documentation/PDF Viewer/how-to/create-PDF Viewer-service](https://ej2.syncfusion.com/documentation/pdfviewer/how-to/create-pdfviewer-service)

您还可以从以下位置下载 ASP.NET MVC Web API 服务应用程序。

[ASP.NET MVC Web API 服务应用](http://www.syncfusion.com/downloads/support/directtrac/general/ze/PdfViewer_WebAPI_Service-1363607987?_ga=2.92758237.1100639919.1557113561-866343850.1555301521)

### 在客户端配置 PDF 查看器

现在让我们看看在客户端配置 PDF 查看器的步骤。

1.  您可以克隆 [Essential JS 2 QuickStart](https://github.com/syncfusion/ej2-quickstart) 项目，并使用以下命令安装必要的包。

```
git clone https://github.com/syncfusion/ej2-quickstart.git quickstart
cd quickstart
npm install 
```

1.  [依赖包](https://ej2.syncfusion.com/documentation/pdfviewer/getting-started/?no-cache=1#dependencies)必须映射到 config.js 配置文件中，存在于目录“quickstart/src”中，使用下面的代码示例。

```
System.config({
    paths: {
        'npm:': '../node_modules/',
        'syncfusion:': 'npm:@syncfusion/'

    },
    map: {
        app: 'app',

         //Syncfusion packages mapping
        "@syncfusion/ej2-base": "syncfusion:ej2-base/dist/ej2-base.umd.min.js",
        "@syncfusion/ej2-buttons": "syncfusion:ej2-buttons/dist/ej2-buttons.umd.min.js",
        "@syncfusion/ej2-popups": "syncfusion:ej2-popups/dist/ej2-popups.umd.min.js",
        "@syncfusion/ej2-navigations": "syncfusion:ej2-navigations/dist/ej2-navigations.umd.min.js",
        "@syncfusion/ej2-inputs": "syncfusion:ej2-inputs/dist/ej2-inputs.umd.min.js",
        "@syncfusion/ej2-splitbuttons": "syncfusion:ej2-splitbuttons/dist/ej2-splitbuttons.umd.min.js",
        "@syncfusion/ej2-dropdowns": "syncfusion:ej2-dropdowns/dist/ej2-dropdowns.umd.min.js",
        "@syncfusion/ej2-lists": "syncfusion:ej2-lists/dist/ej2-lists.umd.min.js",
        "@syncfusion/ej2-data": "syncfusion:ej2-data/dist/ej2-data.umd.min.js",
        "@syncfusion/ej2-notifications": "syncfusion:ej2-notifications/dist/ej2-notifications.umd.min.js",
        "@syncfusion/ej2-pdfviewer": "syncfusion:ej2-pdfviewer/dist/ej2-pdfviewer.umd.min.js"

    },
    packages: {
        'app': { main: 'app', defaultExtension: 'js' }
    }
});

System.import('app'); 
```

1.  ** **使用以下代码添加一个 HTML div 元素作为“index.html”中的 PDF 查看器

```
<!--Element which will be rendered as PDF Viewer --></pre><div id="PdfViewer" style="height: 600px;"></div> 
```

1.  基本的 JS 2 组件支持一组内置主题，这里我们将为 PDF 查看器使用 material 主题。要在应用程序中添加材质主题，需要在“quick start/src/styles/style . CSS”中导入一个适用的 CSS。

```
@import '../../node\_modules/@syncfusion/ej2/material.css'; 
```

1.  将以下代码放入“app.ts”中以启动 PDF 查看器。

```
// Import the required modules 
import { PdfViewer, Toolbar, Magnification, Navigation, LinkAnnotation, BookmarkView,
ThumbnailView, Print, TextSelection, TextSearch, Annotation} from '@syncfusion/ej2-pdfviewer';

// Inject the required modules
PdfViewer.Inject(Toolbar, Magnification, Navigation, LinkAnnotation, BookmarkView, ThumbnailView, Print, TextSelection, TextSearch, Annotation);

// Create the PDF Viewer instance
let pdfviewer: PdfViewer = new PdfViewer();
// Map the service URL
// Please use your own service URL of ASP.NET (Core or MVC) Web API 
pdfviewer.serviceUrl = "http://localhost:62978/api/pdfviewer";
pdfviewer.appendTo("#PdfViewer");
pdfviewer.load('inputfile.pdf', null); 
```

_ 注意:这里你必须使用来自 ASP.NET 核心的服务 URL 或 ASP.NET MVC Web API 服务应用程序 URL，如前面的步骤所提供的。_

1.  运行以下命令启动应用程序

| `npm start` |

1.  您的浏览器窗口将如下所示:![JavaScript PDF Viewer default view](img/8833acd1f7be9c6f9a368add464c5ec7.png)

*PDF 查看器默认视图*

1.  ## PDF 查看器–模块

我们探索的所有特性都被分解成单独的模块，以便支持选择性引用；您的应用程序中只需包含您需要的功能。以下是可用的模块:

*   [**工具栏**](https://ej2.syncfusion.com/documentation/api/pdfviewer/toolbar/) :内置工具栏，更好的用户交互。
*   [**放大**](https://ej2.syncfusion.com/documentation/api/pdfviewer/magnification/) :进行变焦操作，获得更好的观看体验。
*   [**导航**](https://ej2.syncfusion.com/documentation/api/pdfviewer/navigation/) :轻松导航 PDF 页面。
*   [**LinkAnnotation**](https://ej2.syncfusion.com/documentation/api/pdfviewer/#enablehyperlink) :通过超链接轻松导航进出 PDF 文档。
*   [**缩略图视图**](https://ej2.syncfusion.com/documentation/api/pdfviewer/#enablethumbnail) :允许用户通过缩略图视图轻松导航。
*   [**书签视图**](https://ej2.syncfusion.com/documentation/api/pdfviewer/bookmarkView/) :允许用户通过书签视图轻松导航。
*   [**文本选择**](https://ej2.syncfusion.com/documentation/api/pdfviewer/#enabletextselection) :从 PDF 文件中选择并复制文本。
*   [**文本搜索**](https://ej2.syncfusion.com/documentation/api/pdfviewer/textSearch/) :轻松搜索 PDF 文档中的文本。
*   **:直接从浏览器打印整篇文档或特定页面。**
***   [**标注**](https://ej2.syncfusion.com/documentation/api/pdfviewer/annotation/) :允许用户添加或编辑标注。**

 **## 支持的框架

此 PDF 查看器支持以下 Web 框架:

*   [JavaScript](https://www.syncfusion.com/javascript-ui-controls/js-pdf-viewer)
*   [角度](https://www.syncfusion.com/angular-ui-components/angular-pdf-viewer)
*   [反应](https://www.syncfusion.com/react-ui-components/react-pdf-viewer)
*   视图
*   [。网络核心](https://www.syncfusion.com/aspnet-core-ui-controls/pdf-viewer)
*   [。净 MVC](https://www.syncfusion.com/aspnet-mvc-ui-controls/pdf-viewer)
*   布拉索

## 路线图

在 Syncfusion 的 JavaScript PDF Viewer 的未来版本中，可以预期以下功能:

*   形状标注，如矩形、圆形、直线、折线和多边形
*   图章注释
*   测量工具，如长度、面积、体积、半径和周长。
*   便笺和注释

## 总结

我们希望您现在了解了 **JavaScript PDF Viewer** 的特性、它的真实用例，以及如何将其集成到 web 应用程序中。如果你想尝试 PDF 浏览器组件，你可以下载我们的[免费试用版](https://www.syncfusion.com/downloads/essential-js2)。您可以访问 [GitHub](https://github.com/syncfusion/ej2-javascript-ui-controls) 中的 PDF 查看器源代码，如果您想探索更多的应用，可以查看我们的[示例浏览器](https://ej2.syncfusion.com/demos/#/material/pdfviewer/default.html)和[文档](https://ej2.syncfusion.com/documentation/pdfviewer/getting-started/)以获得详细的解释。

如果你有任何问题或需要任何信息，请在下面的评论区告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/incidents/) 或[反馈门户](https://www.syncfusion.com/feedback/)联系我们。我们总是乐意帮助你。

介绍 Syncfusion 的 JavaScript PDF 浏览器的帖子[首先出现在](https://blog.syncfusion.com/post/introducing-syncfusion-javascript-pdf-viewer-component-for-web.aspx)[的 Syncfusion 博客](https://blog.syncfusion.com)上。**