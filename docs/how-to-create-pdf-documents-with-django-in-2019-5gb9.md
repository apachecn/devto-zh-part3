# 如何在 2019 年用 Django 创建 PDF 文档

> 原文：<https://dev.to/djangotricks/how-to-create-pdf-documents-with-django-in-2019-5gb9>

[![](img/27bdc81e29bc96cc775e5e40778158d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r495Rvfa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-7ryrK96vtEc/XDgT8U3TaSI/AAAAAAAAB7k/IfjBWM8qyk8Q-hpRRTgW9Upq4EvT3DJcgCLcBGAs/s1600/how-to-create-pdf--documents-with-django-in-2019.png)

如果你读过我的《用 Django 开发 Web》食谱，你可能会记得用 **Pisa xhtml2pdf** 创建 PDF 文档的食谱。这个库完成了它的工作，但是它只支持 HTML 和 CSS 特性的一个子集。例如，对于多栏布局，您必须使用表格，就像 1994 年一样。

我需要一些新鲜和灵活的选项来为我一直在构建的[捐赠平台**www.make-impact.org**](http://www.make-impact.org/)和[战略规划者**第一件事第一件事**](https://www.1st-things-1st.com) 生成捐赠收据。经过快速搜索，我找到了另一个更合适的图书馆。它叫 **[WeasyPrint](http://weasyprint.org/)** 。在这篇文章中，我将告诉你如何与 Django 一起使用它，以及它有什么价值。

## 特性

WeasyPrint 使用 HTML 和 CSS 2.1 创建像素完美，或者说点完美的 PDF 文档。 **WeasyPrint** 不使用 WebKit 或 Gecko，但有自己的渲染引擎。作为其正确工作的证明，它通过了 web 开发人员中著名的 Acid2 测试，该测试是在 HTML5 之前创建的，用于检查浏览器与 CSS 2 标准的兼容性。

文档中的[列出了所有支持的特性(和不支持的例外)。但我最喜欢的是这些:](http://weasyprint.readthedocs.io/en/latest/features.html)

*   **具有浮动元素的布局。**如果你想在信中把收信人地址放在左边，寄信人信息放在右边，或者你想把主要内容和旁注放在练习本上，你就不必再用表格了。只需使用浮动元素。
*   **工作环节。**生成的文档可以包含指向外部 URL 和内部锚点的可点击链接。你可以直接创建一个可点击的目录或者一个可以回到你的网站的横幅。
*   **支持网页字体。**有了各种各样的可嵌入 web 字体，您的文档看起来不再乏味。为什么不用优雅的草书或者粗体的西文字母来写标题呢？
*   **背景图片。**默认情况下，打印 HTML 页面时，会打印所有前景图像，但会跳过背景。当您生成用于打印的 PDF 文档时，您可以在任何地方显示背景图像，甚至在打印页面的页边空白处。
*   **SVG 保存为矢量图像。**当 PDF 文档中有图表和图形时，您通常希望保持线条的质量。即使它们在屏幕上看起来很好，光栅图像也可能不是您想要的，因为在打印的页面上，分辨率会有所不同，质量也会降低。WeasyPrint 将 SVG 图像保存为矢量图像，因此您可以获得最高的打印质量。

## 重要提示

WeasyPrint 需要 **Python 3.4** 或更新版本。这对新的 Django 项目来说很好，但如果您想将其集成到运行在 Python 2.7 上的现有网站中，这可能是一个障碍。它能成为你将旧的 Django 项目升级到新的 Python 版本的主要论据吗？

WeasyPrint**依赖于几个操作系统库** : Pango、GdkPixbuf、Cairo 和 Libffi。在文档中，有[可以理解的单行指令](http://weasyprint.readthedocs.io/en/latest/install.html)如何在不同的操作系统上安装它们。只有当您无法完全控制要部署项目的服务器时，您才会遇到问题。

如果你需要一些基本的页眉和页脚 T2，你可以使用 CSS 选择器。如果你需要为每一页使用**扩展的页眉和页脚**，最好将每一页的单独 HTML 文档合并成 PDF 文档。下面是一些例子。

有趣的是，**表情符号**是用一些奇怪的光栅单色字体绘制的。我不建议在你的 pdf 中使用它们，除非你用 SVG 图像替换它们。

## 显示代码

当一篇技术文章有一些快速的代码片段可以复制和粘贴时，它总是更有价值。给你！

### 简单 PDF 视图

这个代码片段生成一个捐赠收据，并直接在浏览器中显示。如果 PDF 可以立即下载，请将内容处理从`inline`更改为`attachment`。

```
# -*- coding: UTF-8 -*- from __future__ import unicode_literals

from django.http import HttpResponse
from django.template.loader import render_to_string
from django.utils.text import slugify
from django.contrib.auth.decorators import login_required

from weasyprint import HTML
from weasyprint.fonts import FontConfiguration

from .models import Donation

@login_required
def donation_receipt(request, donation_id):
    donation = get_object_or_404(Donation, pk=donation_id, user=request.user)
    response = HttpResponse(content_type="application/pdf")
    response['Content-Disposition'] = "inline; filename={date}-{name}-donation-receipt.pdf".format(
        date=donation.created.strftime('%Y-%m-%d'),
        name=slugify(donation.donor_name),
    )
    html = render_to_string("donations/receipt_pdf.html", {
        'donation': donation,
    })

    font_config = FontConfiguration()
    HTML(string=html).write_pdf(response, font_config=font_config)
    return response 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 CSS 进行页面配置

使用`background-image`和`content`属性:
，您的 PDF 文档可以在每一页都有一个带有图像和文本的页脚

```
{% load staticfiles i18n %}
<link href="https://fonts.googleapis.com/css?family=Playfair+Display:400,400i,700,700i,900" rel="stylesheet" />
<style>
@page {
    size: "A4";
    margin: 2.5cm 1.5cm 3.5cm 1.5cm;
    @bottom-center {
        background: url({% static 'site/img/logo-pdf.svg' %}) no-repeat center top;
        background-size: auto 1.5cm;
        padding-top: 1.8cm;
        content: "{% trans "Donation made via www.make-impact.org" %}";
        font: 10pt "Playfair Display";
        text-align: center;
        vertical-align: top;
    }
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

### 分页

您可以使用 CSS 在页脚显示页码，如下所示。

```
@page {
    margin: 3cm 2cm;
    @top-center {
        content: "Documentation";
    }
    @bottom-right {
        content: "Page " counter(page) " of " counter(pages); 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 横向页面布局

您可以使用`size: landscape`将页面旋转到水平布局。

```
@page {
    size: landscape;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 基于 HTML 的页脚

在每页的页眉或页脚显示图像和文本的另一个选项是使用带有`position: fixed`的 HTML 元素。这样你在格式上有更多的灵活性，但是所有页面上的元素都有相同的内容。

```
<style>
footer {
    position: fixed;
    bottom: -2.5cm;
    width: 100%;
    text-align: center;
    font-size: 10pt;
}
footer img {
    height: 1.5cm;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

```
<footer>
    {% with website_url="https://www.make-impact.org" %}
        <a href="{{ website_url }}">
            <img alt="" src="{% static 'site/img/logo-contoured.svg' %}" />
        </a><br />
        {% blocktrans %}Donation made via <a href="{{ website_url }}">www.make-impact.org</a>{% endblocktrans %}
    {% endwith %}
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

### 文档逐页渲染

当您需要一个具有复杂的独特页眉和页脚的文档时，最好将每个页面呈现为一个单独的 HTML 文档，然后将它们合并成一个文档。这是怎么做到的:

```
def letter_pdf(request, letter_id):
    letter = get_object_or_404(Letter, pk=letter_id)
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = (
        'inline; '
        f'filename={letter.created:%Y-%m-%d}-letter.pdf'
    )
    COMPONENTS = [
        'letters/pdf/cover.html',
        'letters/pdf/page01.html',
        'letters/pdf/page02.html',
        'letters/pdf/page03.html',
    ]
    documents = []
    font_config = FontConfiguration()
    for template_name in COMPONENTS:
        html = render_to_string(template_name, {
            'letter': letter,
        })
        document = HTML(string=html).render(font_config=font_config)
        documents.append(document)

    all_pages = [page for document in documents for page in document.pages]
    documents[0].copy(all_pages).write_pdf(response)

    return response 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

我相信 **WeasyPrint** 不仅可以用于发票、门票或预订确认，还可以用于在线杂志和小册子。如果您希望看到 WeasyPrint 的 PDF 渲染效果，请在 www.make-impact.org(准备就绪后)向您选择的组织捐款，并下载捐款收据。或者查看 my.1st-things-1st.com[的演示账户](https://my.1st-things-1st.com)，找到按钮下载 PDF 格式的优先化项目结果。

* * *

丹尼尔·科尔派的封面照片。