# 🤓💻在大约 20 分钟内创建一个很酷的 Reddit web 应用程序！

> 原文：<https://dev.to/nour_sofanati/-create-a-cool-reddit-web-app-in-20-minutes-1dgh>

# 我们在做什么？🤓

在本帖中，我们将使用 Reddit 创建一个美丽的风景画廊🏔。

画廊将会是这个样子。

[![Picture of the gallery](img/e1edc2ecb309ec02191edc48daeea956.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iLTbenLD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39n4jvp86glckziqxk93.jpg)

每张照片都会导航到子编辑中相应的帖子。

值得注意的是，我还在 YouTube 上制作了一个视频，你可以在这里观看。

## 入门👩‍💻

好了，要开始，你需要创建这三个文件:

*   index.HTML
*   风格。半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   索引. js

创建一个新的 UL 元素，并链接 HTML 中的 CSS 和 JavaScript，如下所示:

```
<!DOCTYPE html>
<html>

<head>
    <link rel="stylesheet" href="style.css">
    /r/EarthPorn
</head>

<body>
    <ul></ul>
    <script src="index.js"></script>
</body>

</html> 
```

* * *

## JavaScript-ing📜

转到 Javascript 文件，创建一个带有 subreddit URL +'的变量。我们将为 thisو使用/r/earthporn，你可以继续使用你喜欢的任何东西。

```
const request_URL = 'https://www.reddit.com/r/earthporn.json'; 
```

之后，在我们的 JavaScript 中缓存我们的 UL HTML 元素，这样我们就可以把图片附加到它上面

```
const imageList = document.querySelector('ul'); 
```

### 取数据！

我们将使用 fetch()
从我们的 request_URL 获取数据。你可以从这个链接了解更多关于 [fetch 的信息。
当我们检索数据时，我们将把它附加到 UL 元素](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 

```
 //Start the fetching request with request_URL
fetch(request_URL)
  .then(data => {
    return data.json(); //Returning the data blob to the fetch request so we can extract the JSON from it.
  }).then(posts => {
    posts.data.children.forEach(post => {

      //Defining 'PostObject'
      let postObject = {
        imgurl: post.data.preview.images[0].resolutions[3].url, //Parsing the resultuion of the images
        imgtitle: post.data.title, //Parsing the title.
        upvotes: post.data.ups, //Parsing the upvotes
        posturl: 'https://www.reddit.com' + post.data.permalink //Parsing the post URL
      }

      //Adding a new element to the UL html element.
      imageList.innerHTML += addNewImage(postObject);
    })
  });

function addNewImage(postObject) {
  return `
  <li>
    <p>${postObject.imgtitle}</p>
    <img src="${postObject.imgurl}">
    <h5>🔼${postObject.upvotes}</h5>
    <a target="_blank" href="${postObject.posturl}">🔗</a>
  </li>
  `;
} 
```

就这样，JavaScript 完成了！

* * *

## 造型🎨🖌

我们差不多完成了，只剩下造型了！

```
body {
    margin: 0; //Remove any margin from the body element
    font-family: 'Segoe UI'; //Change the font family
}

ul {
    margin: 3px 0;
    padding: 0;
    width: 100%;
    list-style: none;
    display: grid; Display as a grid with three columns
    grid-template-columns: repeat(3, 1fr);
    row-gap: 10px;
    column-gap: 10px;
}

li {
    position: relative;
    height: 30vh;
    opacity: .5;
}

img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 7px;
}

li p,
li h5,
li a {
    position: absolute;
    margin: 0;
    padding: 0;
    opacity: 0;
    transition: all 100ms ease-in-out;
}

li:hover,li:hover *{
    opacity: 1;
}
li p{
    top :10px;
    left :10px;
    color: #fff;
    text-shadow: 0 0 5px black; 
    font-weight: 500
}

li h5{
    background: #fff;
    font-weight: 100;
    bottom: 10px;
    left: 10px;
    font-size: 1.3rem;
    padding: 3px;
}
li a{
    bottom: 10px;
    right: 10px;
    font-size: 1.3rem;
    padding: 3px;
    text-decoration: none;
} 
```

就这样，你完成了！

* * *

我很期待看到你们能在此基础上做些什么！我希望看到你们中的一些人对 [GitHub 库](https://github.com/NourSofanati/redditgallery)提出拉取请求，也许你们中的一些人会让它对屏幕尺寸有更多的反应？

也请告诉我你对这类帖子的看法，很高兴听到你的反馈！