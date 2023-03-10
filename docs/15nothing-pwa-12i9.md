# How did I develop Nothing PWA in 15 minutes

> 原文：<https://dev.to/henrylim96/15nothing-pwa-12i9>

> 英文版(英文版):[0 到 15 分钟——15 分钟内建造一个什么都没有的 PWA](https://dev.to/henrylim96/zero-to-15--building-a-nothing-pwa-in-15-minutes-258j)

## 什麼是什么都没有？

[![](img/68c15b650da8d0da6541f55e3bf817a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--es5rS71C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXZkZE7VkNnfQ5q18LpMBow.jpeg)

The original " [Nothing](https://play.google.com/store/apps/details?id=com.gorro.nothing) " Android application was developed by Chilango Lab. This app has been downloaded over one million times in Google Play Store, and has a high rating of 4.2 stars.

And this Android version of Nothing application actually doesn't have any function. But there is another very unique egg () in this application.

In other words, the Android version of Nothing application is 14MB in size. To make matters worse, it occupied 19.24MB after installation. This gave me a whim. What can I do to reduce the size of this application?

[![](img/5f347838d30a1b9594e78244ccd0bc4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bolv9Rtx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5jnvpxtc0e1jrp10a194.png) 
*(left) Nothing Android application: 19.24MB—(right) Nothing progressive web application: 205KB 【T5]*

The solution is very simple. I can use HTML, CSS, and JavaScript to reduce the size of the application. However, I can take it to a higher level, that is, upgrade it to progressive web application (PWA). In this way, users can use it without the Internet, and can still use it normally. At the same time, this PWA can be added to the homepage.

## 👩🏻‍🎨图标設計

Before I begin to discuss in depth, let me design a *beautiful* icon for this Nothing PWA.

The tool I used today is called [T0】 Launcher Icon Generator 【T1]. This is a [open source tool](https://github.com/romannurik/AndroidAssetStudio) developed by a Google employee named [T2】 Roman Nurik 【T3].

[![](img/ff26e28896700140ae06333a5c6fc761.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1HwOgmAs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1400/1%2AIlOsAMmxr7AUcq2K5maV9Q.png)

In a very short time, I used Launcher icon Generator to design a very beautiful icon (see the picture above).

In addition, this tool will automatically export icon to different sizes: 48px, 72px, 96px, 144px, 192px and 512px. This function provides great convenience.

## pwacompath

Ok, I have designed the icon, and the next step is to put the icon into the webpage.

First, I will create a new [T0】 Web App Manifest 【T1] file, and write the information of the icon, the name of the application, the theme color, and so on in the manifest.json file. In this case, the Android version of Chrome will automatically display the startup screen when the PWA is started.

But not every visitor supports the function of this startup screen. What can I do to bring this function to all the visitors?

Let me introduce [T0】 PWACompat 【T1], which is a tool library that can bring the functions of Web App Manifest to browsers that do not support Web App Manifest. In this way, it can bring the function of progressive web application (PWA) to more browsers.

This pwacopt is very easy to use. You only need to add the webappmanifest (`manifest.json`) and PWACompat (【T1]) to your code (see the following code).

```
<link rel="manifest" href="manifest.json" />
<script async src="https://cdn.jsdelivr.net/npm/pwacompat@2.0.6/pwacompat.min.js"
    integrity="sha384-GOaSLecPIMCJksN83HLuYf9FToOiQ2Df0+0ntv7ey8zjUHESXhthwvq9hXAZTifA"
    crossorigin="anonymous"></script> 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/6e8f9d924027753663c515b021721ac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uSssavUG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59vmpvldy9p74bt0wio7.gif) 
*Startup screen (iOS) 【T5] in Safari*

In this way, I successfully brought the PWA startup screen to the old browser (the above example is Safari for iOS). But this is not all the functions of PWACompat. PWACompat has more functions than you think.

Let me see what other functions pwacompat has:

—automatically add icon meta tag (and favicon) in `<head>` according to icon information in Manifest file.

—according to the `theme_color` attribute in the Manifest file, automatically add the meta tag of the theme color in `<head>`.

-Wait.

## added to the main screen

This is my favorite feature in progressive web application, that is, adding to the home screen (A2HS). However, after Chrome 68, [T0】 Chrome will display a smaller mini information bar to replace the previous larger installation banner :

[![](img/146bf47a2a59323399b84f839bbeb038.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ruMjH-d2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A3GwP3kbKBFSedyTaI4NWZQ.png) 
*Goodbye, web application installation banner (before Chrome 67)*

Nevertheless, this mini information bar is also a temporary solution, which will be removed in future Chrome updates. Therefore, I need to use a better UX added to the home screen.

[![](img/95e6de868147b6d868a73528f92e091f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zyX5s5uZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gv6errto96fbkd8blotl.png) 
*(left) Install button-(middle) Install button+A2HS mini information bar-(right) A2HS dialog box*

If the browser supports the function added to the home screen, this will display an "Install" button at the top of the webpage. When the user clicks the Install button, the A2HS dialog box will be displayed on the webpage (see the following code).

```
 var installPromptEvent;
var btnInstall = document.querySelector('#install');

window.addEventListener('beforeinstallprompt', function (event) {
    event.preventDefault();
    installPromptEvent = event;
    btnInstall.removeAttribute('disabled');
});

btnInstall.addEventListener('click', function () {
    btnInstall.setAttribute('disabled', '');
    installPromptEvent.prompt();
    installPromptEvent.userChoice.then((choice) => {
        if (choice.outcome === 'accepted') {
            console.log('User accepted the A2HS prompt');
        } else {
            console.log('User dismissed the A2HS prompt');
        }
        installPromptEvent = null;
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

*If the webpage meets the conditions of being added to the main screen, the visitor will launch the "beforeinstallprompt" event) 【T1].*

## Eggs

[![](img/85e4976e94aab787e43c7c6c6d737281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Davn8YwE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AePTthP8JcXDteH9p7G8gqQ.jpeg) 
*剧透预警！！！*

This is the egg in Nothing PWA: Konami Code+ [10 hours of YouTube video](https://twitter.com/Nick_Craver/status/720062942960623616) .

[![](img/d84ddda73cbe4bc05960e98243c9a3a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q0TS7wvZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2A9f-Coe9dfwz_UmlsTUmHjA.gif) 
*👆🏻👆🏻*

When the user has successfully completed the secret trick of Conami, this Nothing PWA will reprint the user to this very special [T0】 YouTube video .

In this Nothing PWA, I used this [konami-js](http://snaptortoise.github.io/konami-js/) . The reason why I choose to use this is because its documentation is very small and it is very easy to use.

Moreover, this Konami-JS also supports mobile phones. The only difference is that this Conami secret skill will change from "T0" to "T1".

## Pull to refresh

Just then, I found a small problem. If users are using mobile phones or touch screens, they will accidentally refresh to the web page when they slide down.

[![](img/cb1596f29b721104e6490ce979eda4ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fRJyjpZo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AcDXJ33JdnhzIUfzy8R_14g.gif)

How should I solve this problem?

The good news is that I can use `overscroll-behavior-y: contain` to avoid pulling and refreshing the webpage.

In this way, the problem is solved!

## Web hosting

This Nothing PWA is deployed on Netlify.

### What is Netlify?

Netlify is a front-end web hosting platform with complete functions. The reason why I chose to use Netlify this time is very simple, that is, Netlify is very easy to use, and Netlify is free to use!

[![](img/7ed92ce85238afeedb9aed7721317329.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vaTUyqZS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AxLFM3ifLVb5X63Nj7XZHUw.png) 
*Netlify 仪表盘*

If you want to deploy your web page to Netlify, you have three ways: using command-line tools, manual deployment, or continuous deployment.

#### Command line tool (CLI)

This is the most common way to deploy websites. Install the command-line tool of Netlify on your computer, log in to your Netlify, initialize the project, and then you can deploy your website to Netlify.

```
> brew tap netlify/netlifyctl 
> brew install netlifyctl
> netlifyctl login
> netlifyctl init
> netlifyctl deploy 
```

Enter fullscreen mode Exit fullscreen mode

#### Manual deployment

This method is the simplest way for Netlify to deploy web pages: Drag and Drop.

[![](img/bc714ab8986349f049a3a0ec3ca8803f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZPOP2jf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A8eB2dwalDXgHBHR4Pm83Iw.png)

In this way, developers only need to drag and drop the folder of web pages into Netlify's website. This feature is very cool, which makes me hope that Firebase can add this feature in Firebase Hosting.

#### 持續部署(持续部署)

Nothing PWA I use continuous deployment to automatically deploy web pages to Netlify.

When my GitHub project is connected to Netlify, whenever I push the code to GitHub, Netlify will automatically deploy the webpage to Netlify. This automatic deployment provides the function of automatic update, which is very convenient!

### 🔒HTTPS！

As one of the necessary conditions of progressive web application is from secure origin, I must ensure that HTTPS is correctly implemented in this web page.

The good news is that Netlify provides free HTTPS for everyone, which also includes custom domain names.

[![](img/434a0da2349e11c33298dfdf366fa8dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MTLZNWWq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Acu1Zs1B4ziZFkJVXj7lwTA.png)

Netlify also provides a great function, that is, HSTS ( [http strict transport security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) ). After startup, your webpage will be forcibly reprinted to HTTPS version. This can ensure that the web page is always running under HTTPS.

## 💡灯塔

[T0】 Lighthouse 【T1] is an open source automation tool, which is used to improve the quality of network applications. You can use Lighthouse to analyze the performance of web pages, SEO, and so on. You can study how to make your web page better according to the Lighthouse report.

[![](img/b6bf691dffd98a0cae73a0618974d06e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iUSeYkQX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A85AX9Hs1Tty316wkADoifQ.png)

Isn't it nice? I succeeded in getting 97 points in performance, and 100 out of 100 in PWA, Accessibility, Best Practices and SEO.

您可以在這裡查看完整的灯塔報告:[https://builder-dot-light house-ci . app spot . com/report . 1533954675085 . html](https://builder-dot-lighthouse-ci.appspot.com/report.1533954675085.html)

## Try it.

[![](img/0776d3b3f2132fc96c23f18cad92a8d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PN0KoqL1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AhA7rq9gWv2ea6UEIwMIDOw.gif)

In this way, I successfully converted a 19MB Android application into a progressive web application within 15 minutes, and reduced the application to 205KB!

🚀 您可以在這裡嘗試虚无 PWA: [虚无. limhenry.xyz](https://nothing.limhenry.xyz)

這艾滋病患者的代碼可以在开源代码库上找到:[github.com/limhenry/nothing](https://github.com/limhenry/nothing)