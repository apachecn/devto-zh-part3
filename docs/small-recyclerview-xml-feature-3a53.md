# 小型 RecyclerView XML 功能

> 原文：<https://dev.to/devit951/small-recyclerview-xml-feature-3a53>

您知道吗，您可以直接在 XML 中设置 RecyclerView layoutManager，而不是总是在代码中设置 RecyclerView layoutManager。

## 例子

将此属性之一粘贴到 XML 格式的 RecyclerView 中。

对于 GridLayoutManager:
`app:layoutManager="android.support.v7.widget.GridLayoutManager"`

对于 LinearLayoutManager:
`app:layoutManager="android.support.v7.widget.LinearLayoutManager"`

而对于 StaggeredGridLayoutManager:
`app:layoutManager="android.support.v7.widget.StaggeredGridLayoutManager"`