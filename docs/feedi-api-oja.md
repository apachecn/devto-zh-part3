# feedrss api

> 原文：<https://dev.to/daviducolo/feedi-api-oja>

我刚刚实现了一个服务，允许将 RSS 提要转换成 REST API。看一看

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [大卫桑坦杰罗](https://github.com/davidesantangelo) / [ api.rss](https://github.com/davidesantangelo/api.rss)

### RSS 作为 RESTful。该服务允许您将 RSS 提要转换成一个很棒的 API。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/f3c30e772998a3f35805cb7485f3365b.png)](https://codeclimate.com/github/davidesantangelo/feedi/maintainability)[![](img/c1f5a6f01d2810a5f3381e34bb2439e0.png)](https://camo.githubusercontent.com/fc6a713168a81afb783b0a1dca706fb233284d2d6792676c37d9d3bd1a855d09/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f7461672f64617669646573616e74616e67656c6f2f66656564692e737667)[![License](img/2f2cc63c570cad8294f38a23b3c9a09f.png)](https://github.com/eonu/arx/blob/master/LICENSE)[![contributions welcome](img/415935da8031f15a94578a276d0ed929.png)](https://github.com/davidesantangelo/feedi/issues)[![Open Source Helpers](img/0d6c81f25f09e27e70be7c759d36829c.png)](https://www.codetriage.com/davidesantangelo/feedi)[![](img/59df111ef1bb1f9d37cfc530bb5cc5d0.png)](https://camo.githubusercontent.com/15eb0f1374460b02b81f078cbe3c24bb089a9c58d7c38bf0b49f790ed741b513/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c616e6775616765732f746f702f64617669646573616e74616e67656c6f2f66656564692e737667)

# API。RSS — <g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji> 这个项目不再维护了！！！！ <g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji>

### 由于这个项目已经不再维护，请考虑使用新项目[davideasntangelo/Dato . RSS](https://github.com/davidesantangelo/dato.rss)！

# API.RSS

你能找到的最好的 RESTful RSS 体验。

API。RSS 将 feed 数据变成了一个很棒的 API。该 API 简化了处理 RSS、Atom 或 JSON 提要的方式。您可以使用一个简单、快速且干净的 REST API 添加并跟踪您最喜欢的提要数据。所有条目都通过机器学习和语义引擎来丰富。

## 搜索引擎

看看 React 中围绕这个 API 开发的一个小搜索引擎。

欢迎对[的知识库](https://github.com/davidesantangelo/datorss)进行反馈。

## 例子

```
curl 'https://<domain>/search/entries?q=news' | json_pp
{
  "data": [
    {
      "id": "86b0f829-e300-4eef-82e1-82f34d03aff6",
      "type": "entry",
      "attributes": {
        "title": "\"Pandemic, Infodemic\"
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/davidesantangelo/api.rss)

```
 RestClient.get "https://api.feedirss.com/feeds", { Authorization: "Token <TOKEN>" } 
```

Enter fullscreen mode Exit fullscreen mode

```
 RestClient.get "https://api.feedirss.com/search/entries?q=dev.to", { Authorization: "Token <TOKEN>" } 
```

Enter fullscreen mode Exit fullscreen mode

```
{  "data":  [  {  "id":  "fe8cbc4d-fe19-48c0-9017-283ea9a12cca",  "type":  "entry",  "attributes":  {  "title":  "Thoughts on migrating to TypeScript and improving the overall quality of the frontend DEV codebase",  "url":  "https://dev.to/ben/thoughts-on-migrating-to-typescript-and-improving-the-overall-quality-of-the-frontend-dev-codebase-1121",  "published_at":  1555524852,  "body":  "\n\n<p>I think the evolution and increased popularity of TypeScript over the past year or so means that it is a good time to migrate towards using it on DEV.</p>\n\n<p>Nick Taylor has been the biggest proponent, but it all jives with my view of things.</p>\n\n<p>Read more here:</p>\n\n\n<div class=\"ltag__link\">\n <a href=\"/nickytonline\" class=\"ltag__link__link\">\n <div class=\"ltag__link__pic\">\n <img src=\"https://res.cloudinary.com/practicaldev/image/fetch/s--wsDCLFo5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--HhqKWFvW--/c_fill%2Cf_auto%2Cfl_progressive%2Ch_150%2Cq_auto%2Cw_150/https://thepracticaldev.s3.amazonaws.com/uploads/user/profile_image/9597/d1d224a1-9ad1-4bd4-90c3-57f88c6c9bf5.jpeg\" alt=\"nickytonline image\">\n </div></a>\n <a href=\"/nickytonline/dev-to-with-a-typescript-or-flow-frontend-codebase-1n33\" class=\"ltag__link__link\">\n <div class=\"ltag__link__content\">\n <h2>dev.to with a TypeScript or Flow frontend codebase?</h2>\n <h3>Nick Taylor</h3>\n <div class=\"ltag__link__taglist\">\n<span class=\"ltag__link__tag\">#meta</span><span class=\"ltag__link__tag\">#javascript</span><span class=\"ltag__link__tag\">#typescript</span><span class=\"ltag__link__tag\">#flow</span>\n</div>\n </div>\n </a>\n </div>\n\n\n<p>I did not want us to be too far on the bleeding edge of frontend technology and that continues to be my stance, but I think TypeScript is in a good place and Rails' support of \"modern\" JavaScript is likewise in a good place.</p>\n\n<p>As it currently stands, we have some of our JavaScript in the \"old\" <code>app/assets/javascripts</code> area of the codebase and some in the new <code>app/javascript</code> area which is configured via Webpacker.</p>\n\n<p>As mentioned by others, I don't think we need to wholely re-write in TypeScript, but we should begin refactoring and writing new features this way. And in general, we should definitely be migrating the oldest JavaScript into the new area to whatever extent possible.</p>\n\n<p>I think this initiative can pretty effectively be led by the community rather than top down from our team, because we won't need to provide a lot of specific instruction in order to make improvements in these areas.</p>\n\n<p>Any pull requests to provide good instructions for this approach in the README and/or docs would be appreciated to get the ball rolling on this.</p>\n\n<p>Part of the reason I feel like we can get moving on some of this now is that GitPod might be useful for helping frontend developers get up and running with the codebase and making changes without the worry of installing and getting the app running locally.</p>\n\n\n<div class=\"ltag__link\">\n <a href=\"/ben\" class=\"ltag__link__link\">\n <div class=\"ltag__link__pic\">\n <img src=\"https://res.cloudinary.com/practicaldev/image/fetch/s--RELHeEj1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--Sb1rTJg1--/c_fill%2Cf_auto%2Cfl_progressive%2Ch_150%2Cq_auto%2Cw_150/https://thepracticaldev.s3.amazonaws.com/uploads/user/profile_image/1/f451a206-11c8-4e3d-8936-143d0a7e65bb.png\" alt=\"ben image\">\n </div></a>\n <a href=\"/ben/spin-up-a-local-instance-of-dev-in-the-cloud-with-gitpod-it-s-incredibly-simple-pij\" class=\"ltag__link__link\">\n <div class=\"ltag__link__content\">\n <h2>Spin up a \"local\" instance of DEV in the cloud with GitPod (It's incredibly simple)</h2>\n <h3>Ben Halpern</h3>\n <div class=\"ltag__link__taglist\">\n<span class=\"ltag__link__tag\">#contributorswanted</span><span class=\"ltag__link__tag\">#gitpod</span><span class=\"ltag__link__tag\">#opensource</span><span class=\"ltag__link__tag\">#docker</span>\n</div>\n </div>\n </a>\n </div>\n\n\n<p>Only time will tell if GitPod leads to true productivity enhancements for frontend developers looking to contribute, but I think it's something worth exploring.</p>\n\n\n",  "text":  "I think the evolution and increased popularity of TypeScript over the past year or so means that it is a good time to migrate towards using it on DEV.\n\nNick Taylor has been the biggest proponent, but it all jives with my view of things.\n\nRead more here:\n\n\n\n  \n  \n  \n  \n  \n  \n dev.to with a TypeScript or Flow frontend codebase?\n Nick Taylor\n  \n#meta#javascript#typescript#flow\n\n  \n  \n  \n\n\nI did not want us to be too far on the bleeding edge of frontend technology and that continues to be my stance, but I think TypeScript is in a good place and Rails' support of \"modern\" JavaScript is likewise in a good place.\n\nAs it currently stands, we have some of our JavaScript in the \"old\" app/assets/javascripts area of the codebase and some in the new app/javascript area which is configured via Webpacker.\n\nAs mentioned by others, I don't think we need to wholely re-write in TypeScript, but we should begin refactoring and writing new features this way. And in general, we should definitely be migrating the oldest JavaScript into the new area to whatever extent possible.\n\nI think this initiative can pretty effectively be led by the community rather than top down from our team, because we won't need to provide a lot of specific instruction in order to make improvements in these areas.\n\nAny pull requests to provide good instructions for this approach in the README and/or docs would be appreciated to get the ball rolling on this.\n\nPart of the reason I feel like we can get moving on some of this now is that GitPod might be useful for helping frontend developers get up and running with the codebase and making changes without the worry of installing and getting the app running locally.\n\n\n\n  \n  \n  \n  \n  \n  \n Spin up a \"local\" instance of DEV in the cloud with GitPod (It's incredibly simple)\n Ben Halpern\n  \n#contributorswanted#gitpod#opensource#docker\n\n  \n  \n  \n\n\nOnly time will tell if GitPod leads to true productivity enhancements for frontend developers looking to contribute, but I think it's something worth exploring.",  "categories":  [],  "sentiment":  {  "type":  "positive",  "score":  0.6428571428571429  },  "tags":  [  "typescript",  "device file",  "bloodrock",  "codebase",  "javascript",  "bleeding edge technology",  "technology",  "ruby on rails",  "readme",  "application software",  "cloud computing",  "open-source software",  "docker (software)",  "only time will tell (song)",  "productivity software",  "front and back ends"  ]  },  "relationships":  {  "feed":  {  "data":  {  "id":  "9036d02d-51fb-43bf-9012-c22e67beea7b",  "type":  "feed"  }  }  }  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

PS。令牌将在 2 天后过期。您可以生成一个新的。看看医生。

还有很多需要改进的地方:)。随时给我提建议，帮我改进服务。如果你喜欢这个项目给我留一个星:d。