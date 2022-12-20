# Ghost Finder -最先进的 Ghost 搜索插件

> 原文：<https://dev.to/kingrayhan/ghost-finder-the-most-advanced-ghost-search-plugin-169f>

## ![GitHub logo](img/04febf72106d337f3956ebfaf9121dae.png) [电子主题](https://github.com/electronthemes) / [寻鬼者](https://github.com/electronthemes/ghost-finder)

### 最先进的幽灵搜索插件

<article class="markdown-body entry-content" itemprop="text">

# 幽灵找上门来了

[![image](img/c1fd02b890a07c99102a876ce22fe34d.png)](https://user-images.githubusercontent.com/7611746/55557292-5ee78a00-570b-11e9-873c-fe86f1d6aafa.png)[![](img/2e86c586a20887c07239ed00fe3ff2c3.png)](https://camo.githubusercontent.com/d799b786e7e98343facfe385501ad53495d85d69/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f7061636b6167652d6a736f6e2f762f656c656374726f6e7468656d65732f67686f73742d66696e6465722e737667)[![](img/9352315616c8664f74000e70b61f5242.png)](https://camo.githubusercontent.com/b3ce7501df5493c6652850f4ee15cae6bbad9d5b/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c616e6775616765732f636f64652d73697a652f656c656374726f6e7468656d65732f67686f73742d66696e6465722e737667)[![](img/2febd29a00484594506da7ba0af402f4.png)](https://camo.githubusercontent.com/954402978165d70cc55bf9eb74233573067e070e/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f656c656374726f6e7468656d65732f67686f73742d66696e6465722e737667)[![](img/b2e13014f228886ca328abcd9de9dd6d.png)](https://camo.githubusercontent.com/fb1d6d1f1c8c9c94aa832f730a9a529fb12b9a09/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6973737565732d7261772f656c656374726f6e7468656d65732f67686f73742d66696e6465722e7376673f7374796c653d706c6173746963)[![](img/08128da522d67d98aa42614a5359517d.png)](https://camo.githubusercontent.com/ac5334205f9b064f15a6eaa5c6652296547b358a/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6173742d636f6d6d69742f656c656374726f6e7468656d65732f67686f73742d66696e6465722e737667)

### 从[这里](https://electronthemes.com/blog/add-search-option-to-ghost-theme/)阅读关于这个插件的博客

## 装置

```
npm install ghost-finder
```

### 运筹学

[![download](img/302fcb9a9cba0bd58c291d45cc76caf8.png)](https://camo.githubusercontent.com/8608a649425c41a7d775abbdff29b890f02450a5/68747470733a2f2f696d672e69636f6e73382e636f6d2f6d6174657269616c2d73686172702f32342f3030303030302f646f776e6c6f61642d322e706e67) 下载[捉鬼者-1.0.0.zip](https://github.com/electronthemes/ghost-finder/archive/v1.0.0.zip)

**包含脚本**

```
<script src="{{asset "ghost-finder/dist/ghost-finder.js"}}"></script>
```

**Setup markup**

```
<div&gt
    <input id="search-input" type="text" placeholder="Type to search" /&gt
    <div id="search-result"></div&gt
</div>
```

**激活插件**

```
new GhostFinder({
    input: '#search-input',
    showResult: '#search-result',
    contentApiKey: //CONTENT API KEY...,
})
```

要查看如何获取 contentApiKey，[请单击此处](https://github.com/electronthemes/ghost-finder/wiki/How-to-get-contentApiKey-%3F)

* * *

### 选择

| 名字 | 缺省值 | 细节 |
| --- | --- | --- |
| 投入 | `null` **必选** | 搜索输入的 DOM 选择器 |
| 显示结果 | `null` **必选** | 将插入搜索结果的 DOM 选择器 |
| homeUrl | 当前网站 url | 您的 ghost 站点 url |
| contentApiKey | `null` **必选** | 内容 api 密钥 |
| 结果模板 | 看见 |

…</article>

[View on GitHub](https://github.com/electronthemes/ghost-finder)

## 安装

**包括剧本**

```
<script src="{{asset "ghost-search/dist/ghost-finder.js"}}"></script> 
```

**Setup markup**

```
<div>
    <input id="search-input" type="text" placeholder="Type to search" />
    <div id="search-result"></div>
</div> 
```

**激活插件**

```
<script>
    new GhostFinder({
        input: '#search-input',
        showResult: '#search-result',
        contentApiKey: //CONTENT API KEY...,
    })
</script> 
```

要查看如何获取 contentApiKey，[请单击此处](https://github.com/electronthemes/ghost-finder/wiki/How-to-get-contentApiKey-%3F)

* * *

### 选项

| 名字 | 缺省值 | 细节 |
| --- | --- | --- |
| 投入 | `null` **必选** | 搜索输入的 DOM 选择器 |
| 显示结果 | `null` **必选** | 将插入搜索结果的 DOM 选择器 |
| homeUrl | 当前网站 url | 您的 ghost 站点 url |
| contentApiKey | `null` **必选** | 内容 api 密钥 |
| 结果模板 | 见下文 | 搜索结果的结果包装模板 |
| singleResultTemplate | 见下文 | 单一搜索结果模板 |
| 摘录 _ 长度 | Fifteen | 显示在`##excerpt`变量中的字数 |
| 时间格式 | `'MMMM Do YYYY'` | `##published_at`变量的时间格式字符串 |

### 默认模板

**结果模板**

```
<ul class="search-results-wrapper">
    <p>Search match(es): ##resultCount</p>
    ##results
</ul> 
```

**单个结果模板**

```
<li><a href="##url">##title</a></li> 
```

### 变量

| 字段名 | 目的 |
| --- | --- |
| `##title` | 帖子标题 |
| `##url` | 发布 url |
| `##primary_tag_name` | 主标签的名称 |
| `##primary_tag_url` | 主标签的 Url |
| `##primary_author_name` | 主要作者姓名 |
| `##primary_author_url` | 主要作者的个人资料 url |
| `##primary_author_avater` | 第一作者简介照片 |
| `##excerpt` | 展示帖子内容的一些文字。默认字数为 15 |
| `##published_at` | 发布日期。格式可以通过`time_format`选项改变 |
| `##feature_image` | 发布特色图片 url |
| `##resultCount` | 匹配结果计数 |

[http://bit.ly/create-ghost-theme](http://bit.ly/create-ghost-theme)