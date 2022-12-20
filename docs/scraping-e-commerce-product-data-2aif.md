# 抓取电子商务产品数据

> 原文：<https://dev.to/scrapingbee/scraping-e-commerce-product-data-2aif>

在本教程中，我们将看到如何用 Java 从任何电子商务网站中提取产品数据。产品数据提取有许多不同的用例，例如:

*   电子商务价格监测
*   价格比较器
*   可用性监控
*   提取评论
*   市场调查
*   地图违规

我们将从这个产品页面中提取这些不同的字段:价格、产品名称、图片 URL、SKU 和货币:

[https://www . asos . com/the-north-face/the-north-face-vault-backpack-28 升黑色/prd/10253008](https://www.asos.com/the-north-face/the-north-face-vault-backpack-28-litres-in-black/prd/10253008)

[![](img/88968aee1b0377fc278b65b194ba9f01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iB7kU6mc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.scrapingbee.cimg/post/java-product/creenshot-2019-04-03-15.56.02.jpg)

## 你将需要什么

我们将使用 HtmlUnit 执行 HTTP 请求并解析 DOM，将这个依赖项添加到您的 pom.xml 中

```
<dependency>
   <groupId>net.sourceforge.htmlunit</groupId>
   <artifactId>htmlunit</artifactId>
   <version>2.19</version>
</dependency> 
```

我们还将使用杰克逊库:

```
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.9.8</version>
</dependency> 
```

## Schema.org

为了提取我们感兴趣的字段，我们将从 Html 标记中解析[https://schema.org](https://schema.org)元数据。

Schema 是一个 ***语义*** 词汇，可以添加到任何网页中。实现模式有很多好处。大多数搜索引擎用它来理解一个页面是关于什么的(一个产品，一篇文章，一篇评论，以及[更多的](https://schema.org/docs/schemas.html)

据 schema.org 称，全球约有 1000 万网站使用它。这是巨大的！
有不同类型的模式，今天我们来看看[产品类型](https://schema.org/Product)

这真的很方便，因为一旦你编写了一个提取特定模式数据的 scraper，它将在任何其他使用相同模式的网站上工作。没有更具体的 XPath / CSS 选择器要写！

根据我在 PricingBot(我以前的公司)的经验，大约 40%的电子商务网站在其 DOM 中使用 schema.org 元数据。

有三种主要的模式标记:

***JSON-LD*T3** 

```
<script  type="application/ld+json">  {  "@context":  "http://schema.org",  "@type":  "ItemList",  "url":  "http://multivarki.ru?filters%5Bprice%5D%5BLTE%5D=39600",  "numberOfItems":  "315",  "itemListElement":  [  {  "@type":  "Product",  "image":  "http://img01.multivarki.ru.ru/c9/f1/a5fe6642-18d0-47ad-b038-6fca20f1c923.jpeg",  "url":  "http://multivarki.ru/brand_502/",  "name":  "Brand 502",  "offers":  {  "@type":  "Offer",  "price":  "4399 p."  }  },  {  "@type":  "Product",  "name":  "..."  }  ]  }  </script> 
```

```
<div vocab="http://schema.org/" typeof="ItemList">
    <link property="url" href="http://multivarki.ru?filters%5Bprice%5D%5BLTE%5D=39600"><span property="numberOfItems">315</span>
    <div property="itemListElement" typeof="Product">
        <img property="image" alt="Photo of product" src="http://img01.multivarki.ru.ru/c9/f1/a5fe6642-18d0-47ad-b038-6fca20f1c923.jpeg"> <a property="url" href="http://multivarki.ru/brand_502/"><span property="name">BRAND 502</span></a>
        <div property="offers" typeof="http://schema.org/Offer">
            <meta property="schema:priceCurrency" content="RUB">руб
            <meta property="schema:price" content="4399.00">4 399,00
            <link property="schema:itemCondition" href="http://schema.org/NewCondition">
        </div>...
        <div property="itemListElement" typeof="Product">
          ...
        </div>
    </div>
</div> 
```

还有我们例子中使用的那个， ***微数据*** :

```
<div class="schema-org">

<div itemscope="" itemtype="https://schema.org/Product">
    <img itemprop="image" src="https://images.asos-media.com/products/the-north-face-vault-backpack-28-litres-in-black/10253008-1-black" alt="Image 1 of The North Face Vault Backpack 28 Litres in Black">
    <link itemprop="itemCondition" href="https://schema.org/NewCondition">
    <span itemprop="productID">10253008</span>
    <span itemprop="sku">10253008</span>
    <span itemprop="brand" itemscope="" itemtype="https://schema.org/Brand">
        <span itemprop="name">The North Face</span>
    </span>
    <span itemprop="name">The North Face Vault Backpack 28 Litres in Black</span>
    <span itemprop="description">Shop The North Face Vault Backpack 28 Litres in Black at ASOS. Discover fashion online.</span>
    <span itemprop="offers" itemscope="" itemtype="https://schema.org/Offer">
        <link itemprop="availability" href="https://schema.org/InStock">
        <meta itemprop="priceCurrency" content="GBP">
        <span itemprop="price">60</span>
        <span itemprop="eligibleRegion">GB</span>
        <span itemprop="seller" itemscope="" itemtype="https://schema.org/Organization">
            <span itemprop="name">ASOS</span>
        </span>
    </span>  
</div>

  </div> 
```

请注意，您可以在一个页面中有多个报价。

## 提取数据

首先是创建一个产品的基本 POJO:

```
public class Product {

    private BigDecimal price;
    private String name;
    private String sku;
    private URL imageUrl;
    private String currency;
        // ...getters & setters 
```

然后我们需要转到目标 URL 并创建一个基本的微数据解析器来提取我们感兴趣的字段。我用的是 HtmlUnit，这是一个纯 Java 的无头浏览器。我可以使用很多不同的库，比如 Jsoup 或 Selenium + Headless Chrome。

但在大多数情况下，HtmlUnit 是一个好的解决方案，因为它比 Selenium + Headless Chrome 更轻，但比原始的 HTTP client + JSoup(仅处理 Html 解析)提供了更多的功能。

对于“大量使用 Javascript”的网站，依靠像 React / Vue.js 这样的前端框架，无头 Chrome 是一条出路！

```
 WebClient client = new WebClient();
client.getOptions().setCssEnabled(false);
client.getOptions().setJavaScriptEnabled(false);
String productUrl = "https://www.asos.com/the-north-face/the-north-face-vault-backpack-28-litres-in-black/prd/10253008";

HtmlPage page = client.getPage(productUrl);
HtmlElement productNode = ((HtmlElement) page
                .getFirstByXPath("//*[@itemtype='https://schema.org/Product']"));
URL imageUrl = new URL((((HtmlElement) productNode.getFirstByXPath("./img")))
                .getAttribute("src"));
HtmlElement offers = ((HtmlElement) productNode.getFirstByXPath("./span[@itemprop='offers']"));

BigDecimal price = new BigDecimal(((HtmlElement) offers.getFirstByXPath("./span[@itemprop='price']")).asText());
String productName = (((HtmlElement) productNode.getFirstByXPath("./span[@itemprop='name']")).asText());
String currency = (((HtmlElement) offers.getFirstByXPath("./*[@itemprop='priceCurrency']")).getAttribute("content"));
String productSKU = (((HtmlElement) productNode.getFirstByXPath("./span[@itemprop='sku']")).asText()); 
```

在第一行，我创建了 HtmlUnit HTTP 客户端并禁用了 Javascript，因为我们不需要它来获取模式标记。

然后就是基本的 XPath 表达式来选择我们想要的有趣的 DOM 节点。

这个解析器远非完美，它不能提取所有内容，也不能处理多个报价。但是，这将让您了解如何提取模式数据。

然后，我们可以创建产品对象，并将其打印为 JSON 字符串:

```
Product product = new Product(price, productName, productSKU, imageUrl, currency);
ObjectMapper mapper = new ObjectMapper();
String jsonString = mapper.writeValueAsString(product) ;
System.out.println(jsonString); 
```

## 避免受阻

既然我们能够提取我们想要的产品数据，我们必须小心不要被阻塞。

由于各种原因，网站上有时会实施反 bot 机制。保护网站免受僵尸程序攻击的最明显的原因是防止繁重的自动化流量影响网站的性能(你必须小心并发请求，因为这会增加延迟...).另一个原因是阻止垃圾邮件等机器人的不良行为。

有各种保护机制。有时，如果你的机器人每秒/每小时/每天请求太多，它就会被阻止。有时，每个 IP 地址的请求数量有一个速率限制。最难保护的是有用户行为分析的时候。例如，如果同一个 IP 同时发出请求，网站可以分析请求之间的时间。

隐藏我们的抓取工具的最简单的方法是使用代理。结合随机用户代理，使用代理是一个强大的方法来隐藏我们的抓取工具，抓取速率有限的网页。当然，最好不要在第一时间被屏蔽，但有时网站只允许每天/每小时一定量的请求。

在这些情况下，您应该使用代理。有很多免费的代理列表，我不推荐使用它们，因为它们通常很慢，不可靠，而且提供这些列表的网站并不总是清楚这些代理的位置。有时公共代理名单是由一个合法的公司操作的，提供高级代理，有时不是...

我建议使用付费代理服务，或者你可以建立自己的服务。

为 HtmlUnit 设置代理很简单:

```
ProxyConfig proxyConfig = new ProxyConfig("host", myPort);
client.getOptions().setProxyConfig(proxyConfig); 
```

## 更进一步

如您所见，由于 Schema.org 数据，现在提取产品数据比十年前容易多了。

但是仍然存在挑战，例如处理没有实现模式的网站，处理 IP 阻塞和速率限制，渲染 Javascript...

这就是为什么我们一直在和我的合作伙伴 Pierre 一起开发一个网络抓取 API

ScrapingBee 是一个 API，可以从任何网站提取任何 HTML，而无需处理代理、验证码和无头浏览器。一个 API 调用，其中只有您想要从中提取数据的产品 URL。

我希望你喜欢这篇文章，一如既往，你可以在 Github 资源库中找到完整的代码:[https://github.com/ksahin/introWebScraping](https://github.com/ksahin/introWebScraping)*