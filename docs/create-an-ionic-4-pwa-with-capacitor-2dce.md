# 创建一个带电容器的离子 4 PWA

> 原文：<https://dev.to/jscrambler/create-an-ionic-4-pwa-with-capacitor-2dce>

在本教程中，我们将学习用 Ionic 4 和 Capacitor 构建一个**渐进式 Web 应用程序(PWA)** 。

PWA 是一种类似于传统 web 应用程序的 web 应用程序，但它为用户提供了仅在原生移动应用程序中可用的额外功能，如即时加载、添加到主屏幕、闪屏、访问设备功能、推送通知和离线支持。

渐进式网络应用中的术语“渐进式”指的是这些应用如何提供可靠的网络体验**-无论网络条件如何都能即时加载-**-对每一次用户交互做出即时流畅的响应-以及**参与** -提供身临其境的自然用户体验。****

 ****为了实现这些要求苛刻的用户体验，PWA 使用服务人员来支持即时加载和离线支持等功能，因此需要通过 HTTPS 安全地提供服务，因为服务人员仅在 HTTPS 连接下工作。PWAs 实现了对所有新旧浏览器的支持，这是这类应用的一个重要方面。由于 pwa 使用了并非在所有浏览器中都可用的现代功能，因此 pwa 使用了渐进式增强功能，即在可用时使用该功能，并为不支持该功能的浏览器使用后备功能。

