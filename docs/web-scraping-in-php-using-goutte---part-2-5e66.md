# 使用 Goutte 在 PHP 中进行网页抓取-第 2 部分

> 原文：<https://dev.to/sayopaul/web-scraping-in-php-using-goutte---part-2-5e66>

# 使用 Goutte II 在 PHP 中进行网页抓取

在[的上一篇文章](https://dev.to/sayopaul/web-scraping-in-php-using-goutte--4p6h)中，我们了解了网页抓取，并且研究了 [Goutte](https://github.com/FriendsOfPHP/Goutte) ，一个非常棒的 PHP 网页抓取库。在本文中，我们将通过浏览 [Punch](https://punchng.com) 的网站来将我们的知识付诸实践。更具体地说，我们将竭尽全力获取最新的新闻[https://punchng.com/topics/news](https://punchng.com/topics/news)头条😎。

让我们开始吧💪！

**注意:这仅用于测试目的，我不打算以任何方式复制从 Punch 获得的材料，我也不建议你这样做，因为这将侵犯版权**。

首先，我们设置 Composer 自动加载，导入 Goutte 名称空间，并实例化一个新的 Goutte 客户端:

```
 require "vendor/autoload.php";
    use Goutte\Client;
    $client = new Client(); 
```

下一步是通过$client 对象发送请求。$client 对象返回一个 crawler 实例。我们就是用这个实例来应用我们的过滤器。

```
 $crawler = $client->request('GET',"https://punchng.com/topics/news"); 
```

在 Punch news 页面的首页是文章框。每篇文章都有它自己的盒子和一个标题。分段标题”。我们要选择所有的标题(。seg-title ),然后逐一查看。我们是这样做的:

```
 $crawler->filter('.seg-title')->each(function ($node){

     }); 
```

注意方法`each()`？当当前选择(节点列表)包含多个节点时，`each()`方法允许我们迭代当前选择。如上所述，我们选择了每个标题(。因此我们有不止一个节点，我们想遍历它们。在地下，`each()`方法接受一个匿名函数的实例，遍历当前节点列表，然后将每次迭代中的一个节点传递给闭包，从而允许我们访问闭包中的当前节点(`$node`)。

```
 public function each(\Closure $closure)
     {
          $data = array();
          foreach ($this->nodes as $i => $node) {
              $data[] = $closure($this->createSubCrawler($node), $i);
          }

          return $data;
      } 
```

好了，接下来我们要做的是从当前节点提取文本。

```
 $crawler->filter('.seg-title')->each(function ($node){
         $headline = $node->text();
         echo $headline;
     }); 
```

我们通过调用方法`text()`获得节点的文本内容。接下来我们要做的就是打印出标题，这样我们就有了！每当我们运行这个脚本时，我们总能在 punch 上打印出最新的 10 个新闻标题。就像我在上一篇文章中所说的，当涉及到抓取时，几乎任何事情都是可能的(甚至登录和填写表单)。极限是你的思想😊。我真诚地希望我们能更深入，但可悲的是，这是所有的现在😅。

更多信息，请务必阅读 [DomCrawler](https://symfony.com/doc/current/components/dom_crawler.html) 、 [CssSelector](https://symfony.com/doc/current/components/css_selector.html) 和 [Goutte](https://github.com/FriendsOfPHP/Goutte) 的文档。

你有任何网页浏览的需求吗？你可以雇我来帮你解决这个问题