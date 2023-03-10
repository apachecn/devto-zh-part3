# Java 网页抓取简介

> 原文：<https://dev.to/scrapingbee/introduction-to-web-scraping-with-java-5i8>

Web 抓取或爬行是通过下载和解析 HTML 代码来提取您想要的数据，从而从第三方网站获取数据。

因为每个网站都不提供干净的 API，或者根本不提供 API，所以当涉及到提取网站信息时，web 抓取可能是唯一的解决方案。许多公司使用它来获取有关竞争对手价格、新闻聚合、大量电子邮件收集的信息…

几乎所有的东西都可以从 HTML 中提取，唯一“难以”提取的信息是图片或其他媒体中的信息。

在这篇文章中，我们将看到用 Java 获取和解析数据的基本技术。

### 先决条件

*   基本的 Java 理解
*   基本 XPath

### 工具

你将需要带有 [HtmlUnit](http://htmlunit.sourceforge.net)
的 Java 8

```
<dependency>
    <groupId>net.sourceforge.htmlunit</groupId>
    <artifactId>htmlunit</artifactId>
    <version>2.19</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在使用 Eclipse，我建议您在细节窗格中配置最大长度(当您单击 variables 选项卡时),这样您将看到当前页面的整个 HTML。

[![](img/1d98b172ef1c4020fc01bae68ad36689.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hjspapGm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.scrapingbee.cimg/post/intro-java/detail_pane.jpg)

### 让我们刮刮克雷格列表

对于我们的第一个例子，我们将从 Craigslist 获取商品，因为他们似乎没有提供 API，收集名称、价格和图像，并将其导出到 JSON。

首先，让我们来看看当你在 Craigslist 上搜索一个项目时会发生什么。打开 Chrome 开发工具，点击网络选项卡:

[![](img/280831be3fe828305018cc359917cfea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H-VwUWOJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.scrapingbee.cimg/post/intro-java/craiglist_request_search.jpg)

搜索网址为:

```
https://newyork.craigslist.org/search/moa?is_paid=all&search_distance_type=mi&query=iphone+6s 
```

Enter fullscreen mode Exit fullscreen mode

也可以使用

```
https://newyork.craigslist.org/search/sss?sort=rel&query=iphone+6s 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以打开你最喜欢的 IDE 了，是时候编码了。HtmlUnit 需要一个 WebClient 来发出请求。有许多选项(代理设置、浏览器、启用重定向...)

我们将禁用 Javascript，因为我们的示例不需要它，禁用 Javascript 会使页面加载更快:

```
String searchQuery = "Iphone 6s" ;

WebClient client = new WebClient();
client.getOptions().setCssEnabled(false);
client.getOptions().setJavaScriptEnabled(false);
try {
  String searchUrl = "https://newyork.craigslist.org/search/sss?sort=rel&query=" + URLEncoder.encode(searchQuery, "UTF-8");
  HtmlPage page = client.getPage(searchUrl);
}catch(Exception e){
  e.printStackTrace();
}
} 
```

Enter fullscreen mode Exit fullscreen mode

HtmlPage 对象将包含 HTML 代码，你可以用`asXml()`方法访问它。

现在我们将获取标题、图像和价格。我们需要检查一个项目的 DOM 结构:

[![](img/b5b7cbb2de605a596a6b1beefdc083cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U6AFV8ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.scrapingbee.cimg/post/intro-java/craiglist-dom-new-compressor.jpg)

使用 HtmlUnit，您有几个选择 html 标签的选项:

*   `getHtmlElementById(String id)`
*   `getFirstByXPath(String Xpath)`
*   `getByXPath(String XPath)`返回一个列表
*   很多其他的，rtfm！

因为没有我们可以使用的 ID，所以我们必须创建一个 [Xpath](http://www.w3schools.com/xsl/xpath_syntax.asp) 表达式来选择我们想要的标签。

**XPath** 是一种选择 XML 节点的查询语言(在我们的例子中是 HTML)。

首先，我们将选择所有具有类别`result-info`的`<p>`标签

然后我们将遍历这个列表，为每个项目选择名称、价格和 URL，然后打印出来。

```
List<HtmlElement> items = (List<HtmlElement>) page.getByXPath("//li[@class='result-row']") ;
if(items.isEmpty()){
  System.out.println("No items found !");
}else{
for(HtmlElement item : items){
  HtmlAnchor itemAnchor = ((HtmlAnchor) htmlItem.getFirstByXPath(".//p[@class='result-info']/a"));

  HtmlElement spanPrice = ((HtmlElement) htmlItem.getFirstByXPath(".//a/span[@class='result-price']")) ;

  String itemName = itemAnchor.asText()
  String itemUrl =  itemAnchor.getHrefAttribute()

  // It is possible that an item doesn't have any price
  String itemPrice = spanPrice == null ? "0.0" : spanPrice.asText() ;

  System.out.println( String.format("Name : %s Url : %s Price : %s", itemName, itemPrice, itemUrl));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将使用 [Jackson](https://github.com/FasterXML/jackson) 库把结果放到 JSON 中，以 JSON 格式映射条目，而不只是打印结果。

我们需要一个 POJO(普通旧 java 对象)来表示项目

**Item.java**T2】

```
public class Item {
    private String title ; 
    private BigDecimal price ;
    private String url ;
//getters and setters
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将其添加到您的 pom.xml :

```
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.7.0</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们要做的就是创建一个条目，设置它的属性，并将其转换为 JSON 字符串(或文件...)，并把前面的代码稍微改编一下:

```
for(HtmlElement htmlItem : items){
   HtmlAnchor itemAnchor = ((HtmlAnchor) htmlItem.getFirstByXPath(".//p[@class='result-info']/a"));

   HtmlElement spanPrice = ((HtmlElement) 
   htmlItem.getFirstByXPath(".//a/span[@class='result-price']")) ;

   // It is possible that an item doesn't have any 
   //price, we set the price to 0.0 in this case
   String itemPrice = spanPrice == null ? "0.0" : 
   spanPrice.asText() ;

   Item item = new Item();

   item.setTitle(itemAnchor.asText());
   item.setUrl( baseUrl + 
   itemAnchor.getHrefAttribute());

   item.setPrice(new 
   BigDecimal(itemPrice.replace("$", "")));

   ObjectMapper mapper = new ObjectMapper();
   String jsonString = 
   mapper.writeValueAsString(item) ;

   System.out.println(jsonString);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 更进一步

这个例子并不完美，还有很多可以改进的地方:

*   多城市搜索
*   处理分页
*   多标准搜索

你可以在这个 [Github repo](https://github.com/ksahin/introWebScraping) 中找到代码

这是我的第一篇博文，希望你喜欢，欢迎在评论中给我反馈。

### 进一步阅读

我最近写了一篇关于[网络抓取而不被阻塞的博文](https://dev.to/scrapingbee/a-guide-to-web-scraping-without-getting-blocked-5e7e)来解释不同的技术，以便隐藏你的抓取器，看看吧！