我们将要构建的应用程序是一个简单的 JavaScript 行话应用程序，它基于在 [GitHub](https://github.com/HugoGiraudel/SJSJ) 上可用的简化 JavaScript 行话。我们将把条目导出为 JSON 数据，并从 PWA 中使用它们。我们已经创建了一个静态生成的 JSON API 可用[这里](https://media.jscrambler.com/blog/data.json)。

你可以在这个 [GitHub 资源库](https://github.com/techiediaries/ionic-capacitor-pwa)里找到这个 app 的源代码。

> **注意**:如果你想从你的服务器上使用一个 API，确保你的网络服务器上已经启用了 [CORS](https://blog.jscrambler.com/understanding-cors-cross-origin-resource-sharing/) 。否则，由于现代浏览器上的**同源策略**，网络浏览器会阻止你的请求。

现在，让我们开始吧！

## 先决条件

*   您需要一个安装了 Node.js 和 npm 的开发环境。你可以通过进入[官方网站](https://www.nodejs.org)安装这两个软件，并为你的系统抓取二进制文件。
*   熟悉 TypeScript，因为我们将把 [Ionic 4](https://blog.jscrambler.com/introduction-to-ionic-4/) 与 Angular 一起使用。

## 生成一个新的离子 4 项目

让我们从使用以下命令从 npm 在您的系统上全局安装 Ionic CLI 开始:

```
npm install -g ionic 
```

在 CLI 中，您可以使用以下命令生成一个新项目:

```
ionic start 
```

CLI 将提示您输入项目的相关信息，例如名称(输入 **jsjargonpwa** )和启动模板(选择**侧菜单**)。这将设置您的项目。

当提示**安装免费的 Ionic Appflow SDK 并连接你的应用？(Y/n)** 只需键入`n`。

您现在可以导航到您的项目文件夹，并使用:
在本地提供您的应用程序

```
cd ./jsjargonpwa
ionic serve 
```

您的申请可从`http://localhost:8100`地址获得。

我们将在主页上工作，所以你可以删除列表页。首先，删除包含文件的列表文件夹；接下来，打开`src/app/app.component.ts`文件并从`appPages`数组中删除列表页面的条目:

```
 public appPages = [
       {
         title: 'Home',
         url: '/home',
         icon: 'home'
       }
     ]; 
```

接下来，打开`src/app/app-routing.module.ts`文件，删除列表页面的路径:

```
 const routes: Routes = [
     {
       path: '',
       redirectTo: 'home',
       pathMatch: 'full'
     },
     {
       path: 'home',
       loadChildren: './home/home.module#HomePageModule'
     }
   ]; 
```

## 获取 JSON 数据

我们将使用 Angular 的`HttpClient`向我们的服务器发送 GET 请求来获取 JSON 条目。在此之前，我们需要将它导入到我们的项目中。

首先，打开`src/app/home/home.module.ts`文件，导入`HttpClientModule` :

```
 // [...]
   import { HttpClientModule } from '@angular/common/http';

   @NgModule({
     imports: [
       /* [...] */
       HttpClientModule
     ],
     declarations: [HomePage]
   })
   export class HomePageModule {} 
```

接下来，打开`src/app/home/home.page.ts`文件并相应地更新:

```
 import { Component } from '@angular/core';
   import { HttpClient } from '@angular/common/http';
   @Component({
     selector: 'app-home',
     templateUrl: 'home.page.html',
     styleUrls: ['home.page.scss'],
   })
   export class HomePage {
     API_URL = "https://media.jscrambler.com/blog/data.json";
     entries: Array<any>;
     constructor(private httpClient: HttpClient){
     }
     ionViewDidEnter(){
       this.getData();
     }
     getData(){
       this.httpClient.get(this.API_URL).subscribe((entries: any[])=>{
         this.entries = entries;
       })
     }

   } 
```

我们声明了两个变量:`API_URL`——保存我们需要获取的 JSON 文件的地址——和`entries`，一个保存条目的数组。

现在，让我们通过组件的构造函数将`HttpClient`作为`httpClient`注入。

接下来，我们添加一个调用`HttpClient`的`get()`方法的`getData()`方法，并订阅返回的可观察值。然后，我们将获取的数据分配给`entries`变量。

最后，我们添加了加载 Ionic 页面时调用的`ionViewDidEnter()`事件，并在页面加载后调用`getData()`方法来获取条目。

接下来，打开`src/app/home/home.page.html`文件，更新如下:

```
 <ion-header>
     <ion-toolbar>
       <ion-buttons slot="start">
         <ion-menu-button></ion-menu-button>
       </ion-buttons>
       <ion-title>
         JSJargon
       </ion-title>
     </ion-toolbar>
   </ion-header>
   <ion-content>
       <ion-list lines="none">
         <ion-item *ngFor = "let entry of entries">
           <ion-card>
             <ion-card-header>
               <ion-card-title>{{ entry.name }}</ion-card-title>
             </ion-card-header>
             <ion-card-content>
               <p>{{ entry.description }}</p>
             </ion-card-content>
           </ion-card>
         </ion-item>
       </ion-list>
   </ion-content> 
```

我们简单地遍历`entries`变量，并使用离子卡显示每个条目的名称和描述。

这是结果的截图:

<center>![App Screenshot 1](img/7651b03efa8e73a2454eed6f7e99bb26.png)</center>

## 添加电容器

[Capacitor](https://capacitor.ionicframework.com/) 是一个开源的原生容器(类似于 Cordova ),由 Ionic 团队构建，你可以使用它来构建运行在 iOS、Android、electronic(桌面)上的 web/移动应用，以及具有相同代码库的渐进式 Web 应用。它允许您在每个平台上访问完整的原生 SDK，并轻松部署到应用商店或创建应用的 PWA 版本。

电容器可以与 Ionic 或任何首选前端框架一起使用，并可以通过插件进行扩展。它有一套丰富的官方插件，你也可以和 [Cordova 插件](https://cordova.apache.org/plugins/)一起使用。

[![Protect your Code with Jscrambler](img/ee5fdc03b17ad6adb178b9607c227f8a.png)](https://blog.jscrambler.com/protecting-hybrid-mobile-apps-with-ionic-and-jscrambler/?utm_source=dev.to&utm_medium=referral)

## 安装电容器

让我们从在您的项目中安装电容器开始:

```
npm install --save @capacitor/cli @capacitor/core 
```

接下来，需要用`npx cap init \[appName\] [appId]` :
初始化电容器

```
npx cap init jsjargon com.techiediaries.jsjargon 
```

## 使用剪贴板插件

现在，让我们在我们的项目中使用剪贴板电容器插件，通过例子来看看电容器是如何工作的。

打开`src/app/home/home.page.ts`文件，添加:

```
import { Plugins } from '@capacitor/core';
const { Clipboard } = Plugins; 
```

接下来，添加用于将 JS 术语复制到剪贴板的`copy()`方法:

```
 async copy(name: string, text: string){
       Clipboard.write({
         string:  name + ' is ' + text
       });
     } 
```

最后，打开`src/app/home/home.page.html`文件，在离子卡上为每个条目添加一个按钮:

```
 <ion-card>
             <ion-card-header>
               <ion-card-title>{{ entry.name }}</ion-card-title>
             </ion-card-header>
             <ion-card-content>
               <p>{{ entry.description }}</p>
             </ion-card-content>
               <ion-button (click)="copy(entry.name, entry.description)">
                 Copy
               </ion-button>
           </ion-card> 
```

这是结果的截图:

<center>![App Screenshot 2](img/29a62e1a5cbc11fedba43419c51c8754.png)</center>

## 添加 Web 清单和服务人员

web 清单和服务人员是 PWA 的基本要求。您可以使用`@angular/pwa`包来添加它们。在您的终端中，运行:

```
cd jsjargonpwa
ng add @angular/pwa 
```

这是`@angular/pwa`在您的项目中添加和更新的内容的屏幕截图:

<center>![CLI Angular Added](img/7d0bb2ea6fe5ab367cf57b2c8bdacb26.png)</center>

例如，创建一个`src/manifest.webmanifest`文件，并在`index.html`文件中引用:

```
 <link rel="manifest" href="manifest.webmanifest"> 
```

另外，在`src/assets/icons`文件夹中添加了不同的默认图标。在生产中，您需要用自己的图标来更改这些图标。

在`src/app/app.module.ts`文件中，使用以下代码行注册了一个服务人员:

```
 ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }) 
```

这仅在生产环境中启用，因此您需要构建用于生产环境的应用程序来注册服务人员。

接下来，我们需要使用下面的命令为生产构建我们的应用程序:

```
ionic build --prod 
```

最后，我们需要使用托管服务，如 [Netlify](https://netlify.com) 通过 HTTPS 安全地托管应用程序(PWA 需要)。这里是[链接](https://objective-ride-b228de.netlify.com/)到我们主办的 PWA。

## 结论

在本教程中，我们已经看到了如何创建一个离子 4，角和电容 PWA。

我们已经看到了一个使用`HttpClient`发送 GET 请求的例子，以及如何使用剪贴板电容插件访问设备剪贴板。我们希望本教程对您自己的项目有所帮助。

**如果你正在用敏感的逻辑构建 Ionic 应用程序，一定要遵循[我们的指南](https://blog.jscrambler.com/protecting-hybrid-mobile-apps-with-ionic-and-jscrambler/?utm_source=dev.to&utm_medium=referral)** 来保护它们免受代码窃取和逆向工程。

这篇文章最初发表在 Ahmed Bouchefra 的 Jscrambler 博客上。****