# 如何使用 Headless JS (Android)在 React Native 中创建一个不可阻挡的服务

> 原文：<https://dev.to/mathias5r/how-to-create-an-unstoppable-service-in-react-native-using-headless-js-android-5f61>

[![Alt text of image](img/7599608c383e90a0df51fab64e4508a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N3LpvABh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9a2xhsasrqrt29x3s5wz.gif)

<figcaption>Unstoppable</figcaption>

如果你已经开发了一个相对复杂的移动应用程序，可能你会发现自己绞尽脑汁去完成一些不常见的任务。这已经发生在我在 Segware 开发我们的应用程序时，集成特定硬件的事件。挑战在于每个事件都应由应用程序处理，而不管其状态如何，即应用程序处于前台、后台、关闭状态，甚至是在未启动时(设备重启)。

在这种情况下，必须有一些不附属于应用程序的东西，但同时它必须是**不可停止的**，几乎百分之百的时间都在运行以执行任务。在原生 android 应用中，可以使用一个名为[服务](https://developer.android.com/guide/components/services?hl=pt-br)的组件来完成，该组件能够在没有 UI 线程或用户操作的情况下执行任务。

本文的主要目标是解释如何实现一个原生 android 模块，以便在 React Native (RN)应用程序中执行这种任务，使用服务、广播接收器和 [headless JS](https://facebook.github.io/react-native/docs/headless-js-android) 。在本文中，我使用了一个简单的 **heartbeat** 应用程序作为例子，我将一步步展示我是如何创建它的。

## 步骤 1 -创建桥

[![bridge](img/d042639249ca9cb6b2babdb29af51c4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2V5iH_xS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/behlwr5w32atbrjqxcx7.gif)

<figcaption>Bridging</figcaption>

为了全面理解，我将简要介绍如何在 RN 中实现到本机模块的桥。如果你喜欢自动化过程或者想要一个更简单的方法，我推荐使用[react-native-create-bridge](https://github.com/peggyrayzis/react-native-create-bridge#react-native-create-bridge)或者如果你非常熟悉这个步骤，你可以直接跳到步骤 2。

在实际环境中，网桥是 RN 层和本地层之间的一种通信方式。正如文档所说，要创建一个桥，首先你必须提供一个**包**，负责在 RN 层注册你的模块或 UI 模块(这里没有用到)。如下面的类所示，它实例化了将 react 上下文作为参数传递的模块。

```
// HeartbeatPackage.java

public class HeartbeatPackage implements ReactPackage {

    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        return Arrays.<NativeModule>asList(
                new HeartbeatModule(reactContext)
        );
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
} 
```

**模块**本身负责定义本地层中 RN 层可用的方法和道具。要公开一个 Java 方法，必须使用 *@ReactMethod* 对其进行注释，并且返回将总是 *void* 。桥是异步的，所以将结果传递给 RN 层的唯一方法是使用回调或发出事件。

```
// HeartbeatModule.java

public class HeartbeatModule extends ReactContextBaseJavaModule {

    public static final String REACT_CLASS = "Heartbeat";
    private static ReactApplicationContext reactContext;

    public HeartbeatModule(@Nonnull ReactApplicationContext reactContext) {
        super(reactContext);
        this.reactContext = reactContext;
    }

    @Nonnull
    @Override
    public String getName() {
        return REACT_CLASS;
    }

    @ReactMethod
    public void startService() {
        // Starting the heartbeat service
        this.reactContext.startService(new Intent(this.reactContext, HeartbeatService.class));
    }
} 
```

要完成这座桥，只需要把它连接到两边。在 android 文件夹中用 RN 项目自动创建的*MainApplicaiton.java*中，您必须在 *getPackages* 方法中实例化新模块。一旦你这样做了，模块的方法和道具就可用了，你可以像下面这样导入和使用。

```
// Heartbeat.js

import { NativeModules } from 'react-native';

const { Heartbeat } = NativeModules;

export default Heartbeat; 
```

**重要**:模块名必须与模块类中 REACT_CLASS 变量定义的名称相同。

## 步骤 2 -创建服务

如前所述，服务用于执行与应用程序主线程分离的任务。在本例中，创建的服务在启动时负责在特定的时间间隔内发送心跳事件。在 android 中有一些实现间隔的方法，但是这里我使用了一个[处理程序](https://developer.android.com/reference/android/os/Handler)来处理一个 runnable 对象并每 2 秒发送一次事件。*rctdeviceventmitter*可以向 RN 层发送事件信号，并且可以直接从 react 上下文中获得，如下所示。

```
public class HeartbeatService extends Service {
    ...
    private Handler handler = new Handler();
    private Runnable runnableCode = new Runnable() {
        @Override
        public void run() {
            context.getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class).emit("Heartbeat",null);
            handler.postDelayed(this, 2000);
        }
    };
    ...
} 
```

android 服务的文档非常清楚地说明了 Android 的系统管理器对你的服务的影响。如果它发现您的设备处于关键操作状态，如内存不足或电池电量不足，它会终止一些服务来释放这些资源。为了防止这种情况发生，你必须把你的服务变成一个[前台](https://developer.android.com/guide/components/services#Foreground)类型，这样做的话，一个通知必须固定在设备的状态栏上。这样，通知被包含在服务类的 *onStartMethod* 中，如下所示。

```
 @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        this.handler.post(this.runnableCode); // Starting the interval
        // Turning into a foreground service
        Intent notificationIntent = new Intent(this, MainActivity.class);
        PendingIntent contentIntent = PendingIntent.getActivity(this, 0, notificationIntent, PendingIntent.FLAG_CANCEL_CURRENT);
        Notification notification = new NotificationCompat.Builder(this, CHANNEL_ID)
                .setContentTitle("Heartbeat service")
                .setContentText("Running...")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentIntent(contentIntent)
                .setOngoing(true)
                .build();
        startForeground(SERVICE_NOTIFICATION_ID, notification);
        return START_STICKY;
    } 
```

**重要的**:为了让服务工作，还需要在 **AndrodManifest.xml** 中声明。[见此](https://developer.android.com/guide/components/services#Declaring)。

## 步骤 3 -创建广播接收器

一个 [BroadcastReceiver](https://developer.android.com/guide/components/broadcasts) ，顾名思义，它是一个响应式组件，可以从 android 系统或其他应用程序广播消息，类似于发布-订阅设计模式。在 android 系统中有许多可用的广播消息，但在我们的例子中，最重要的是 **BOOT_COMPLETED** 。它告诉我们，设备经历了重新启动操作，这个过程已经结束。

通过侦听此特定消息，我们的广播接收器将能够在设备重启过程完成后的几秒钟内重新启动之前创建的服务，这将继续发送心跳事件。实现的代码如下所示。

```
// BootUpReceiver.java

public class BootUpReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        context.startService(new Intent(context, HeartbeatService.class));
    }
} 
```

在 **AndrodManifest.xml** 中，还需要声明广播接收器并定义它将侦听的消息。还必须提供阅读此类邮件的相应权限。

```
<manifest...>
    ...
    android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    ...
    <application...>
        ...
        <receiver
            android:name="com.rnheartbeat.BootUpReceiver"
            android:enabled="true"
            android:permission="android.permission.RECEIVE_BOOT_COMPLETED">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </receiver>
     </application>
</manifest> 
```

## 步骤 4 -创建应用程序

当然，这是本教程中最简单的一步，但同时也是最令人兴奋的一步。它显示了到目前为止所做的一切的结果。在 RN 层，我们需要实现一个组件来进行交互，并向用户显示心跳事件，我认为最适合此事件的示例是心跳动作，所以让我们开始吧。

[![Heartbeat](img/2dab7447879f66452f48bd713f35e37e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3I0NNYiR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gicupktjk7q983quqa20.gif)

<figcaption>Heartbeat</figcaption>

在 RN 项目的 *App.js* 中，我创建了一个简单的组件，如下图所示。利用[反应钩子](https://reactjs.org/docs/hooks-intro.html)的*使用状态*和*使用效果*提供的好处，注册一个监听器，每个心跳事件都会改变心脏图像的宽度和高度。很简单，对吧？此外，按钮组件的按下从本机层调用 *startService* 方法。

```
const App = () => {
  const [heartBeat, setHeartBeat] = useState(false);

  useEffect(() => {
    DeviceEventEmitter.addListener('HeartBeat', () => {
      console.log('Receiving heartbeat event');
      setHeartBeat(true);
      setTimeout(() => {
        setHeartBeat(false);
      }, 1000);
    });
  });

  const imageSize = heartBeat ? 150 : 100;
  return (
    <View style={styles.container}>
      <View style={styles.view}>
        <Image source={heart} style={{ width: imageSize, height: imageSize }} resizeMode="contain" />
      </View>
      <View style={styles.view}>
        <TouchableOpacity style={styles.button} onPress={() => Heartbeat.startService()}>
          <Text style={styles.instructions}>Start</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
}; 
```

最后，是时候测试我们的应用程序了。正如本文开头所评论的，要在我们的测试中获得批准的应用程序，必须处理它的 4 种状态(前台、后台、关闭或未启动)中的事件。在第一种状态下，当应用程序显示给用户时，它的表现如下图所示。当服务启动时，设备的状态栏会弹出一个通知。

[![Running](img/5bba95306f40a11f2a38618a82265c4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y46_VYQc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r1v46zoup34d4lla0d4.gif)

或许，你想知道我们如何在应用程序的其他状态中看到事件的处理。这时我们在项目的根文件夹中运行命令 **react-native log-android** 。在命令行中运行它，我们可以看到当应用程序在后台或关闭时由服务执行的 *console.log* ，如下所示。

[![Console.log](img/f347035b7aeef434464f775cbe2f220c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WoHcfn5x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rraevjlq2jpyhbvvom2e.png)

好了，现在我们看到应用程序运行良好，但是应用程序未启动时的测试丢失了。我们预计广播接收器将再次启动服务，并继续向 RN 层生成心跳事件，我们可以再次看到 *console.log* 消息。但令我们惊讶的是，这并没有发生。令人沮丧对吗？

[![Frustrating](img/184d87d712b97799113e0460eb2b86f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kg8VojIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n804clzl8mx0o2i03tsb.gif)

当设备重启并发送广播消息时，在本地层创建的服务开始发送事件。但是，RN 层没有任何动作来接收这些事件。此时，当服务试图访问不存在的 React 上下文时，我们有一个 *NullPointerException* 。为了解决这个问题，是时候使用 React API 提供的一个很棒的工具了:Headless JS。

## 步骤 5 -创建无头服务

Headless JS 是一种 RN 服务，它能够封装一些 javascript 操作，并独立于应用程序执行它们，就像 android 服务一样。无头服务可以做任何类型的操作，除了 UI 操作。在实际的例子中，headless 服务可以替换掉*rctdeviceventmitter*，去掉了在本地层中对 React 上下文的直接使用。

首先，在 *HeartbeatService* 类中，我们用无头服务 start 替换事件发射器，如下所示。当我们这样做时，服务将执行操作，并进入“暂停”模式，直到两秒钟后再次启动。

```
public class HeartbeatService extends Service {
    ...
    private Handler handler = new Handler();
    private Runnable runnableCode = new Runnable() {
        @Override
        public void run() {
            // context.getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class).emit("Heartbeat",null);
            Context context = getApplicationContext();
            Intent myIntent = new Intent(context, HeartbeatEventService.class);
            context.startService(myIntent);
            HeadlessJsTaskService.acquireWakeLockNow(context);
            handler.postDelayed(this, 2000);
        }
    };
    ...
} 
```

现在我们创建如下所示的 *HeartbeatEventService* 。您可以注意到，我们不使用 React 上下文，所以服务有自己的上下文。设置中的事件名称必须与之前定义的名称相同。

```
//HeartbeatEventService

public class HeartbeatEventService extends HeadlessJsTaskService {
    @Nullable
    protected HeadlessJsTaskConfig getTaskConfig(Intent intent) {
        Bundle extras = intent.getExtras();
        return new HeadlessJsTaskConfig(
                "Heartbeat",
                extras != null ? Arguments.fromBundle(extras) : null,
                5000,
                true);
    }
} 
```

无头服务必须在 [AppRegistry](https://facebook.github.io/react-native/docs/appregistry) 上注册，正如它的文档所说，每个应用程序注册表都应该在 require 序列的早期被要求，以确保 JS 执行环境在其他模块被要求之前被设置。在 *index.js* 中我们像下面这样调用注册表。

```
// index.js

AppRegistry.registerHeadlessTask('Heartbeat', () => MyHeadlessTask); 
```

现在， *MyHeadlessTask* 负责通过一个状态来改变心脏图像的宽度和高度。因为我们这里有一个与应用程序组件共享的集中状态，所以我决定使用 [Redux](https://redux.js.org/) 来代替钩子。这样，只需要创建 redux 的[基本结构](https://github.com/mathias5r/rn-heartbeat/blob/master/store.js)(动作、减速和存储)来正确设置状态。下面显示了 *MyHeadlessTask* 方法。

```
const MyHeadlessTask = async () => {
  console.log('Receiving HeartBeat!');
  store.dispatch(setHeartBeat(true));
  setTimeout(() => {
    store.dispatch(setHeartBeat(false));
  }, 1000);
}; 
```

当触发心跳时，传递给应用程序的状态会改变，重新呈现整个组件。如果我们再次测试重启过程，我们可以看到，即使在这种状态下，服务也能够再次启动并触发事件。哦耶！就是这么做的。

[![Done](img/17ed811ae6d654b0cf8c0fcf03401961.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1EdXGuwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/58wj9hwf6ln8pwhtijlo.jpeg)

## 结论

到目前为止，我们已经在 React Native 中完成了一项势不可挡的服务，集成了一个原生 android 模块，并使用了一些重要的工具，如广播接收器、headless JS 等等。对于未来的工作，挑战是在 iOS 系统上做同样的事情，该系统对在应用程序关闭或未启动的情况下执行任务有一些限制。

这个例子的所有代码你可以在
中看到 [mathias5r/rn-heartbeat](https://github.com/mathias5r/rn-heartbeat)