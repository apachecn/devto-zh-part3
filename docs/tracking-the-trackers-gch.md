# 追踪追踪者

> 原文：<https://dev.to/siscia_/tracking-the-trackers-gch>

这个项目想探究网络是如何被谁跟踪的。

大多数精通技术的读者都知道，当我们访问一个网页时，后台会发生一些事情。

来自服务器的页面被发送到用户的浏览器，用户开始在屏幕上绘制内容。但是，浏览器可能需要访问其他资源，最常见的是:

*   形象
*   说明如何设计几个元素的样式(如:颜色，尺寸，文本的位置)，称为 CSS
*   动画或智能应用程序的代码，称为 JS
*   字体(文本的显示方式)

所有这些资源可能由同一个网站提供，也可能由不同的网站提供。

如果这些资源是由不同的网站提供的，浏览器需要通过向不同的参与者发出请求来获取它们。

所有这些请求都可能被用来跟踪网络上的用户，特别是如果它们与 cookies(因此每个网站上都有烦人的横幅)和标题(来自那些我们没有横幅的网站)相关联。

这方面的一个例子是脸书，Twitter，谷歌，Reddit 等的社交按钮。为了显示这些按钮，有必要向相应的公司提出请求，并发送有关用户的信息。这样就有可能显示非常社交化的按钮，比如(“琼恩、提利昂和珊莎喜欢这个元素”)，但这些社交平台会知道你访问了哪个页面。

最后，网站还可以使用分析解决方案，帮助网站了解谁访问了他们的网站，哪些页面访问得更频繁，以及其他信息。最常见的分析解决方案是由谷歌自己免费提供的，当然网站获得了许多有用的数据，但谷歌也获得了同样的数据。

有了这些基本知识，让我们来探索如何知道谁在跟踪网络。

## 获取数据

要知道对什么服务发出了什么请求，最简单的方法就是使用 Firefox 之类的浏览器来呈现网页，并跟踪所有的请求。

这个过程并不像看起来那么简单，可能由于朋友的帮助，一个合理简单的解决方案成为可能。

> Chrome headless 和 selenium 可能也有帮助
> 
> —拉米罗·阿尔戈齐诺(@拉尔戈齐诺)[2019 年 5 月 14 日](https://twitter.com/ralgozino/status/1128403031702294529?ref_src=twsrc%5Etfw)

以编程方式获取浏览器显示网页所需的所有请求的列表有多难？

我们以编程方式驱动 Firefox 通过代理发出所有请求。

在硒线项目中，一切都被很好地打包在一起。

结果是一个很小的 python 脚本，它输入一个域，启动 Firefox，让 Firefox 访问并呈现主页，通过代理跟踪所有请求，最后将所有请求存储到 SQLite 文件中。

```
import sys                                                                                                                                                              

from seleniumwire import webdriver  # Import from seleniumwire                                                                                                          
from selenium.webdriver.firefox.options import Options                                                                                                                  

import tldextract                                                                                                                                                       
from urllib.parse import urlparse                                                                                                                                       

import sqlite3                                                                                                                                                          
import json                                                                                                                                                             

options = Options()                                                                                                                                                     
options.headless = True                                                                                                                                                 
# Create a new instance of the Firefox driver                                                                                                                           
driver = webdriver.Firefox(options=options)                                                                                                                             

original_domain = sys.argv[1]                                                                                                                                           
url = 'https://{}'.format(original_domain)                                                                                                                              
# Make a request to the URL
driver.get(url)                                                                                                                                                         

conn = sqlite3.connect("requests.db")                                                                                                                                   
c = conn.cursor()                                                                                                                                                       

c.execute('''                                                                                                                                                           
    CREATE TABLE IF NOT EXISTS requests(                                                                                                                                
        original_domain TEXT NOT NULL,                                                                                                                                  
        original_url TEXT NOT NULL,                                                                                                                                     
        time_request INT DEFAULT (strftime('%s','now')),                                                                                                                
        request TEXT,                                                                                                                                                   
        status_code INT,                                                                                                                                                
        subdomain TEXT,                                                                                                                                                 
        domain TEXT,                                                                                                                                                    
        tld TEXT,                                                                                                                                                       
        scheme TEXT,                                                                                                                                                    
        netloc TEXT,                                                                                                                                                    
        path TEXT,                                                                                                                                                      
        params TEXT,                                                                                                                                                    
        query TEXT,                                                                                                                                                     
        fragment TEXT,                                                                                                                                                  
        request_header TEXT,                                                                                                                                            
        response_header TEXT                                                                                                                                            
    );                                                                                                                                                                  
''')                                                                                                                                                                    
conn.commit()                                                                                                                                                           
insert_stmt = """
INSERT INTO requests(
        original_domain,
        original_url,
        request,
        status_code,
        subdomain,
        domain,
        tld,
        scheme,
        netloc,
        path,
        params,
        query,
        fragment,
        request_header,
        response_header
)
VALUES(?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, json(?), json(?));
"""

# Access requests via the `requests` attribute
for request in driver.requests:
    if request.response:
        rpath = request.path
        subdomain, domain, tld = tldextract.extract(rpath)
        parsedRequest = urlparse(rpath)
        scheme, netloc, path, params, query, fragment = parsedRequest
        status_code = request.response.status_code
        data = (
            original_domain,
            url,
            rpath,
            request.response.status_code,
            subdomain,
            domain,
            tld,
            scheme,
            netloc,
            path,
            params,
            query,
            fragment,
            json.dumps(dict(request.headers)),
            json.dumps(dict(request.response.headers)),
        )

        c.execute(insert_stmt, data);

conn.commit()

driver.close()
driver.quit() 
```

此时，我们有了一个脚本，它在输入中给定一个域，获取它的主页，并将呈现该主页所需的所有请求存储到一个小数据库中。

然后我们用[前 1000 万最具影响力的](https://www.domcop.com/top-10-million-domains)网站(实际上是域名)的列表来了解哪些网站访问量最大。

我们处理域列表以获得前几百个域:

```
cat top10milliondomains.csv | awk -F "," '{ print substr($2, 2, length($2) - 2)}' | head -n 1000 
```

最后，我们使用`xargs`来并行运行 python 脚本。

```
xargs -n1 -P6 python3 tracker.py 
```

因此，整个指挥命令是:

```
cat top10milliondomains.csv | awk -F "," '{ print substr($2, 2, length($2) - 2)}' | head -n 1000 | xargs -n1 -P6 python3 tracker.py 
```

几个小时后，我们收集了 186582 个请求，同时呈现了 1924 个域名的主页。这些请求针对 3472 个域。

请求的数量肯定不是很大，远非如此，但为了做到这一点，Firefox 需要渲染整个网页以及 JS 和 CSS，这绝对不是一个轻量级的任务。

一份简短的数据分析很快就会出来，请在 twitter 上关注我，或者订阅邮件列表来接收更新。

[此处储存库](https://github.com/siscia/TrackingTheTrackers)