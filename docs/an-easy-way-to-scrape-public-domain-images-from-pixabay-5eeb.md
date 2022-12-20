# 从 Pixabay 中抓取公共域图像的简单方法

> 原文：<https://dev.to/cihankoseoglu_84/an-easy-way-to-scrape-public-domain-images-from-pixabay-5eeb>

最初出现在[learn think implementation](https://www.learnthinkimplement.com/pixabay-scraper/)中

最近，我正在进行一些 Python 开发。在我的一个个人项目中，我需要几百张图片，我需要它们在公共领域，这样我就不必处理任何版权问题。众所周知，Pixabay 有助于解决这种情况，但由于我需要大量的图片，我不能只手动挑选、选择和下载它们。所以我决定检查一下它的 [API](https://www.pixabay.com/api/docs) ，看看它是否容易刮图像，事实证明确实如此！

当你使用这些图片时，请注意以下要求。由于我将它们用于一个不对外开放的非商业个人项目，并且在我的本地机器上(也就是说，除了我之外，我没有任何用户)，所以我不包括图像的链接，但是获取它们也很容易。

> 如果你使用 API，无论何时显示搜索结果，都要向你的用户展示图片和视频来自哪里。需要一个到 Pixabay 的链接，您可以为此使用我们的徽标。作为免费使用 API 的回报，这是我们善意要求的一件事。

如果您查看文档，您会发现您将需要一个 API 密钥。这个非常容易做到，你只需要注册 Pixabay 就可以了。

下面是 API 的工作原理。我们有一个用于`GET`请求`https://pixabay.com/api/`的基本端点，我们只需将查询参数附加到这个端点，然后我们得到一个 JSON 响应，非常简单，非常有效。

完整的代码在最下面，所以如果你不想要这个演练，可以直接跳到那里

## 定义常量和变量

一旦您获得了 API 密钥，让我们添加我们想要的端点和参数。文档中有完整的参数列表，您可以将它们添加到`PARAMS`对象中。

query 是我放在这里的一个变量，因为我希望搜索查询是`istanbul`。我也会把它添加到我的`PARAMS`中。

我们将我们的`PARAMS`和`ENDPOINT`定义如下。

```
 API_KEY = "API-KEY"
URL_ENDPOINT = "https://pixabay.com/api/"
query = "istanbul"
PER_PAGE = 200

PARAMS = {'q': location, 'per_page': PER_PAGE, 'page': 1}
ENDPOINT = URL_ENDPOINT + "?key=" + API_KEY 
```

## 向 API 请求

我们需要向该端点发送一个 GET 请求，因为我们将导入并使用`requests`包。Requests 使得向端点发送请求和利用响应变得很容易。API 使用分页，因此我们将通过总点击数(来自响应)除以我们拥有的`PER_PAGE`常量来获得总页数。

一旦我们得到数据，我们将调用它的`.json()`方法来读取 JSON 格式的数据。你可以把 JSON 格式打印到你的控制台上，用美化器看看 JSON。

当我们这样做的时候，我们会看到我们有`hits`作为保存图像对象的对象。在每个对象中，我们有一个`webformatURL`键，它的值是指向图像文件的链接。在这个请求中，实际的图像没有返回给我们。我们需要收集这些图像 URL。为此，我们创建了一个名为`url_links`的新数组，并将链接添加到这个数组中。

```
url_links = []

req = requests.get(url=ENDPOINT, params=PARAMS)
data = req.json()

num_pages = (data["totalHits"] // PER_PAGE) + 1

for image in data["hits"]:
    url_links.append(image["webformatURL"]) 
```

## 这个分页呢？

当我们这样做时，我们将获得第一页，但我们仍然需要获得其他页面。既然我们现在知道了页数，让我们循环一下其他页面。为了不超过我们的查询限制，我们将使用`time.sleep()`来暂停执行。

```
for page in range(2, num_pages + 1):
    time.sleep(3)
    PARAMS['page'] = page
    req = requests.get(url=ENDPOINT, params=PARAMS)
    data = req.json()
    for image in data["hits"]:
        url_links.append(image["webformatURL"]) 
```

## 下载图片

一旦我们有了这些链接，我们需要向每个链接发出请求并下载它们。我们将再次使用`requests`包。

```
index = 0
for image in url_links:
    index += 1
    r = requests.get(image, allow_redirects=False)
    file_name = "istanbul_image_" + str(index)
    script_dir = os.path.dirname(__file__)
    rel_path = "img/" + file_name + ".jpg"
    abs_file_path = os.path.join(script_dir, rel_path)
    open(abs_file_path, 'wb').write(r.content) 
```

对于`url_links`中的每个图像链接，我们创建了一个对该图像的 GET 请求，我们创建了一个新文件(我将其命名为伊斯坦布尔图像 _${index})，但是您当然可以使用您想要的任何文件。我们导入了`os`包，以便轻松地访问我们的文件系统。我们获取 Python 可执行文件所在的路径，创建一个相对路径将图像放入一个文件夹，然后将图像的内容写入这个文件。

通过改变查询字符串(当然还有文件命名)，你几乎可以从 Pixabay 下载任何你想要的图像。这是全部代码。如果有任何问题，请通过 [@cihankoseoglu](https://dev.to/cihankoseoglu) 在 [Twitter](//www.twitter.com/cihankoseoglu) 上告诉我！

```
#!/usr/bin/env python3 
import requests
import time
import os

API_KEY = "API-KEY"
URL_ENDPOINT = "https://pixabay.com/api/"
query = "istanbul"
PER_PAGE = 200

PARAMS = {'q': query, 'per_page': PER_PAGE, 'page': 1}
ENDPOINT = URL_ENDPOINT + "?key=" + API_KEY

url_links = []

req = requests.get(url=ENDPOINT, params=PARAMS)
data = req.json()

num_pages = (data["totalHits"] // PER_PAGE) + 1

for image in data["hits"]:
    url_links.append(image["webformatURL"])

for page in range(2, num_pages + 1):
    time.sleep(3)
    PARAMS['page'] = page
    req = requests.get(url=ENDPOINT, params=PARAMS)
    data = req.json()
    for image in data["hits"]:
        url_links.append(image["webformatURL"])

index = 0
for image in url_links:
    index += 1
    r = requests.get(image, allow_redirects=False)
    file_name = "istanbul_image_" + str(index)
    script_dir = os.path.dirname(__file__)
    rel_path = "img/" + file_name + ".jpg"
    abs_file_path = os.path.join(script_dir, rel_path)
    open(abs_file_path, 'wb').write(r.content) 
```