# 雨果离子模板

> 原文：<https://dev.to/codingcatdev/hugo-ionic-template-59jk>

> 原帖:[https://ajonp.com/lessons/hugo-ionic-template/](https://ajonp.com/lessons/hugo-ionic-template/)

[https://www.youtube.com/embed/tgXFNqInA0w](https://www.youtube.com/embed/tgXFNqInA0w)

> 请注意，在学习这一课的时候，我选择制作分支而不是标签，看看人们是否会更喜欢。然而，有一个更新的维克多/雨果模板开始使用网络包，我不想混淆任何人，所以请不要拉主分支开始。
> 
> *   主(首字母)
> *   netlify-victor-hugo(将所有内容移至 npm 设置)
> *   完成-第 4 课(具备上线所需的一切)
> *   prod(展示了示例触发器，以及 git 子模块)

## 正式文件

[谷歌云构建-部署工件](https://cloud.google.com/cloud-build/docs/configuring-builds/build-test-deploy-artifacts#deploying_artifacts)

## 上课步骤

1.  ~~克隆第 4 课~~
2.  将 Netlify Victor/Hugo 模板添加到项目中。
3.  更新品牌。
4.  配置 Firebase 托管
5.  配置 Algolia 索引
6.  配置 Google 云构建

# 将 Netlify Victor/Hugo 模板添加到项目

## 得到初始设置

您可以在[初始设置课程](https://ajonp.com/lessons/4-hugo-ionic)和 [YouTube 视频](https://www.youtube.com/watch?v=CZmEZ62yMFA)中一步一步地学习。

或者你可以从克隆开始[第四课雨果爱奥尼亚](https://github.com/AJONPLLC/lesson-4-hugo-ionic)，我们使用一个特定的分支来开始这节课，就像原版课的主一样。

```
git clone --single-branch -b netlify-victor-hugo https://github.com/AJONPLLC/lesson-4-hugo-ionic.git 
```

Enter fullscreen mode Exit fullscreen mode

## 结构

```
|--site                // Everything in here will be built with hugo
|  |--content          // Pages and collections - ask if you need extra pages
|  |--data             // YAML data files with any data for use in examples
|  |--layouts          // This is where all templates go
|  |  |--partials      // This is where includes live
|  |  |--index.html    // The index page
|  |--static           // Files in here ends up in the public folder
|--src                 // Files that will pass through the asset pipeline
|  |--css              // Webpack will bundle imported css seperately
|  |--index.js         // index.js is the webpack entry for your css & js assets 
```

Enter fullscreen mode Exit fullscreen mode

## 奔跑的雨果

此时，通过在站点文件夹中运行命令，您仍然可以将该项目作为普通 hugo 站点运行。

```
cd site/ && hugo serve 
```

Enter fullscreen mode Exit fullscreen mode

> 确保返回到您的根文件夹`cd ..`

## 奔跑的维克多/雨果

在这一点上，我们需要安装一些额外的要求。

通过 npm 安装文件 npm 自带[NodeJs](https://nodejs.org/en/download/)

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

启动浏览器同步服务器

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似这样的内容:

```
[13:29:18] Starting 'server'...
[Browsersync] Access URLs:
 --------------------------------------
       Local: http://localhost:3000
    External: http://192.168.86.23:3000
 --------------------------------------
          UI: http://localhost:3001
 UI External: http://localhost:3001
 --------------------------------------
[Browsersync] Serving files from: ./dist
[13:29:34] Starting 'hugo'...
[ '-d', '../dist', '-s', 'site', '-v' ] 
```

Enter fullscreen mode Exit fullscreen mode

## 更新使用维克多/雨果资产管道

这变得如此强大的原因是，您可以开始安装所有您喜欢的 npm 模块，并像导入大多数非静态项目一样导入它们。

将 baseof.html 文件从我们的主题复制到我们的基本目录，这样我们就可以覆盖主题文件。

```
mkdir site/layouts && mkdir site/layouts/_default &&  cp site/themes/ajonp-hugo-ionic/layouts/_default/baseof.html site/layouts/_default/baseof.html 
```

Enter fullscreen mode Exit fullscreen mode

> 记住在你的文件中编辑 baseof.html，而不是主题(否则它们不会被保存。)

现在我们要编辑 baseof.html 并删除这两行(就在`</head>` )

```
<link href="{{ "/css/custom.css" | absURL }}" rel="stylesheet" as="style" type="text/css"> 
<link href="{{ "/css/syntax.css" | absURL }}" rel="stylesheet" as="style" type="text/css"> 
```

Enter fullscreen mode Exit fullscreen mode

这些最初来自我们的网站文件夹，无论是主题还是直接，我们将更新这个文件，以允许维克多/雨果来处理这一要求。所以我们将用
替换 html 中的那一行

```
<link rel="stylesheet" href="css/main.css"> 
```

Enter fullscreen mode Exit fullscreen mode

另外，当我们在/site/layouts _ default/base of . html 中时，在`</body>`的正上方添加一个引用。

```
<script src="app.js"></script>

You should now see the theme change back to the stock Ionic Blue color, as we have not moved our styles to the source location.
However there will be a new addition to the console in debugger. 
```

Enter fullscreen mode Exit fullscreen mode

同意😺 [AJonP 的 Youtube 频道](https://www.youtube.com/channel/UCnKZ8gEb78zXKMi1ns-IQ2g)

```
Move our custom.css file to the source folder. 
```

Enter fullscreen mode Exit fullscreen mode

sh
mv site/static/CSS/custom . CSS src/CSS/imports/custom . CSS

```
Now we need to import this file into our src/css/main.css 
```

Enter fullscreen mode Exit fullscreen mode

css
/*
可以使用 import 语句包含部分:
*/
@ import " imports/reset . CSS "；
@ import " imports/custom . CSS "；

/*
或者在这里添加你的声明:
*/
body {
font-family:sans-serif；
font-size:1em；
文本对齐:居中；
}

```
## Example of loading software to use
Lets try adding something simple that we might use like lodash 
```

Enter fullscreen mode Exit fullscreen mode

sh
npm 安装 lodash

```
 Now this will be avalabel for us to use in our app.js or any other file we would like to reference the module.

src/js/app.js 
```

Enter fullscreen mode Exit fullscreen mode

js
console . log(`Subscribe to 😺 <a href="https://www.youtube.com/channel/UCnKZ8gEb78zXKMi1ns-IQ2g">AJonP's Youtube Channel</a>`)；

var _ = require(' lodash ')；
console.log(_。版)；

```
In the console you will now see the current version of lodash.

# Update branding

I am sure that you love looking at AJ on your favicon and manifest (he likes being there 😺). But you probably want to update a few things.

## icons

In the `themes/ajonp-hugo-ionic/static/icons` folder you will find several images that can be used for your manifest file. I typically use [www.favicon-generator.org](https://www.favicon-generator.org/) to update these files based on an image that I create. You can now add all of these images to `site/static/icons` so they can be referenced later in manifest.json or anywhere in your site.

## manifest.json

You should update this file to reflect any changes in your icons folder. 
```

Enter fullscreen mode Exit fullscreen mode

json
{
"name": "AJonP "，
"manifest_version": 1，
"short_name": "AJonP "，
"start_url": "/"，
"scope": "/"，
"display": "standalone "，
"background_color": "#FFFFFF "，
"theme_color": "#3D2D4D "，
" GCM _ sender _ id ":" 100...

```
 ## Favicons

If you are using an older browser the main icon that goes in your browser will be used by `/site/static/favicon.ico`. Otherwise you should hvae larger images spelled out in the `themes/ajonp-hugo-ionic/layouts/partials/head.html`, they look similar to this... 
```

Enter fullscreen mode Exit fullscreen mode

html【rel = " icon】
type = " image/png "
sizes = " 32 x32 "
href = "/icons/favicon-32x 32 . jpg "
/>rel = " icon "
type = " image/png "

```
 Please feel free to update the entire head.html partial, or any of the layouts for that matter AJ doesn't mind too much 😼.

# Configure Firebase Hosting

Please make sure to checkout [Google Cloud Firebase CI/CD](https://ajonp/lessons/2-firebase-ci) for a great example.

## Quick steps 
```

Enter fullscreen mode Exit fullscreen mode

sh
火灾基地初始化

```
1\. Select Hosting  
![Firebase Hosting](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544643924/ajonp-ajonp-com/4-lesson-hugo-ionic/xujgloub1npwpgvlccbx.jpg)

1\. Pick your project  
![](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544644014/ajonp-ajonp-com/4-lesson-hugo-ionic/tlxmijckkvljjqoxc1wc.jpg)

1\. Set the output folder to dist, and select no for rewrite all urls.  
![](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544644082/ajonp-ajonp-com/4-lesson-hugo-ionic/q6k1sok8avnqqesglkmq.jpg)

# Configure Algolia Indexing
First signup on [www.algolia.com/](https://www.algolia.com/).

## Algolia Signup
1\. About You!  
[Algolia Signup about](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544652567/ajonp-ajonp-com/4-lesson-hugo-ionic/oal4d7yzzwkxdovyoxov.jpg)

1\. Your Datacenter!  
[Algolia Your Data Center](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544652629/ajonp-ajonp-com/4-lesson-hugo-ionic/ohsotz84xgeeodrunlkr.jpg)

1\. Your Project Type - Depends on what you are making but I would choose Media for most sites.  
![Algolia Your Project](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544652688/ajonp-ajonp-com/4-lesson-hugo-ionic/ozjk0vuaneigikbl8oo0.jpg)

1\. Pick Dashboard once you get here  
![Algolia Dashboard](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544652806/ajonp-ajonp-com/4-lesson-hugo-ionic/fkzflzgad0cgqovz11r4.jpg)

1\. You can run the tutorial or just skip for now.

1\. Now select Indicies, and Create Index!  
[Indicies](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544652891/ajonp-ajonp-com/4-lesson-hugo-ionic/hswlxadds5sens0rb0s5.jpg)

## Create Index

1\. Example of an Index Creation  
![Example Index](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544652920/ajonp-ajonp-com/4-lesson-hugo-ionic/jrklg9odo7to6mwdxhks.jpg)

1\. API Keys are where we can now get the remainder of our information that we will need.![](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544653048/ajonp-ajonp-com/4-lesson-hugo-ionic/oqpgcjirolsqwkdkw3ov.jpg)

## Add Algolia for Search

> In your site/config.toml file you will see params.algolia. These are used just for the search bar to make the search happen...do not use your Admin API KEY!! 
```

Enter fullscreen mode Exit fullscreen mode

toml
# Search Only
【params . algolia】
appId = " c 1oj 9 hoh 3 e "
API key = " f 61 B2 BF 395516 ca 150 fc 7b 75281190 ab "
index name = " example "

```
 You will notice a section later when we use Cloud Depoy that will require you to add

* ALGOLIA_APP_ID
* ALGOLIA_ADMIN_KEY
* ALGOLIA_INDEX_NAME
* ALGOLIA_INDEX_FILE

# Configure Google Cloud Build
An important part to any project is setting up a solid CI/CD pipeline (Continuous Integration/Continuous Delivery). I still believe that Google Cloud offers the best pricing for any size development team! If you are a very small shop you will run builds for free, for a very long time.
![Google Cloud Pricing](https://res.cloudinary.com/ajonp/image/upload/f_auto,fl_lossy,q_auto/v1544644850/ajonp-ajonp-com/4-lesson-hugo-ionic/xwfyo6ysbxykebpf4wzo.jpg)

## Docker Images / Cloud Build

> I found out recently from [Mike McDonald](https://twitter.com/asciimike), that there are a great set of [Google Cloud Platform Community Images](https://github.com/GoogleCloudPlatform/cloud-builders-community). They should work really well most of the time, but you might have to be careful on versioning. For instance Hugo is currently at .49 and I need something above .50 for my site. This is why I remain using my own Docker files.

## cloudbuild.yaml 

This file controls all of the steps necessary throughout our build process. You can manually execute each step locally to see that everything works as expected on your docker images. 
```

Enter fullscreen mode Exit fullscreen mode

yaml
步骤:

# 打造雨果形象

*   name:' gcr . io/cloud-builders/docker ' args:[' build '，'-t '，' gcr.io/$PROJECT_ID/hugo '，'。/dockerfiles/hugo' ] # Git 子模块，运行 npm install，hugo build
*   name:' gcr . io/$ PROJECT _ ID/Hugo ' args:[' bash '，'。/deploy.sh'] # Algolia 索引更新
*   name:' gcr . io/cloud-builders/NPM '参数:[ 'run '，' algolia'] env:
    *   ALGOLIA _ APP _ ID = $ { _ ALGOLIA _ APP _ ID } '
    *   ALGOLIA _ ADMIN _ KEY = $ { _ ALGOLIA _ ADMIN _ KEY } '
    *   ALGOLIA _ INDEX _ NAME = $ { _ ALGOLIA _ INDEX _ NAME } '
    *   ALGOLIA _ INDEX _ FILE = $ { _ ALGOLIA _ INDEX _ FILE } ' # SW-pre cache
*   name:' gcr . io/cloud-builders/NPM '参数:[ 'run '，' generate-service-worker'] #构建 firebase 映像
*   name:' gcr . io/cloud-builders/docker ' args:[' build '，'-t '，' gcr.io/$PROJECT_ID/firebase '，'。/dockerfiles/firebase' ] #部署到 firebase
*   name:' gcr . io/$ PROJECT _ ID/FIREBASE ' args:[' deploy '，'- token '，' ${_FIREBASE_TOKEN}'] #您可以选择保留构建映像# images:[' gcr . io/$ PROJECT _ ID/Hugo '，' gcr.io/$PROJECT_ID/firebase']

```
 Please checkout all of the [cloud builder images](https://cloud.google.com/cloud-build/docs/cloud-builders).

### Step 0 and 1
 Docker is an available image we will use this to create our own image instance from our Dockerfile in dockerfiles/hugo/Dockerfile. The Google Cloud build image Node seems to offer a lot of the tools we will need already so we just add Hugo into this Dockerfile. 
```

Enter fullscreen mode Exit fullscreen mode

docker file
FROM node
LABEL author = "[developer@ajonp.com](mailto:developer@ajonp.com)

# 设置 debconf 以非交互方式运行

运行 echo ' debconf debconf/frontend select non interactive ' | debconf-set-selections

# 安装基础相关性

运行 apt-get update & & apt-get upgrade-y
运行 apt-get install-y-q-no-install-recommendes \
apt-transport-https \
asciidoc \
build-essential \
ca-certificates \
curl \
git \
libssl-dev \
python \
python-pygments \
rsync \
software-properties-common \
dev scripts \

# 下载安装雨果

ENV HUGO _ VERSION 0.51
ENV HUGO _ BINARY HUGO _ $ { HUGO _ VERSION } _ Linux-64 bit . deb

# 添加[https://github . com/sp f13/HUGO/releases/download/v $ { HUGO _ VERSION }/$ { HUGO _ BINARY }](https://github.com/spf13/hugo/releases/download/v%24%7BHUGO_VERSION%7D/%24%7BHUGO_BINARY%7D)/tmp/HUGO . deb

运行 curl-sL-o/tmp/HUGO . deb \
[https://github . com/sp f13/HUGO/releases/download/v $ { HUGO _ VERSION }/$ { HUGO _ BINARY }](https://github.com/spf13/hugo/releases/download/v%24%7BHUGO_VERSION%7D/%24%7BHUGO_BINARY%7D)&&\
dpkg-I/tmp/HUGO . deb&&\
RM/tmp/HUGO . deb

# 确认雨果

运行 hugo env

```
 The next step in this will execute our local file `deploy.sh` so that we can execute a few key commands.
The purests out there will say that we can execute both the git command and the npm commands using the two external images. I find that it is faster to not spin up a new image since we are sitting in a node image already for npm. The git submodule also is a bit of a pain because you have to go through a complicated process (from what I have gathered) to either link github to google source repos or create tokens for one off logins. 
```

Enter fullscreen mode Exit fullscreen mode

嘘

# [T1】！/bin/bash](#binbash)

echo-e " \ 033[0；32 添加子模块...\ 033[0m]

git 子模块初始化
git 子模块更新-递归-远程

echo-e " \ 033[0；32m 通过 npm 安装...\ 033[0m]

npm 安装

echo-e " \ 033[0；32m 通过国家预防机制建设...\ 033[0m]

npm 运行构建

```
 1\. Update any submodules (like our theme).
1\. Install all of the victor/hugo dependencies (or all other).
1\. Run the build command which will push all of our files to the `/dist` folder.

### Step 2
Now I know everything I said up above about npm image...sorry for any confusion. However, I thought it was better to leave these npm commands seperate from the deploy as they are not required and can be easily commented out of your builds.

In the case of this evn variables, we can again leave these out of the public git repos of the world and only have them stored wihtin Google Cloud builder [variable values](https://cloud.google.com/cloud-build/docs/configuring-builds/substitute-variable-values), using the notation `${_variable}`. 
```

Enter fullscreen mode Exit fullscreen mode

亚 ml

# 阿尔戈利亚指数更新

*   name:' gcr . io/cloud-builders/NPM '参数:[ 'run '，' algolia'] env:
    *   ALGOLIA _ APP _ ID = $ { _ ALGOLIA _ APP _ ID } '
    *   ALGOLIA _ ADMIN _ KEY = $ { _ ALGOLIA _ ADMIN _ KEY } '
    *   ALGOLIA _ INDEX _ NAME = $ { _ ALGOLIA _ INDEX _ NAME } '
    *   ALGOLIA _ INDEX _ FILE = $ { _ ALGOLIA _ INDEX _ FILE } '

```
 This is again using `atomic-algolia` to look at our newly created `dist/algolia.json` file and compares that to what is in the Algolia index.

### Step 3

Want a PWA still? Then we need to have a service worker. No AJ not an Animal service worker. 🐱 
```

Enter fullscreen mode Exit fullscreen mode

亚 ml

# SW-预缓存

*   name:' gcr . io/cloud-builders/NPM ' args:[' run '，' generate-service-worker']

```
 ### Step 4 and 5

> Please make sure to checkout [Google Cloud Firebase CI/CD](https://ajonp/lessons/2-firebase-ci) for a great example.

The biggest item that you need to remember here is adding your token to the cloud builder.'

Make sure to checkout [Firebase Administrative Commands](https://firebase.google.com/docs/cli/#administrative_commands) 
```

Enter fullscreen mode Exit fullscreen mode

sh
firebase 登录:ci

Enter fullscreen mode Exit fullscreen mode

雅姆

# 构建 firebase 映像

*   name:' gcr . io/cloud-builders/docker ' args:[' build '，'-t '，' gcr.io/$PROJECT_ID/firebase '，'。/dockerfiles/firebase' ] #部署到 firebase
*   name:' gcr . io/$ PROJECT _ ID/FIREBASE '参数:['deploy '，'- token '，' ${_FIREBASE_TOKEN}']

```
 Now you should have your full [Hugo](https://gohugo.io/) site up and running on [Firebase Hosting](https://firebase.google.com/products/hosting/)! 
```

Enter fullscreen mode Exit fullscreen mode