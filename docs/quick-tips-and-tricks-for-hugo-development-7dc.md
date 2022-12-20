# Hugo 开发的快速提示和技巧

> 原文：<https://dev.to/remotesynth/quick-tips-and-tricks-for-hugo-development-7dc>

Hugo 是 go 内置的一个非常强大的静态站点引擎。我在各种项目中使用过它，包括用它来为我运行的的[活动(包括我的免费在线月度聚会)建立网站。这是非常基本的，因为我当时不知道这一切会导致什么。最后，两年后，我花时间重建了这个网站(虽然它还没有上线)，在这个过程中，我学到了很多关于 Hugo 的新东西。](https://cfe.dev/)

虽然我赞扬 Hugo 的文档包括大量可用的示例模板和代码，但这篇文章分享了我在构建这个网站时学到的一些东西，这些东西对文档中的内容进行了一些扩展。我应该指出的是，可能有更好的方法来做我正在做的一些事情，所以，如果你们中的任何人是 Hugo 专家，我很乐意听到改进的想法。

## 基本分页

Hugo 提供配置和一个对象(`.Paginator`)来帮助[分页](https://gohugo.io/templates/pagination/)。分页列表页面上的默认项目数是 10，但在我的例子中，我只想要 5 个。为了做到这一点，我改变了我的`config.yaml`的设置。

```
paginate: 5 
```

Enter fullscreen mode Exit fullscreen mode

在页面上的项目列表下面，有一个按页码排列的页面列表，以及一个后退和前进按钮。我想做的是，如果有多个页面，就显示导航，如果有前几页，就显示后退按钮，如果有后几页，就显示前进按钮。最后，当前页面会有不同的样式，不会被链接。

```
{{ if gt .Paginator.TotalPages 1}}
  <!-- Pagination -->
  <nav class="pagination">
    {{ $paginator := .Paginator }}
    {{ if .Paginator.HasPrev }}
    <a href="{{ .Paginator.Prev.URL }}" class="pagination__page pagination__icon pagination__page--next"><i class="ui-arrow-left"></i></a>
    {{ end }}
    {{ range .Paginator.Pagers }}
      {{ if eq .PageNumber $paginator.PageNumber }}
    <span class="pagination__page pagination__page--current">{{ .PageNumber }}</span>
      {{ else }}
    <a href="{{ .URL }}" class="pagination__page">{{ .PageNumber }}</a>
      {{ end }}
    {{ end }}
    {{ if .Paginator.HasNext }}
    <a href="{{ .Paginator.Next.URL }}" class="pagination__page pagination__icon pagination__page--next"><i class="ui-arrow-right"></i></a>
    {{ end }}
  </nav>
  {{ end }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

上面代码的第一行检查我们是否有一个以上的总页数。`.Paginator.HasPrev`用于确定前一分页页面是否存在，而`.Paginator.HasNext`用于确定下一分页页面是否存在。同样，`.Paginator.Prev`包含前面的分页页面对象，`.Paginator.Next`包含下一个页面对象。`.Paginator.Pagers`包含所有要循环访问的分页页面。

您可能注意到的一个奇怪现象是，我用分页器设置了一个变量。为什么？它允许我在迭代当前页面对象的页码时比较分页页面对象的`.PageNumber`,以便在导航中正确地突出显示当前页面。

## 按日期获取帖子

这是一个与我正在创建的网站类型有关的怪癖。在我的站点中，记录了未来事件(即未来数据，因此我需要在构建时使用`--buildFuture`标志)和过去事件。如果您想获得某个日期范围内的帖子，这种查询可能也很有用。最终的结果实际上很简单，但是我承认我尝试了很多不同的方法来让它工作，但是都失败了。

在第一种情况下，我的站点上仅有的未来日期的帖子是事件，所以我只查询日期大于`now`的页面。我将它设置在一个变量中，这样我就可以检查它是否为空，如果为空，就显示一条消息。否则，我只是遍历并显示即将到来的项目。

```
{{ $upcoming := where .Site.RegularPages ".Date" "ge" now }}
{{ if ne (len $upcoming) 0 }}
    {{ range $upcoming }}
        <!-- display upcoming events -->
    {{ end }}
{{ else }}
    <p>Shoot! There are no upcoming events</p>
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

对过去事件的查询有点复杂，因为我只需要选择 events 部分中的页面。在这种情况下，我只需要嵌套我的 [where 函数](https://gohugo.io/functions/where/)。

```
{ $recorded := where (where .Site.RegularPages ".Date" "le" now) "Section" "events" }}
{{ if ne (len $recorded) 0 }}
    {{ range $recorded }}
        <!-- display recorded events -->
    {{ end }}
{{ else }}
    <p>Shoot! There are no recorded events</p>
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

## 短码

这个非常简单，在[文档](https://gohugo.io/content-management/shortcodes/)中有很好的介绍，但是我提到它是因为它是一个很容易被忽略的特性。当我希望能够在 Markdown 生成的内容中显示比 Markdown 所能处理的更复杂的内容时，短代码非常有用。例如，Hugo 有一些[内置的短代码](https://gohugo.io/content-management/shortcodes/#use-hugo-s-built-in-shortcodes)，用于添加 Gists、高亮代码等等。

在我的例子中，我想创建一个自定义的短代码来显示我在上面创建的记录事件的列表。我做的第一件事是将模板放在`/layouts/shortcodes/`中。在这种情况下，假设我将文件命名为`recorded-events.html`。接下来，我可以在页面的 Markdown 中调用它。

```
{{% recorded-events %}} 
```

Enter fullscreen mode Exit fullscreen mode

短代码实际上支持传递参数，这使得它们比我在这里展示的要强大得多，但我只是想确保您了解这个特性。

## 菜单导航

维护菜单导航会变得棘手，这就是为什么 Hugo 提供了一个[菜单对象](https://gohugo.io/templates/menu-templates/)来帮助你管理它。您可以有多个导航菜单。在我的例子中，我在`config.yaml`中定义了两个:“main”和“top”。(没错，我给它们起名字超级有创意！)

```
menu: ["main", "top"] 
```

Enter fullscreen mode Exit fullscreen mode

如果我愿意，我可以在配置中进一步定义菜单，但是，在我的例子中，我希望导航以创建记录事件的下拉列表。该列表将限于最近的 10 个事件，之后它会将您链接到包含过去事件完整列表的页面。

在这种情况下，我依赖于在每个页面的首页定义导航。例如，下面是我去年四月聚会的相关前沿内容:

```
menu:
  main:
    parent: "events"
    name: "April  2019" 
```

Enter fullscreen mode Exit fullscreen mode

这表示它在主菜单中的“事件”下(这是“记录的事件”菜单项的标识符)。现在我只需要遍历最近的 10 个，所以我使用了一个 sort 来按照子页面对象的日期降序排序。

```
{{ $currentPage := . }}
{{ range .Site.Menus.main }}
    {{ $parentNavURL := .URL }}
    <li{{ if .HasChildren }} class="nav__dropdown"{{ end }}>
    <a href="{{ .URL }}">{{ .Name }}</a>
    {{ if .HasChildren }}
    <ul class="nav__dropdown-menu">

        {{ $children := sort .Children ".Page.Date" "desc" }}
        {{ range first 10 $children }}
        <li><a href="{{ .URL }}">{{ .Name }}</a></li>
        {{ end }}
        {{ if gt .Children 10 }}
        <li><a href="{{ $parentNavURL }}">More...</a></li>
        {{ end }}
    </ul>
    {{ end }}
    </li>
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我将变量`parentNavURL`设置为父 URL(即“记录的事件”菜单项)，这样我就可以创建一个“更多...”菜单项链接到所有已记录事件的列表。然后，我只遍历前 10 个，并显示“More ...”仅当超过 10 个孩子时链接。

另一方面，尽管尝试了几次，我还是无法将事件动态添加到即将到来或已录制的菜单中。因此，我现在在导航中手动交换这些。

## 下一页/上一页导航

当你在查看一篇文章(或者，在我的例子中，是事件)时，能够直接导航到下一篇或上一篇文章可能是有用的。Hugo 通过 page 对象的一些属性使这变得非常简单。这是我的模板中的相关代码。

```
<!-- Prev / Next Post -->
<nav class="entry-navigation">
    <div class="clearfix">
    {{ if ne .PrevInSection  nil }}
    <div class="entry-navigation--left">
        <i class="ui-arrow-left"></i>
        <span class="entry-navigation__label">Previous Event</span>
        <div class="entry-navigation__link">
        {{ with .PrevInSection }}
        <a href="{{.Permalink}}" rel="next">{{ .Title }}</a>
        {{ end }}
        </div>
    </div>
    {{ end }}
    {{ if ne .NextInSection  nil }}
    <div class="entry-navigation--right">
        <span class="entry-navigation__label">Next Event</span>
        <i class="ui-arrow-right"></i>
        <div class="entry-navigation__link">
        {{ with .NextInSection }}
        <a href="{{ .Permalink }}" rel="prev">{{ .Title }}</a>
        {{ end }}
        </div>
    </div>
    {{ end }}
    </div>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

`. PrevInSection`为您提供同一站点部分中上一页的页面对象。如果不存在，则为 null(Go 的情况下为`nil`)。`with .PrevInSection`只允许我对对象属性使用简写的点符号(即使用`.Permalink`而不是`.PrevInSection.Permalink`)。

## 相关岗位

Hugo 内置了对[相关内容](https://gohugo.io/content-management/related/)的支持。它包括如何确定一个条目是否相关的默认配置，但是在我的例子中，我必须在`config.yaml`中对它进行一些定制。关键的区别在于，我主要依靠每个页面的 categories 属性来确定它是否相关，并且我希望包含更新的页面，因为我希望推荐当前的事件，例如，如果您正在查看相关的旧事件。

```
related:
  threshold: 80
  includeNewer: true
  toLower: false
  indices:
  - name: categories
    weight: 100
  - name: date
    weight: 10 
```

Enter fullscreen mode Exit fullscreen mode

在我的模板中显示相关页面非常简单。在我的例子中，我想显示当前页面的前五个相关页面。

```
{{ $related := .Site.RegularPages.Related . | first 5 }}
{{ with $related }}
{{ range . }}
    <!-- display related items -->
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，`.Site.RegularPages.Related`后面的`.`是传递给当前页面对象的函数(即`Related`)的参数。`| first 5`是一个限制结果的过滤器。同样，`with $related`只是让我在迭代中使用简写的点符号。

## 显示相对年龄

对于我的最后一个项目(至少到目前为止)，当列出特定地点的过去事件时，我希望显示事件的相对年龄，而不仅仅是日期。

```
{{ $ageDays := div (sub now.Unix .Date.Unix) 86400 }}
{{ $ageMonths := div (sub now.Unix .Date.Unix) 2592000 }}
{{ $ageYears := math.Floor (div $ageMonths 12) }}
<li class="entry__meta-date">
{{ if lt $ageDays 0 }}
    {{ $ageDays := mul $ageDays -1 }}
    In {{ $ageDays }} {{ cond (eq $ageDays 1) "day" "days"}}
{{ else if ge $ageYears 1 }}
    {{ $ageYears }} {{ cond (eq $ageYears 1) "year" "years" }} ago
{{ else if eq $ageDays 0 }}
    Today
{{ else if lt $ageDays 31 }}
    {{ $ageDays }} {{ cond (eq $ageDays 1) "day" "days"}} ago
{{ else }}
    {{ $ageMonths }} {{ cond (eq $ageMonths 1) "month" "months" }} ago
{{ end }}
</li> 
```

Enter fullscreen mode Exit fullscreen mode

这个的要点很简单。首先，我创建变量来确定页面的年、月、日。然后，利用 Hugo 提供的各种功能，我显示这些。例如，由于我有未来的事件，其中一些事件可能会返回一个负的年龄。因此，如果以天为单位的年龄小于 0，我将(使用`mul`)乘以负 1。我还使用`cond`根据条件显示单数或复数。

## 更来了...

正如我所说，我还没有完成这个网站的重新设计。如果我获得了更多的见解，我一定会分享。希望这对研究如何在 Hugo 中做事的人有用。如果你有任何关于我如何做得更好的建议，请告诉我。