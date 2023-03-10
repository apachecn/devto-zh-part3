# 使用 Vue.js 和 CometChat 添加可靠的语音和视频聊天

> 原文：<https://dev.to/yemiwebby/add-reliable-voice-and-video-chat-with-vue-js-and-cometchat-3nj7>

[![](img/251f9809676e9561e3f5655aa2b59d8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ErBZZrh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJawH4le3YY__TZaojzQ-5g.png)

### 先决条件

JavaScript 和 Vue.js 的基础知识将帮助您从本教程中获得最大收益。为了让每个人都容易理解，我将尽力分解任何复杂的实现。此外，您还需要确保您的计算机上安装了节点运行时和 npm。[点击此处安装节点](https://nodejs.org/en/)，否则请点击[此链接安装 npm](https://www.npmjs.com/) 。

### 简介

不管你的 web 应用程序的大小如何，语音和视频聊天功能是一个插件，它不仅可以让你的用户实时交流，进行面对面的互动或会面，而不必在同一个位置或地区，而且还可以提高你的应用程序的参与度和交互性。虽然语音和视频聊天的实现听起来很酷，但相信我，你不会想从头开始构建它。这就是像 [CometChat](http://cometchat.com) 这样的优秀工具大放异彩的地方。

因此，您可以使用 CometChat API 轻松构建整个功能，而不是从头开始为您的聊天应用程序构建后端，这将使您能够构建实时语音和视频聊天等通信功能。

在本教程中，我们将利用 CometChat 提供的一些优秀的 API 来构建一个语音和视频聊天应用程序。您将能够在本地两个独立的窗口(浏览器)上运行该应用程序，并有能力在成功实施后拨打、接听和拒绝电话。一旦我们完成，您将构建一个类似于以下内容的应用程序:

[![](img/4d65790303da596f4015f7df58e60590.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0JV0hy6E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AglUuoiih2E23ALMo.png)

这个应用程序将使用 Vue.js 和 CometChat Pro SDK 构建。如果你想直接进入代码，可以在 GitHub 上找到本教程的完整源代码。

### 入门

首先，我们将使用名为 [Vue CLI](https://cli.vuejs.org/) 的强大工具创建并安装一个新的 Vue.js 应用程序。这是一个由 Vue.js 团队创建的标准工具，旨在帮助和允许开发人员轻松快速地搭建新项目。从终端运行以下命令，将其全局安装到您的计算机上:

```
npm install -g @vue/cli 
```

安装完成后，继续使用 vue 命令创建一个新的 Vue.js 项目，如下所示:

```
vue create comet-voice-video 
```

通过在键盘上按 Enter 键选择“手动选择功能”选项，并通过在计算机上按空格键选择一个功能来检查该项目将需要的功能。如下所示，您应该选择 Babel、Router 和 Linter / Formatter:

[![](img/1b7afa9933b5dcdc0dcb05e4d4730a9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xLJYmPt4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AgrujiJcOFDvPc6dy.png)

对于其他说明，键入 y 以使用路由器的历史模式。理想情况下，Vue-Router 的默认模式是哈希(#)模式，因为它使用 URL 哈希来模拟完整的 URL，这样当 URL 更改时就不会重新加载页面。在这里选择历史模式将有助于摆脱哈希模式，以便在不重新加载页面的情况下实现 URL 导航，并将此配置添加到将为此项目自动生成的路由器文件中。此外，选择仅带有错误预防的 ESLint，以便选择 linter / formatter 配置。接下来，选择 Lint on save 以获得额外的 Lint 特性，并将您的配置保存在一个专用的配置文件中，以供将来的项目使用。为您的预置键入一个名称，我将它命名为 mine vuecomet:

[![](img/1db4d73415b4a64f5d624ef537b96f99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ybg9ffE7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AiM9lvsjv0UBP9QhC.png)

配置完成后，Vue CLI 将立即开始安装应用程序，并在名为 comet-voice-video 的新文件夹中安装所有必需的依赖项。

### 启动应用程序

现在新应用程序的安装已经完成，进入新项目并使用以下命令启动开发服务器:

```
// move into the app
cd comet-voice-video

// start the server
npm run serve 
```

在 [http://localhost:8080](http://localhost:8080) 上查看应用程序的欢迎页面:

[![](img/66614b6d52eac4bb77f043eb9f0c84e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mol0Mt-p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Abex-CdcSp2bJ5LN2.png)

此外，因为我们将依赖 CometChat Pro 来轻松构建我们的应用程序，所以让我们在进行视频聊天实现之前安装 SDK。通过在您的计算机上按 CTRL + C 停止开发服务器的运行，并从项目目录运行以下命令:

```
npm install @cometchat-pro/chat --save 
```

现在，我们可以很容易地导入 CometChat 对象，只要我们想在应用程序中使用 CometChat，如下所示:

```
import { CometChat } from '@cometchat-pro/chat'; 
```

### 创建 CometChat Pro 账户、应用 ID 和 API 密钥

由于我们将利用 CometChat 的托管服务来成功构建我们的语音和视频聊天应用程序，请访问网站[这里](https://app.cometchat.com/#/register)和**创建一个免费的 CometChat Pro 帐户。**填写所有必需的信息以建立一个试用账户。

登录查看您的 [CometChat 仪表盘](https://app.cometchat.com/#/apps)，让我们创建一个新项目。这将使我们能够访问唯一的应用 ID 和 API 密钥

[![](img/713f70c7385ce36c24654784fd861823.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xamazMng--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AHBgWBArv_bUUJpee.png)

在“添加新应用程序”对话框中，输入名称并单击加号创建新应用程序。完成后，点击**探索**按钮创建新应用。您将被重定向到如下所示的新页面:

[![](img/b95863cd83eed732ec490bddfb0c77f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zxuzp54o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AXwk1hzMXu9aLLyuv.png)

接下来，从左侧菜单转到“API Keys”选项卡，您将看到一个类似于以下内容的页面:

[![](img/058f0790357be5758cbcf7b938c99ef0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5uANaMvy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A2Gvaxwp_shGxabor.png)

在您从仪表板上创建一个新应用程序后，CometChat 会立即为您的新演示应用程序自动生成一个 API 密匙。您不需要创建一个新的，因为这样就足够了，并且您可以完全访问 CometChat 提供的功能。不要忘记记下或者更好的是，复制自动生成的完全访问 API 密钥和应用程序 ID，因为我们很快就会需要它们。

既然我们已经完成了成功创建应用程序所需的所有必要工具和凭证的设置，我们马上就要开始构建了。

### 我们想要达到的目标

在我们开始正确构建应用程序之前，让我们快速讨论一下应用程序的结构以及我们打算如何构建流程。

基本上，我们希望用户从不同的位置登录，并且一旦我们在一个活动服务器上托管我们的应用程序，就能够使用语音和视频聊天，但是为了本教程，我们将在本地使用两个不同的窗口。用户登录后:

[![](img/c33773fce235f3cf0d1d09a574cc53d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ts9E-tJ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/688/0%2AkTt2X4O8Ie6HYNQx.png)

我们将重定向到一个不同的页面，他或她可以输入另一个用户的 UID，并开始视频聊天。CometChat 的每个[用户](https://prodocs.cometchat.com/docs/concepts#section-users)都使用他或她的 UID 进行唯一标识，您可以考虑这一点或将其与数据库中用户的典型唯一主 ID 相关联，这样就有机会标识这样的用户:

[![](img/7405d5d1a2945874e1a2d31624b926bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vD6EoA2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AuqXug55-16JgZuXy.png)

### 初始化 CometChat

首先，在使用 CometChat 时，典型的工作流要求 CometChat 的设置必须通过在调用 CometChat 的任何其他方法之前调用 init()方法来初始化。首先，创建一个名为。env，并将以下代码粘贴到其中:

```
// .env
VUE_APP_COMMETCHAT_API_KEY=YOUR_API_KEY

VUE_APP_COMMETCHAT_APP_ID=YOUR_APP_ID 
```

这将使得在我们的项目中引用和使用我们的应用程序凭证变得非常容易。不要忘记将`YOUR_API_KEY`和`YOUR_APP_ID`占位符替换为从 CometChat 仪表板中获得的相应凭证。

接下来，导航到。/src/App.vue 文件，它是 Vue.js 应用程序的根组件，将其内容替换为:

```
// ./src/App.vue

<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
import { CometChat } from "@cometchat-pro/chat";

import "./App.css";

export default {
  data() {
    return {};
  },
  created() {
    this.initializeApp();
  },
  methods: {
    initializeApp() {
      var appID = process.env.VUE_APP_COMMETCHAT_APP_ID;

      CometChat.init(appID).then(
        () => {
          console.log("Initialization completed successfully");
        },
        error => {
          console.log("Initialization failed with error:", error);
        }
      );
    }
  }
};
</script> 
```

What we have done here is to include the functional component that will render any matched component for a given path from[Vue Router.](https://router.vuejs.org/) We will configure the router later in this tutorial. Next, within the section, we imported the CometChat object and a CSS file that we will create next. Lastly, we initialize CometChat by passing the application ID as a parameter.</p> <p>Now create a new file named <code>App.css</code> within <code>./src/App.css</code> and paste the following content in it:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/App.css @import 'https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css'; @import 'https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css'; #app { font-family: "Avenir", Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; text-align: center; color: #2c3e50; } #nav { padding: 30px; } #nav a { font-weight: bold; color: #2c3e50; } #nav a.router-link-exact-active { color: #42b983; } #auth { width: 600px; margin: 0 auto; } #callScreen { width: 500px; height: 500px; } .home { width: 600px; margin: 0 auto; } </code></pre></div> <p></p> <p>We imported the CDN files for <a href="https://getbootstrap.com/">Bootstrap</a> and <a href="https://fontawesome.com/">Font awesome</a> and then proceeded to add some default style for the application. Feel free to modify this content as you deem fit.</p> <h3> <a name="login-component" href="#login-component" class="anchor"> </a> Login component </h3> <p>One of the key concept when building chat applications with CometChat is to ensure that users are authenticated before they can have access to use CometChat and start a chat. To ensure this, we will create a Login component that will handle the logic for authenticating a user and redirecting such user to the appropriate page for a chat.</p> <p>To begin, create a new folder named auth within the views folder and within the newly created folder, create a new file and call it <code>Login.vue</code>. Open this new file and paste the following contents:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/auth/Login.vue &lt;template&gt; &lt;div id="auth"&gt; &lt;div id="nav"&gt; &lt;router-link to="/login"&gt;Login&lt;/router-link&gt; &lt;/div&gt; &lt;p&gt; Enter your username to start video chat &lt;/p&gt; &lt;p&gt;Create an account through your CometChat dashboard or login with one of our test users (superhero1, superhero2)&lt;/p&gt; &lt;form v-on:submit.prevent="authLoginUser"&gt; &lt;div class="form-group"&gt; &lt;input name="username" id="username" class="form-control" placeholder="Enter your username" v-model="username"/&gt; &lt;/div&gt; &lt;div class="form-group"&gt; &lt;button type="submit" class="btn btn-success"&gt; Login &lt;span v-if="showSpinner" class="fa fa-spin fa-spinner"&gt;&lt;/span&gt;&lt;/button&gt; &lt;/div&gt; &lt;/form&gt; &lt;/div&gt; &lt;/template&gt; &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { data() { return { username: "", showSpinner: false }; }, methods: { authLoginUser() { var apiKey = process.env.VUE_APP_COMMETCHAT_API_KEY; this.showSpinner = true; CometChat.login(this.username, apiKey).then( () =&gt; { this.showSpinner = false; this.$router.push({ name: "home" }); }, error =&gt; { this.showSpinner = false; console.log("Login failed with error:", error.code); } ); } } }; &lt;/script&gt; </code></pre></div> <p></p> <p>What we have done here is pretty simple. First, we included an HTML form and added an input field that will accept the username of a user during the authentication process. Once the form is submitted, it will be processed using a method named authLoginUser().</p> <p>Next within the <script> tag, we imported the CometChat object and created the authLoginUser() method attached to the form. Within the authLoginUser() method, we called the login() method from CometChat and passed our application API and the username to uniquely identify the user.</p> <p>Once the user logs into the application, we used the <a href="https://router.vuejs.org/">Vue Router</a> to redirect the user to the home page which will be handled by a different component name HomeComponent. We will create that in the next section.</p> <h3> <a name="home-component" href="#home-component" class="anchor"> </a> Home component </h3> <p>In chronological order, what we want to achieve with this component is:</p> <ul> <li>Display the <strong>UID</strong> of the currently logged in user</li> <li>Initiate a video chat</li> <li>Accept or reject a call</li> <li>And finally, be able to log out of the application.</li> </ul> <p>Let’s start adding these functionalities. To begin, open <code>./src/views/Home.vue</code> and replace its content with:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;template&gt; &lt;div class="home"&gt; &lt;div id="nav"&gt; &lt;button class="btn btn-success" [@click](http://twitter.com/click)="logoutUser"&gt;Logout&lt;/button&gt; &lt;/div&gt; &lt;div class="form-group"&gt; &lt;div class="form-group"&gt; &lt;p&gt;Welcome &lt;b&gt;{{ this.username}}&lt;/b&gt;, your UID is &lt;b&gt;{{ this.uid }}&lt;/b&gt; &lt;br&gt; Enter the receiver Id to start a chat &lt;/p&gt; &lt;p v-if="error"&gt; &lt;b class="text-danger"&gt; Receiver ID is required &lt;/b&gt; &lt;/p&gt; &lt;input type="text" class="form-control" placeholder="Enter receiver UID" v-model="receiver_id"&gt; &lt;/div&gt; &lt;div v-if="incomingCall"&gt; &lt;button class="btn btn-success" [@click](http://twitter.com/click)="acceptCall"&gt;Accept Call&lt;/button&gt; &lt;button class="btn btn-success" [@click](http://twitter.com/click)="rejectCall"&gt;Reject Call&lt;/button&gt; &lt;/div&gt; &lt;div v-else-if="ongoingCall"&gt; &lt;button class="btn btn-secondary"&gt; Ongoing Call ... &lt;/button&gt; &lt;/div&gt; &lt;div v-else&gt; &lt;button [@click](http://twitter.com/click)="startVideoChat" class="btn btn-secondary"&gt; Start Call &lt;span v-if="showSpinner" class="fa fa-spin fa-spinner"&gt;&lt;/span&gt; &lt;/button&gt; &lt;/div&gt; &lt;/div&gt; &lt;div id="callScreen"&gt;&lt;/div&gt; &lt;/div&gt; &lt;/template&gt; </code></pre></div> <p></p> <p>Here, we added contents to display the username and the UID of the logged in user. Next, we included an input field that will accept the UID of the receiver in order to uniquely identify such user and be able to initiate or send a call request.</p> <p>Once a call request has been received, the receiver has an option of either rejecting or accepting the call. We have included click events for this process and will define the methods next.</p> <p><strong>Get the logged in user</strong></p> <p>First, let’s retrieve the details of the currently logged in user by adding this <script> section to our code. Place the contents below within the Home.vue immediately after the closing tag of the <template> section:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { name: "home", data() { return { username: "", uid: "", session_id: "", receiver_id: null, error: false, showSpinner: false, incomingCall: false, ongoingCall: false }; }, created() { this.getLoggedInUser(); }, methods: { getLoggedInUser() { var user = CometChat.getLoggedinUser().then( user =&gt; { this.username = user.name; this.uid = user.uid; }, error =&gt; { this.$router.push({ name: "homepage" }); } ); }, } }; &lt;/script&gt; </code></pre></div> <p></p> <p>Basically, we imported the CometChat method, defined some properties and corresponding initial values within the data option. And finally, once the component is created, we called a method named <code>getLoggedInUser()</code> to automatically retrieve the details of a logged in user and update the view with it.</p> <p><strong>Start video chat</strong></p> <p>To initiate a chat from the app, we will add this method to the function associated with the Vue instance:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { ... methods: { ... startVideoChat() { if (!this.receiver_id) this.error = true; this.showSpinner = true; var receiverID = this.receiver_id; var callType = CometChat.CALL_TYPE.VIDEO; var receiverType = CometChat.RECEIVER_TYPE.USER; var call = new CometChat.Call(receiverID, callType, receiverType); CometChat.initiateCall(call).then( outGoingCall =&gt; { this.showSpinner = false; console.log("Call initiated successfully:", outGoingCall); // perform action on success. Like show your calling screen. }, error =&gt; { console.log("Call initialization failed with exception:", error); } ); } } }; &lt;/script&gt; </code></pre></div> <p></p> <p>This method will be used to specify the type of call and that of a receiver. For our application, this will be a <code>CALL_TYPE.VIDEO</code> and <code>RECEIVER_TYPE.USER</code> respectively. Finally, we passed these details through an object of call into the <code>initiateCall()</code> method from the CometChat API to send a call request.</p> <p><strong>Listen and receive calls</strong></p> <p>Once a call has been initiated, we need to listen to the call events within our application and based on the actions that will be carried out by the user to either receive or reject the call, we will call the appropriate method. To do this successfully, we need to register the CallListener listener using the <code>addCallListener()</code> method from CometChat. Add the following contents within the created() method:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { ... created() { ... let globalContext = this; var listnerID = "UNIQUE_LISTENER_ID"; CometChat.addCallListener( listnerID, new CometChat.CallListener({ onIncomingCallReceived(call) { console.log("Incoming call:", call); globalContext.incomingCall = true; globalContext.session_id = call.sessionId; }, onOutgoingCallAccepted(call) { console.log("Outgoing call accepted:", call); globalContext.ongoingCall = true; CometChat.startCall( call.sessionId, document.getElementById("callScreen"), new CometChat.OngoingCallListener({ onUserJoined: user =&gt; { /* Notification received here if another user joins the call.*/ console.log("User joined call:", user); /* this method can be use to display message or perform any actions if someone joining the call */ }, onUserLeft: user =&gt; { /* Notification received here if another user left the call.*/ console.log("User left call:", user); /* this method can be use to display message or perform any actions if someone leaving the call*/ }, onCallEnded: call =&gt; { globalContext.ongoingCall = false; globalContext.incomingCall = false; /* Notification received here if current ongoing call is ended.*/ console.log("Call ended:", call); /* hiding/closing the call screen can be done here.*/ } }) ); // Outgoing Call Accepted }, onOutgoingCallRejected(call) { console.log("Outgoing call rejected:", call); this.incomingCall = false; this.ongoingCall = false; this.receiver_id = ""; // Outgoing Call Rejected }, onIncomingCallCancelled(call) { console.log("Incoming call calcelled:", call); } }) ); }, methods: { ... } }; &lt;/script&gt; </code></pre></div> <p></p> <p>Once the recipient accepts an outgoing call in the <code>onOutgoingCallAccepted()</code> callback of the CallListener, we called another method <code>startCall()</code> which takes the</p> <ul> <li>sessionId: The unique session ID available in the CometChat.Call object,</li> <li>callScreen: DOM element where you want to show the call user interface and</li> <li>OngoingCallListener: a CometChat.OngoingCallListener where the real-time events will be received</li> </ul> <p>as parameters.</p> <p><strong>Accept and reject calls</strong></p> <p>Once an incoming call event is received, we will call a method named <code>acceptCall()</code>. This method also takes the unique session ID available in the CometChat.Call object as a parameter. Add this method immediately after the <code>startVideoChat()</code> method:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { ... methods: { ... acceptCall() { let globalContext = this; this.ongoingCall = true; this.incomingCall = false; var sessionID = this.session_id; CometChat.acceptCall(sessionID).then( call =&gt; { console.log("Call accepted successfully:", call); console.log("call accepted now...."); // start the call using the startCall() method console.log(globalContext.ongoingCall); CometChat.startCall( call.sessionId, document.getElementById("callScreen"), new CometChat.OngoingCallListener({ onUserJoined: user =&gt; { /* Notification received here if another user joins the call.*/ console.log("User joined call:", user); /* this method can be use to display message or perform any actions if someone joining the call*/ }, onUserLeft: user =&gt; { /* Notification received here if another user left the call.*/ console.log("User left call:", user); /* this method can be use to display message or perform any actions if someone leaving the call*/ }, onCallEnded: call =&gt; { /* Notification received here if current ongoing call is ended.*/ console.log("Call ended:", call); globalContext.ongoingCall = false; globalContext.incomingCall = false; /* hiding/closing the call screen can be done here.*/ } }) ); }, error =&gt; { console.log("Call acceptance failed with error", error); // handle exception } ); } } }; &lt;/script&gt; </code></pre></div> <p></p> <p>Here, once an incoming call has been accepted, we then called the startCall() method. With this in place, the sender and the recipient can start a chat. Next, add another method to reject calls:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { ... methods: { ... rejectCall() { var sessionID = this.session_id; var globalContext = this; var status = CometChat.CALL_STATUS.REJECTED; CometChat.rejectCall(sessionID, status).then( call =&gt; { console.log("Call rejected successfully", call); globalContext.incomingCall = false; globalContext.ongoingCall = false; globalContext.receiver_id = ""; }, error =&gt; { console.log("Call rejection failed with error:", error); } ); } } }; &lt;/script&gt; </code></pre></div> <p></p> <p>And lastly, add another method to Log out users from the application and redirect back to the login page:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/views/Home.vue &lt;script&gt; import { CometChat } from "[@cometchat](http://twitter.com/cometchat)-pro/chat"; export default { ... methods: { ... logoutUser() { CometChat.logout().then( success =&gt; { console.log("Logout completed successfully"); this.$router.push({ name: "homepage" }); console.log(success); }, error =&gt; { //Logout failed with exception console.log("Logout failed with exception:", { error }); } ); } } }; &lt;/script&gt; </code></pre></div> <p></p> <p>The <code>CometChat.logout()</code> will log the user out of CometChat and also from our application.</p> <p>In case you miss anything, the complete contents of the Home.vue file can be <a href="https://github.com/yemiwebby/comet-voice-video/blob/master/src/views/Home.vue">found here</a>.</p> <h3> <a name="update-router" href="#update-router" class="anchor"> </a> Update router </h3> <p>Earlier, when we installed Vue.js, we also selected an option of installing Vue Router with it. So open <code>./src/router.js</code> and replace the default content in it with the following:</p> <p></p> <div class="highlight"><pre class="highlight plaintext"><code>// ./src/router.js import Vue from 'vue' import Router from 'vue-router' import Home from './views/Home.vue' import Login from './views/auth/Login.vue' Vue.use(Router) export default new Router({ mode: 'history', base: process.env.BASE_URL, routes: [ { path: '/', redirect: 'login', name: 'homepage' }, { path: '/login', name: 'login', component: Login, }, { path: '/home', name: 'home', component: Home } ] }) </code></pre></div> <p></p> <p>What we have done here is to map each route within our application to the respective component that will handle the logic. This is a basic format of configuring Vue Router for a Vue.js application. Click <a href="https://router.vuejs.org/">here</a> to find out more about Vue Router.</p> <h3> <a name="test-the-application" href="#test-the-application" class="anchor"> </a> Test the application </h3> <p>Now that you are done adding all the required logic and components, you can now run the application and test it out. Restart the application with npm run serve and navigate to <a href="http://localhost:8080">http://localhost:8080</a> to view the app.</p> <p><img src="https://cdn-images-1.medium.com/max/688/0*qLJzgX9dJ-ZnIB5U.png" alt=""></p> <p>Open the application in two separate windows and login with any two of the test users: superhero1, superhero2, superhero3, superhero4 or superhero5</p> <p><img src="https://cdn-images-1.medium.com/max/600/0*h2sDQ5lxWHDxWN6p.gif" alt=""></p> <p>Once you are able to log in from both window, enter the UID of one user and click on <strong>Start Call</strong> and once the call request is received by the other user, two new action buttons will be displayed. Click <strong>Accept Call</strong> to accept a call</p> <p><img src="https://cdn-images-1.medium.com/max/600/0*JgKRdftJdW8sg0CR.gif" alt=""></p> <p>or <strong>Reject Call</strong> to reject a call:</p> <p>If you have followed the tutorial diligently up until now, then you should have a functional voice and video chat application running on your computer.</p> <h3> <a name="conclusion" href="#conclusion" class="anchor"> </a> Conclusion </h3> <p>As seen in this tutorial, you will agree with me that it is so easy to quickly implement a video chat feature in your project. This can be easily integrated into an existing Vue.js application or a new one entirely.</p> <p>Feel free to explore the source code as linked at the beginning of this tutorial and add more features as you deem fit.</p> <p>With CometChat, you can easily extend the functionalities of this application by adding more awesome features. <a href="https://prodocs.cometchat.com/">Check the official documentation of CometChat Pro</a> to read more about what its got to offer.</p> <p>I do hope you found this tutorial quite helpful? Can’t wait to see what you will build with the knowledge that you have acquired here.</p> <p>Originally published on <a href="https://www.cometchat.com">CometChat</a></p>