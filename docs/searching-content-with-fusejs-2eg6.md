# 使用 Fuse.js 搜索内容

> 原文：<https://dev.to/matthewbdaly/searching-content-with-fusejs-2eg6>

搜索是我目前非常感兴趣的一个问题。我维护的遗留项目有一个非常糟糕的搜索工具，我渴望用类似 Elasticsearch 的东西来取代它。但是对 Elasticsearch 来说太小而不值得麻烦的小网站仍然可以从一个像样的搜索实现中受益。尽管最近有一些改进，但关系数据库通常不太适合搜索，因为它们并不真正理解相关性的概念——你不能根据匹配程度来轻松地排序，而且你的数据库可能不能很好地处理模糊匹配。

作为个人项目，我目前正在开发一个小型的平面文件 CMS。它是用 PHP 构建的，但它旨在尽可能简单，没有数据库，没有缓存服务，当然也没有搜索服务，所以它需要一些小而简单，但仍然有效的搜索。

过去，我在自己的网站上使用过 Lunr.js，它在这个用例中运行得非常好。然而，对于这种情况来说是有问题的，因为索引需要在服务器端用 Javascript 生成，并且将 Node.js 添加到平面文件 PHP CMS 的堆栈中并不是一个真正的选择。我需要的是可以用我选择的任何语言生成索引，通过 AJAX 加载它，并在客户端搜索它。我最近碰巧发现了 [Fuse.js](https://fusejs.io/) ，这正是我一直在寻找的东西。

假设我们有以下索引:

```
[  {  "title":"About me",  "path":"about/"  },  {  "title":"Meet the team",  "path":"about/meet-the-team/"  },  {  "title":"Alice",  "path":"about/meet-the-team/alice/"  },  {  "title":"Bob",  "path":"about/meet-the-team/bob/"  },  {  "title":"Chris",  "path":"about/meet-the-team/chris/"  },  {  "title":"Home",  "path":"index/"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

这个指数可以以你认为合适的任何方式生成。在这种情况下，页面内容存储在 YAML 前台的 Markdown 文件中，所以我编写了一个 Symfony 控制台命令，它获取内容文件夹中的所有 Markdown 文件，解析它们以获取标题，并检索路径。您还可以检索前端的其他项目，如类别或标签，以及页面内容，并将其包含在索引中。然后数据被转换成 JSON 并保存到索引文件中。如您所见，这个 JSON 没有什么特别之处——这两个字段恰好是我选择的字段。

现在我们可以通过 AJAX 加载 JSON 文件，并将其传递给新的 Fuse 实例。您可以使用`.search()`方法搜索索引，如下所示:

```
import Fuse from 'fuse.js';
window.$ = window.jQuery = require('jquery');

$(document).ready(function () {
  window.$.getJSON('/storage/index.json', function (response) {
    const fuse = new Fuse(response, {
      keys: ['title'],
      shouldSort: true
    });
    $('#search').on('keyup', function () {
      let result = fuse.search($(this).val());

      // Output it
      let resultdiv = $('ul.searchresults');
      if (result.length === 0) {
        // Hide results
        resultdiv.hide();
      } else {
        // Show results
        resultdiv.empty();
        for (let item in result.slice(0,4)) {
          let searchitem = '<li><a href="/' + result[item].path + '">' + result[item].title + '</a></li>';
          resultdiv.append(searchitem);
        }
        resultdiv.show();
      }
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

Fuse.js 真正伟大的地方在于它可以搜索任何 JSON 内容，这使得它非常灵活。对于一个有 MySQL 数据库的站点，您可以从数据库中的一个或多个表生成 JSON，无限期地将它缓存在 Redis 或 Memcached 中，直到内容再次发生变化，然后才重新生成它，这使得客户端搜索非常高效，在正常操作期间不需要访问数据库。或者您可以从静态文件中生成它，如本例所示。这也意味着后端语言不是问题，因为您可以轻松地用 PHP、Javascript、Python 或任何其他语言生成 JSON 文件。

正如你所看到的，使用 Fuse.js 创建一个现成的工作搜索字段非常简单，但是网站列出了许多选项，允许你为自己的特定用例定制搜索，如果你打算在一个项目中使用它，我建议你浏览一下这些选项。