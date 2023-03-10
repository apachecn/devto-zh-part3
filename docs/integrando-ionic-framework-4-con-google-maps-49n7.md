# integrando Ionic Framework 4 con Google maps

> 原文：<https://dev.to/ecudevs/integrando-ionic-framework-4-con-google-maps-49n7>

这是我在 Dev.to 上的第一个帖子，是对 ecudevs 上发布的离子框架的课程的补充。

这次，我们将把我们在离子框架上制作的应用程序与谷歌地图 api 集成起来。由于此帖子是对课程的补充，我们将使用我们在此过程中开发的小应用程序生成的源代码，因此您可以克隆以下存储库:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[ECU devs](https://github.com/ecudevs)/[ionic-5](https://github.com/ecudevs/ionic-5)

### Clase 5 离子框架 ECU 开发

<article class="markdown-body entry-content container-lg" itemprop="text">

## ionic framerk 中的第 5 类源代码

# Ecudevs

克隆存储库后运行:`npm install`然后`ionic serve --lab`

</article>

[View on GitHub](https://github.com/ecudevs/ionic-5)

Esta es una pequeña app que realiza el mantenimiento de una colección de una base de datos de [MongoDB](https://www.mongodb.com/es).

一旦克隆了我们的存储库并安装了所有设备，我们就可以开始工作了。

# 1。获得一个谷歌的 api 密钥

你要做的第一件事是从谷歌地图获取一个关键 Api，我们是从谷歌的[云控制台](https://console.cloud.google.com)这样做的，登录到你的 Google 账户并创建一个新项目。

[![Google Console](img/1b4c93d3f275fad6dfdf541b9fb53a29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rbXUSlm0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o0pw2wgg9e5xmvjlau10.PNG)

创建项目后，从侧面菜单访问 API 库。

[![Acesso a la biblioteca de APIs](img/bd6fd5ecb1d76094f050b159af7e17a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AgLjJ61X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hjh2gtse7uqc5tu76u2n.png)

在此处搜索并选择选项【android 的 map SDK，然后启用此 API。

然后，在“身份凭证”部分，单击“创建身份凭证”并选择“密钥 API”选项。

[![Crear una credencial](img/24b2de5a400f4949b1f361904e0a63d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QfMNVtHk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9kc1kddnpf03qhim6u2v.png)

此过程会自动生成一个关键点，我们现在将该关键点保存在安全的位置以供以后使用，因此我们最终将关闭已部署的对话框。

[![API key generado](img/ad997da607a52b4526b087da8ed9c7ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gg5z595a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gsf1aezddmwiy43glhfs.png)

# 2。土著民族融合协会

[离子原生](https://ionicframework.com/docs/native)是通过 [Cordova](https://cordova.apache.org/) 向我们离子应用添加原生功能的工具。

在这种情况下，对于 google 地图，我们将使用一个名为[离子原生 google 地图](https://github.com/ionic-team/ionic-native-google-maps)的插件，可以通过 npm 和离子 CLI 使用以下命令安装:

```
npm install @ionic-native/core@beta @ionic-native/google-maps@beta

ionic cordova plugin add https://github.com/mapsplugin/cordova-plugin-googlemaps#multiple_maps 
```

Enter fullscreen mode Exit fullscreen mode

为我们的 Google 地图安装了依赖项后，我们需要转到我们的文件*【config . XML】*，紧接在最后一个首选项节点之后，我们粘贴了以下内容，替换了我们在第一步中获得的 key API 值:

```
<preference name="GOOGLE_MAPS_ANDROID_API_KEY" value="NUESTRO_API_KEY" /> 
```

Enter fullscreen mode Exit fullscreen mode

我们的代码应该是这样的:

[![config.xml](img/923d927139492d2eb53f8bace4378aae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MxUvHj3d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rxdi5n6o1ibgb0w7q485.PNG)

我们的配置准备好了，让我们进入我们真正关心的领域，进入我们的代码。

# 3。来规划我们的地图

我们的项目扩展了在生成时为我们提供离子 CLI 的选项卡式模板，迄今为止的所有编程都只侧重于一个选项卡，即*选项卡 1* ，我们的 Google 地图将在*选项卡 2 【T】上编程*

我们要做的第一件事是塑造我们的视野，所以我们将重点关注我们组件*【tab 2】*的 html 和 SCS 文件，并且在标签`<ion-content>`内定位如下。

```
 <div id="map_canvas"></div>
  <ion-fab vertical="bottom" horizontal="end" slot="fixed">
    <ion-fab-button color="danger" (click)="localizar()">
      <ion-icon name="pin"></ion-icon>
    </ion-fab-button>
  </ion-fab> 
```

Enter fullscreen mode Exit fullscreen mode

我们的代码应大致如下: [![tab2.page.html](img/4a8e39290c08451993493878f598a80e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0uXASvK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dphap38xh5f28cs1x4j1.PNG)

我们添加到视图中的第一个 *div* 是指将在其中呈现地图的空间。对此 *div* 我们必须设置某些样式，在这种情况下，我们将要添加的这些样式将使我们的地图占据整个屏幕，因此为此，我们将转到该组件的 SCS 文件并添加以下内容:

```
#map_canvas {
  width: 100%;
  height: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

请务必设定这些型式，否则地图不会显示在萤幕上。

回到我们的观点，我们在地图中包含的“*div 之后添加的下一项，是一个简单的`ion-fab`，它将执行组件的`localizar()`方法，此功能将使我们在地图上显示我们的位置。*

 *是时候在这个视图的驱动程序上工作了，让我们转到文件`tab2.page.ts`，在该文件中我们需要添加以下代码:

```
import { Component, OnInit } from "@angular/core";

import {
  GoogleMaps,
  GoogleMap,
  GoogleMapsEvent,
  Marker,
  GoogleMapsAnimation,
  MyLocation
} from "@ionic-native/google-maps";

import { Platform, LoadingController, ToastController } from "@ionic/angular";

@Component({
  selector: "app-tab2",
  templateUrl: "tab2.page.html",
  styleUrls: ["tab2.page.scss"]
})
export class Tab2Page implements OnInit {
  map: GoogleMap;
  loading: any;

  constructor(
    public loadingCtrl: LoadingController,
    public toastCtrl: ToastController,
    private platform: Platform
  ) {}

  async ngOnInit() {
    // Debido ngOnInit() inicia antes del evento
    // deviceready, debemos detectar cuando este evento se
    // ejecute para en ese momento cargar nuestro mapa sin problema alguno
    await this.platform.ready();
    await this.loadMap();
  }

  loadMap() {
    // Esta función inicializa la propiedad de clase
    // map
    // que va a contener el control de nuestro mapa de google

    // Para crear nuestro mapa debemos enviar como parametros
    // el id del div en donde se va a renderizar el mapa (paso anterior)
    // y las opciones que configuran nuestro mapa
    this.map = GoogleMaps.create("map_canvas", {
      camera: {
        target: {
          lat: -2.1537488,
          lng: -79.8883037
        },
        zoom: 18,
        tilt: 30
      }
    });
  }

  async localizar() {
    // Limpiamos todos los elementos de nuestro mapa
    this.map.clear();

    // Creamos un componente de Ionic para mostrar un mensaje
    // mientras obtenemos esperamos que termine el proceso de
    // obtener la ubicación
    this.loading = await this.loadingCtrl.create({
      message: "Espera por favor..."
    });

    // Presentamos el componente creado en el paso anterior
    await this.loading.present();

    // Ejecutamos el método getMyLocation de nuestra propiedad de clase
    // map
    // para obtener nuestra ubicación actual
    this.map
      .getMyLocation()
      .then((location: MyLocation) => {
        // Una vez obtenida la ubicación cerramos el mensaje de diálogo
        this.loading.dismiss();

        // Movemos la camara a nuestra ubicación con una pequeña animación
        this.map.animateCamera({
          target: location.latLng,
          zoom: 17,
          tilt: 30
        });

        // Agregamos un nuevo marcador
        let marker: Marker = this.map.addMarkerSync({
          title: "Estoy aquí!",
          snippet: "This plugin is awesome!",
          position: location.latLng,
          animation: GoogleMapsAnimation.BOUNCE
        });

        // Mostramos un InfoWindow
        marker.showInfoWindow();

        // Podemos configurar un evento que se ejecute cuando
        // se haya dado clic
        marker.on(GoogleMapsEvent.MARKER_CLICK).subscribe(() => {
          this.showToast("clicked!");
        });
      })
      .catch(error => {
        // En caso de que haya un problema en obtener la
        // ubicación
        this.loading.dismiss();
        this.showToast(error.error_message);
      });
  }

  // Función que muestra un Toast en la parte inferior
  // de la pantalla
  async showToast(mensaje) {
    let toast = await this.toastCtrl.create({
      message: mensaje,
      duration: 2000,
      position: "bottom"
    });

    toast.present();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 所有权`map`

我们班有一个属性 *`map`* ，它将包含我们谷歌地图的所有信息和方法，所以我们首先要做的是创建一个新的地图实例，刚开始我们的组件，为此我们实施了`OnInit`界面，同时但是，在调用此函数之前，我们必须确保运行我们代码的平台已准备就绪，并且能够调用本机功能，这样在加载我们的地图时就不会出现问题，我们是通过离子本身的一个名为 [Platform](https://ionicframework.com/docs/utilities/platform) 的‘T4’来完成此操作的

## loadMap()

该方法通过`GoogleMaps`类的`create`方法实现了我们地图本身的创建。`create`方法从 *div* 接收`Id`作为第一个参数，在此处将呈现我们的地图，并作为第二个参数接收必须实现界面 [`GoogleMapOptions`](https://github.com/ionic-team/ionic-native-google-maps/blob/master/documents/googlemapoptions/README.md) 的对象，在此情况下，我们将通过该对象

## localiar()

在此方法中，我们将使地图获取我们的当前位置，并显示一个小标记，在单击该标记时，我们将为其配置要执行的事件。

我们先用`this.map.clear()`方法清理我们的地图，然后显示一个小对话框，让我们的用户在得到位置时不会绝望。

```
 this.map.clear();

 this.loading = await this.loadingCtrl.create({
  message: "Espera por favor..."
 });

await this.loading.present(); 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们从我们的对象`map`运行`getMyLocation()`方法，这将在获得位置后还给我一个承诺。

```
this.map.getMyLocation()
 .then((location: MyLocation) => {
        ...
  })
 .catch(error => {
        ...
 }); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们得到了我们的位置信息，我们将通过`this.map.animateCamera`在地图上移动，这种方法我们必须发送与我们默认发送到我们地图的信息非常相似的信息，不同的是长度和纬度将会是我们得到的。

```
this.map.animateCamera({
 target: location.latLng,
 zoom: 17,
 tilt: 30
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们的视图位于生成的位置，我们将绘制一个新的标记，设置标题、小描述文字、位置和小动画`GoogleMapsAnimation.BOUNCE`，这样当标记被分解时，将触发反弹动画。

```
let marker: Marker = this.map.addMarkerSync({
 title: "Estoy aquí!",
 snippet: "This plugin is awesome!",
 position: location.latLng,
 animation: GoogleMapsAnimation.BOUNCE
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们设置了单击我们的书签，使其在触发时显示烤面包。

```
marker.on(GoogleMapsEvent.MARKER_CLICK).subscribe(() => { 
 this.showToast("clicked!"); 
}); 
```

Enter fullscreen mode Exit fullscreen mode

[![Resultado final](img/bb0b7f5e6c19c590b063c2ad5c460e2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x2A2s2Dg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zaudo0vlgc6kq6xf60z7.gif)

# 结论

谷歌地图的使用是当今非常普遍的做法，提及地点、地点的重要性已成为我们日常工作的一部分，这就是为什么我们的节目涵盖了这个问题，我希望这一点很有帮助，不要忘记你的喜欢和评论，它们对我们的改进有很大帮助。*