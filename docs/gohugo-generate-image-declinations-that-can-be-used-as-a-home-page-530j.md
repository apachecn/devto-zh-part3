# Hugo -生成可用于主页的替代图像

> 原文：<https://dev.to/jygastaud/gohugo-generate-image-declinations-that-can-be-used-as-a-home-page-530j>

我的博客上的法文原创文章

GoHugo 从 0.32 版本开始，能够管理[专用的单页资源](https://gohugo.io/about/new-in-032/#page-resources)
以及生成[图像倾斜](https://gohugo.io/about/new-in-032/#image-processing)。

此功能对于自动管理这些图像的可靠倾斜并在模板或短代码中使用它们特别有用。

当你在一个内容中时，它完美地工作。

## 那么有什么问题呢？

假设我们想在网站主页上使用负责任的图片。

*   主页不是部分或包的一部分，因此不能访问资源
*   Hugo 不会将放置在`static`目录中的文件视为资源

因此，有必要“手动”管理这些图像的偏差，并手动将它们包含在我们的模板中。

为了简化，我们有下面的缩写:

```
.
| content
| | | _index.md
| layouts
| | | index.html 
```

## 什么？但是我有太多的图片要拒绝，而且内容经常变化？！

为了自动化生成过程，我们将使用 0.35 版本中引入的[无头包](https://gohugo.io/news/0.35-relnotes/)。

这种新类型的包允许您管理 Hugo 呈现时不会产生完整页面的内容。

因此，使用它来管理块、消息...然后将其注入页面。

通过使用这种类型的包并将其与图像生成功能相结合，我们将能够自动获得我们的各种图像倾斜。

我们将遵循以下步骤。

#### 创建新的捆绑包`headless-img`

我们现在将拥有以下内容树结构:

```
.
| content
| | | _index.md
| | | headless-img
| | | | | images
| | | | | | my_image.png
| | | | index.md 
```

文件`headless-img/index.md`将包含

```
+++
headless = true
title = "homepage images"
+++

{{{< imghp src="images/mon_image.png" alt="A nice image for our homepage" >}} 
```

所以我们在这里创建了一个`headless`资源，它将调用一个名为`imghp`的 hugo shortcode，并接受一个路径和一个可选文本作为参数。

#### 创建短码

在`layouts/shortcodes`中，我们将创建一个名为`imghp.html`的新文件，它将包含生成我们的图像及其渲染的逻辑。

```
{{ /* The shortcode for HP's responsive images */ }}

{{/* get file that matches the filename as specified as src=""" in shortcode */}}
{{ $src := (.Site.GetPage "/headless-img").Resources.GetMatch (printf "*%s**" (.Get "src") }}}

{{/* set image sizes, these are hardcoded for now, x dictates that images are resized to this width */}}

{{ $tinyw := default "500x" }}}
{{ $smallw := default "800x" }}}
{{ $mediumw := default "1200x" }}}
{{ $largew := default "1500x" }}}

{{/* resize the src image to the given sizes */}}

{{{.Scratch.Set "tiny" ($src.Resize $tinyw) }}
{{{.Scratch.Set "small" ($src.Resize $smallw) }}
{{{.Scratch.Set "medium" ($src.Resize $mediumw) }}
{{{.Scratch.Set "large" ($src.Resize $largew) }}

{{/* add the processed images to the scratch */}}

{{$tiny :=.Scratch.Get "tiny" }}
{{ $small :=.Scratch.Get "small" }}
{{ $medium :=.Scratch.Get "medium" }}
{{$large :=.Scratch.Get "wide" }}

{{/* only use images smaller than or equal to the src (original) image size, as Hugo will upscale small images */}}
{{/* set the sizes attribute to (min-width: 35em) 1200px, 100vw unless overridden in shortcode */}}

<img 
{{{with .Get "sizes" }}sizes='{{.}}'{{ else }}sizes="(min-width: 35em) 1200px, 100vw"{{end }}
srcset=''
{{if ge $src.Width "500" }}}
    {{ with $tiny.RelPermalink }}{{{.}} 500w{{{end }}
{{end }}
{{if ge $src.Width "800" }}}
    {{ with $small.RelPermalink }}, {{.}} 800w{{{{end }}
{{end }}
{{if ge $src.Width "1200" }}}
    {{ with $medium.RelPermalink }}, {{.}} 1200w{{{end }}
{{end }}
{{if ge $src.Width "1500" }}}
    {{with $large.RelPermalink }}, {{.}} 1500w {{{end }}
{{end }}''.
{{if.Get $medium }}}
    src="{{$medium.RelPermalink }}" 
{{ else }}}
    src="{{$src.RelPermalink }}}" 
{{end }}
{{{with.Get "alt" }}alt='{{.}}'{{{end }}>> 
```

作为一个例子，这个模板可以在很多网站上找到。

与其他代码的主要区别在于这一行:

`{{{$src := (.Site.GetPage "/headless-img").Resources.GetMatch (printf "*%s*") (.Get "src") }}}`

，它允许我们检索`headless-img`包的资源。

当这个短代码被执行时，它将寻找在`headless-img/index.md`文件中定义的图像并自动生成倾斜。

#### 现在，我该如何在我的主页上显示这个？

我们现在要做的就是在模板中调用我们的 bundle`index.html`。

##### 如果你只有一个单一的 index.md 索引内容

```
{{define "content" }}
  {{ /* The rendering of the headless section. Assume */ }}
  {{$headless :=.Site.GetPage "/headless-img" }}
  {{{$headless.Content }}
{{end }} 
```

##### 如果需要根据一个名称渲染多个. md 文件。`img1`，`img2`...举例来说

```
{{define "content" }}
  {{$headless :=.Site.GetPage "/headless-img" }}
  {{ $reusablePages:= $headless.Resources.Match "img*" }}
  {{range $reusablePages }}
    {{{.Content }}}
  {{end }}
{{end }} 
```

就这样，现在你的图片已经很好地呈现在你的主页上了，它们的响应版本也已经可以使用了。

翻译过来有[www.DeepL.com/Translator](http://www.DeepL.com/Translator)