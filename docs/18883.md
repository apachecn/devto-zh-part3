# 卢卡斯——围棋界的网络爬虫

> 原文：<https://dev.to/dan_mcm_/lucas---a-webscraper-in-go-3jkc>

# 卢卡斯

Lucas 是一个使用 [Go](https://golang.org/) 和 [Colly](https://github.com/gocolly/colly) 库构建的网络爬虫。他也是 [YouTube](https://www.youtube.com/watch?v=VrS6akzR3sk) 上一只可爱的蜘蛛！

[![](img/1f9ded3b323efc506fb3d30ebd005f33.png)](https://i.giphy.com/media/26u4ltSAwnEb0Hpwk/giphy.gif)

## 为什么？

我想尝试一下 Go，因为它是我办公室的某些团队使用的编程语言，而不是我经常接触的东西。我觉得尝试解构一个随机的在线网站会很有趣，看看我是否能想出一种编程的方法，以一种可以被认为有用的方式来抽象 HTML 数据。

这个项目开始时是一个有趣的实验，但在进行到目前为止后，我决定停止工作，原因如下。

## 网页抓取的道德性

对很多人来说显而易见的是，从道德上讲，网络抓取是一个有点“灰色”的领域。根据我在这个问题上所做的所有研究和阅读，我通常将其归结为:

如果你没有违反公平使用条款，遵守公司的文件通知他们你这样做，那么你应该是清白的

对于这个复杂的问题，这是一个高度概括的陈述，但我发现这是一个很好的尝试遵循的准则。

当谈到电子商务时，在线网站可以保存一些高利润的数据，这些数据甚至不一定会出现在商店的前端，但他们仍然会让 JSON 在他们的静态页面内容中浮动。

电子商务商店可能会在其 HTML 中留下一些类型的信息，包括:

*   库存水平(如低库存标志或甚至确切的库存数量！)
*   尺寸可用性
*   定价/销售率

对于一般用户来说，这些数据可能不会被注意到，但是可以通过右键单击店面并选择“查看页面源”来访问这些数据。

## 结构化数据的“弊端”

当涉及到设计一个网页抓取器时，你要试着分析你的“目标”网站页面的结构。问自己这些问题:

*   是否有一种简单的模式可以用来访问特定产品的页面
*   是否存在跨多个页面的重复结构，以方便 html 解析

从更技术性的角度来看，请考虑以下几点:

*   网站经常更新 HTML 吗？
*   你能解释在你抓取网页时丢失的数据吗？
*   你将如何存储数据？DB 够快吗？
*   您计划处理多线程/分布式抓取吗？

## 铲运机如何工作

接下来是有趣的部分——代码！我将在这里泛泛而谈，因此所使用的实践可以应用于任何形式的电子商务商店。

### 数据存储

为了建立一种存储抓取的网站数据的方法，我决定使用 postgres DB，没有其他原因，只是因为我对它很熟悉，而且通过 docker-compose 文件很容易设置。

```
lucas:
  container_name: lucas
  image: postgres:9.6-alpine
  ports:
    - '5432:5432'
  environment:
    POSTGRES_DB: 'lucas_db'
    POSTGRES_USER: 'user' 
```

有了这个基本的 PSQL 数据库，我可以通过运行以下命令和以下输入文件
来设置一个基本表

```
psql -h localhost -U user lucas_db -f dbsetup.sql 
```

dbsetup.sql:

```
 \c lucas_db

CREATE TABLE floryday(
  index serial,
  product text,
  code text,
  description text,
  price decimal(53, 4)
) 
```

从表中可以看出，我决定从相关网页中搜集一些基本信息:

*   索引:这只是作为一个唯一的 id
*   产品:产品项目的名称
*   代码:相关产品的代码
*   描述:产品的描述文本
*   价格:产品的价格

如上所列，在您自己的经验中，您可能会对其他领域感兴趣，如规模和可用性。

### 去依赖

我在设置服务时有点粗心，因为我没有依赖 dep 之类的 Go 服务依赖管理工具，而是手动安装它们(因为只有 3 个依赖项，所以还不错)。这是我使用的三个主要的外部库，它们可以用命令`go get <repo-name>`安装:

*   [github.com/gocolly/colly](https://github.com/gocolly/colly)-网络抓取包
*   [github.com/fatih/color](https://github.com/fatih/color)-命令行颜色包
*   [github.com/lib/pq](https://github.com/lib/pq)-postgres 驱动程序包

为了使这个设置简单一点，我设置了一个 docker 文件来跟踪安装

```
FROM golang:1.11

MAINTAINER Daniel McMahon <daniel40392@gmail.com>

WORKDIR /opt/lucas

ADD . /opt/lucas

ENV PORT blergh

# installing our golang dependencies
RUN go get -u github.com/gocolly/colly && \
  go get -u github.com/fatih/color && \
  go get -u github.com/lib/pq

EXPOSE 8000

CMD go run lucas.go 
```

### 主逻辑

简而言之，主代码执行以下操作:

*   从种子 url 开始
*   扫描页面上的所有链接
*   查找与某个正则表达式匹配的页面，例如，我们从一些基本检查中知道这些页面都具有相似的页面结构，并且通常是我们感兴趣的产品页面
*   定义一个结构来表示我们感兴趣的服装值
*   将该结构写入 postgres 数据库
*   继续最多 200 次写入

下面是代码的主要部分和解释逻辑的注释——它没有经过优化，仍然很粗糙，但是它的关键功能已经到位:

卢卡斯. go

```
// as our scraper will only use one file this will be our main package
package main

// importing dependencies
import (
    "encoding/json"
    "log"
    "os"
    "fmt"
    "strings"
    "github.com/gocolly/colly"
    "github.com/fatih/color"
    "database/sql"
    _ "github.com/lib/pq"
    "strconv"
)

// setting up a datastruture to represent a form of Clothing
type Clothing struct {
    Name                    string
    Code                    string
    Description     string
    Price                   float64
}

// setting up a function to write to our db 
func dbWrite(product Clothing) {
    const (
      host     = "localhost"
      port     = 5432
      user     = "user"
      // password = ""
      dbname   = "lucas_db"
    )

    psqlInfo := fmt.Sprintf("host=%s port=%d user=%s "+
    "dbname=%s sslmode=disable",
    host, port, user, dbname)

    db, err := sql.Open("postgres", psqlInfo)
  if err != nil {
    panic(err)
  }
  defer db.Close()

  err = db.Ping()
  if err != nil {
    panic(err)
  }

  // some debug print logs
  log.Print("Successfully connected!")
    fmt.Printf("%s, %s, %s, %f", product.Name, product.Code, product.Description, product.Price)
    sqlStatement := `
    INSERT INTO floryday (product, code, description, price)
    VALUES ($1, $2, $3, $4)`
    _, err = db.Exec(sqlStatement, product.Name, product.Code, product.Description, product.Price)
    if err != nil {
      panic(err)
    }
}

// our main function - using a colly collector
func main() {
        // creating our new colly collector with a localised cache
    c := colly.NewCollector(
        // colly.AllowedDomains("https://www.clotheswebsite.com/"),
        colly.CacheDir(".floryday_cache"),
    // colly.MaxDepth(5), // keeping crawling limited for our initial experiments
  )

    // clothing detail scraping collector
    detailCollector := c.Clone()

        // setting our array of clothing to size 200
    clothes := make([]Clothing, 0, 200)

    // Find and visit all links
    c.OnHTML("a[href]", func(e *colly.HTMLElement) {

        link := e.Attr("href")

        // hardcoded urls to skip -> these arent relevant for products
        if !strings.HasPrefix(link, "/?country_code") || strings.Index(link, "/cart.php") > -1 ||
        strings.Index(link, "/login.php") > -1 || strings.Index(link, "/cart.php") > -1 ||
        strings.Index(link, "/account") > -1 || strings.Index(link, "/privacy-policy.html") > -1 {
            return
        }

        // scrape the page
        e.Request.Visit(link)
    })

    // printing visiting message for debug purposes
    c.OnRequest(func(r *colly.Request) {
        log.Println("Visiting", r.URL.String(), "\n")
    })

    // visting any href links -> this can be optimised later
    c.OnHTML(`a[href]`, func(e *colly.HTMLElement) {

        clothingURL := e.Request.AbsoluteURL(e.Attr("href"))

        // this was a way to determine the page was definitely a product
                // if it contained -Dress- we were good to scrape
        if strings.Contains(clothingURL, "-Dress-"){
            // Activate detailCollector
            color.Green("Crawling Link Validated -> Commencing Crawl for %s", clothingURL)
            detailCollector.Visit(clothingURL)
        } else {
            color.Red("Validation Failed -> Cancelling Crawl for %s", clothingURL)
            return
        }

    })

    // Extract details of the clothing
    detailCollector.OnHTML(`div[class=prod-right-in]`, func(e *colly.HTMLElement) {
        // some html parsing to get the exact values we want
        title := e.ChildText(".prod-name")
        code := strings.Split(e.ChildText(".prod-item-code"), "#")[1]
        stringPrice := strings.TrimPrefix(e.ChildText(".prod-price"),"€ ") 
        price, err := strconv.ParseFloat(stringPrice, 64) // conversion to float64
        color.Red("err in parsing price -> %s", err)
        description := e.ChildText(".grid-uniform")

        clothing := Clothing{
            Name:                   title,
            Code:                   code,
            Description:        description,
            Price:                  price,
        }

        // writing as we go to DB
        // TODO optiize to handle bulk array uploads instead of one at a time
        dbWrite(clothing)

        // appending to our output array...
        clothes = append(clothes, clothing)
    })

    // start scraping at our seed address
    c.Visit("https://www.ourclothingwebstore.com/Dresses-r9872/")

    enc := json.NewEncoder(os.Stdout)
    enc.SetIndent("", "  ")

    // Dump json to the standard output
    enc.Encode(clothes)

} 
```

## 危险地带

[![](img/f95d37027ccff69dbc3d7e9cd3fc03aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bk-StvvH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nerdist.com/wp-content/uploads/2017/05/Top-Gun-tom-Cruise.jpg)

在获得基本的功能设置和工作之后，我决定尝试使用多个产品种子页面。我发现这家商店在一个名为:[https://www.ourclothingwebstore.com/Dresses-r9872/](https://www.ourclothingwebstore.com/Dresses-r9872/)的页面上展示了它的主要产品，然而这可以在 url 的末尾用一个简单的/p2 或者/p3、/p4 直到 p80 什么的来分页！平均来说，每个页面上大约有 40 多种产品。我实现了一个简单的 for 循环，它迭代这个种子 url，每次都更新它。本质上，我可以用更多一点的逻辑设置爬虫来搜索这家商店出售的所有服装产品(类似地，我确信通过一个小的正则表达式调整，逻辑可以应用于零售商提供的其他时尚类别)。

正是在这一点上，我决定停止这个项目的工作，因为我已经实现了我设定的基本目标，并很容易地了解到这个无辜的项目可能会变得多么恶毒。它变成了一个单向停止到本质上有一个数据库，包含了这个电子商务网站的整个店面。

要实现这个目标，肯定需要进行优化，但我认为使用 goroutines，你可以对这个 scraper 进行一些并行化处理，从而加速这个过程，有可能在很短的时间内抓取整个网站。

## 关闭思绪

我尝试对网站的 HTML 结构进行逆向工程，弄清楚它们是如何显示产品页面的，以及如何抽象出要抓取的正确产品 hrefs 和能够获取并写入数据库的正确数据。这是令人愉快的，但这一切都感觉有点太接近太阳，我的法律喜欢。

出于匿名的考虑，我在这个例子中故意没有提到“真实”网站的名称，但基本原则应该适用于大多数主要的在线电子商务零售商。

我惊讶于使用 Colly 库是如此容易——我绝对建议进行测试，但要小心你决定抓取/存储的数据，并调查你的 targets robots.txt 文件以确保你有权访问他们的网站。

任何想法/观点/评论，欢迎在下面留下。

下次见！