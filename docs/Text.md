# Vaya 该组件不存在用于 React-Native，我们没有时间开发它吗？(Android 第一部分)

> [https://dev . to/x0 S3/vaya-El-component-no-existent-to-react-native-y-no-we have time-to-develop it-what we do-Android-part-I-32 df](https://dev.to/x0s3/vaya-el-componente-no-existe-para-react-native-y-no-tenemos-tiempo-de-desarrollarlo-que-hacemos-android-parte-i-32df)

读者群！首先，感谢您点击我的第一封邮件，我相信您会喜欢这里的内容，并对您有所帮助。

谁没有经历过使用 React-Native 开发应用程序时，在“js coach”中找不到该组件，而该组件正是您的项目所需要的，而且您没有时间自己开发该组件？(此处为服务器)

RN 最让我难以理解的一点是，我们不需要把它全部委托给 Javascript 部分，使用这个框架的优点是我们可以去本地世界，带给我们想要的！

[![Awesome](img/e7d448037d8db9061b32518bd748f28f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--imVT0nT1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/3ohzdIuqJoo8QdKlnW/giphy.gif%3Fcid%3D790b76115ca90e6a412e4b674d9705f5)

所以对于这第一部分和第一篇文章，我们将前往本土，带来一个组成部分[安卓武库](https://android-arsenal.com)。

这不是在 Android 上按按钮的典型例子，因为我们已经有了框架，要想接触到我们可以从另一个世界带来的一切，我们首先要带一个简单的组件，因为我们的项目迫切要求我们实施刮脸，赢得胜利。不要惊慌失措，因为我们很快就解决了这个问题

首先，我们将在项目中添加一个库，以便于创建本地文件:t0]

```
yarn add react-native-create-bridge 
```

一经安装，我们就这样称呼它:

```
react-native new-module 
```

控制台将向我们显示一系列选择，具体取决于我们要创建的内容，在本例中，它将是一个本机组件，我们将以 Java 编写您要编写的内容。

[![New-Module](img/d3a38c2824136495cd9ce909f353d86d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZsMOZnYw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e9b1pty291u8jb216kbq.png)

完成此操作后，我们的目录中将有一个. js 文件，其名称与我们的组件(在本例中为 NativeScratchCard.js)的名称相同。如果转到 Android 目录，您将看到自动存储塔为我们生成了一个名为 NativeScratchCard 的包，其中我们有两个 Java 文件。

在我们做任何其他事情之前，我们先进口将使我们免于这种困境的原生书店，在我们的例子中，我们将使用同样的 [Scratch Card](https://android-arsenal.com/details/1/7597)

(是的，我知道了，从现在起，我们几乎可以使用任何我们已经想做的 jeje) [![ITS FREE](img/7eae360285facb79dfd2ab43dcee1fde.png)](https://i.giphy.com/media/TlK63Eu7Dwok2j8TgfS/giphy.gif)

我们将依赖项添加到我们的应用程序中，同步并准备好，我们可以开始引入此组件。

```
dependencies {
    implementation 'in.codeshuffle.scratchcardlayout:ScratchCardLayout:1.0.3'
    implementation fileTree(dir: "libs", include: ["*.jar"])
    implementation "com.android.support:appcompat-v7:${rootProject.ext.supportLibVersion}"
    implementation "com.facebook.react:react-native:+"  // From node_modules
} 
```

我们转向***【native scratchcardmanager . Java】***并将对生成的代码做一些修改，首先是更改我们扩展的类的类型，从而告诉我们亲爱的 rn，我们将带来 ScratchCardLayout 组件，而不是简单的视图。

```
View -> ScratchCardLayout 
```

我们还将更改 createViewInstance 方法，如下所示:

```
@Override
public ScratchCardLayout createViewInstance(ThemedReactContext context) {
        Log.d(REACT_CLASS, "createViewInstance");
        return new ScratchCardLayout(context);
    } 
```

通过阅读本机库，我们可以看到，我们有多种方法可以修改 ScratchCard，我们将展示 3 个属性，以便在 rn 中的组件中使用:画笔大小、完成百分比以及是否启用该组件。

[![React Props](img/49c4788b1a1e7bcaca3bca45b9ae5fb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M-O9LP4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kzmgiqg7e6qzbojgc3dp.png)

我们快到了！ [![Ansiosos](img/22a089c4a9c2594750e57218857e8423.png)](https://i.giphy.com/media/ToMjGpz81S7usvTIM8w/giphy.gif)

现在，仔细想想，我们发现我们要在这个本机组件中公开我们用 Javascript 编写的组件，然后去-我...。似乎没有什么教程来解释如何实现这一点──如果我们要把东西带到本土，就必须知道或至少对 Android 有所了解，但这并没有什么关系，因为我将在这里简单地展示如何在这第一篇帖子中做到这一点(并感谢[瑜伽](https://yogalayout.com/)这是可能的。

为此，我们将更改我们的组件类
扩展的类

```
SimpleViewManager -> ViewGroupManager 
```

并且我们将改写方法**【add view】**

```
 @Override
    public void addView(ScratchCardLayout parent, View child, int index) {
        Log.d(REACT_CLASS, "addView");
        parent.addView(child);
    } 
```

我们只需在 js 中向我们的组件公开本机组件事件，我们就已经玩 Java 了。

为了不致过于冗长的帖子这一部分在 [RN](https://facebook.github.io/react-native/docs/native-components-android#events) 的官方文件中做了很好的解释(放心，也附了我们的代码捕获)

[![Native Events](img/06c374ba59e18b87872e0270742ca719.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SNr-iH7K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ybfun0i6ivqomnsmqxbp.png)

让我们回到我们的方法**【createviewinstance】**上，对其进行修改，使之暴露出本土事件。

```
 @Override
    public ScratchCardLayout createViewInstance(@NonNull ThemedReactContext context) {
        Log.d(REACT_CLASS, "createViewInstance");
        ScratchCardLayout card = new ScratchCardLayout(context);
        onReceiveNativeEvent(context, card);
        card.setScratchDrawable(context.getResources().getDrawable(R.drawable.rn_image_scratch));
        return card;
    } 
```

那我们是不是已经到了-我...。为了能够公开和使用，我们需要将软件包添加到我们的 MainApplication

```
 @Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                    new MainReactPackage(),
                    new NativeScratchCardPackage()
            );
        } 
```

现在我们准备好开始写一些 Javascript 了。

我们将修改我们以前生成的文件，使其如下:

```
import React, { useCallback } from 'react';
import { requireNativeComponent } from 'react-native';

const ScratchCardView = requireNativeComponent('NativeScratchCard');

function ScratchCardComponent(props) {
  const onChange = useCallback(event => {
    switch (Object.keys(event.nativeEvent)[0]) {
      case 'started':
        props.onChange({ type: 'STARTED' });
        break;

      case 'progress':
        props.onChange({
          type: 'PROGRESS',
          payload: {
            progress: event.nativeEvent.progress
          }
        });
        break;

      case 'finished':
        props.onChange({ type: 'FINISHED' });
        break;

      default:
        break;
    }
  }, []);

  return <ScratchCardView style={props.style} {...props} onChange={onChange} />; }

export default ScratchCardComponent; 
```

onChange 属性指的是我们本地代码中的 topChange，您可以在上面的事件链接中看到更多有关它的信息。

并且我们的 App.tsx 文件可以保持原样:

```
const initialState = { started: false, finished: false, progress: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'STARTED':
      return { ...state, started: true };
    case 'FINISHED':
      return { ...state, finished: true, progress: 100 };
    case 'PROGRESS':
      return { ...state, progress: action.payload.progress };
    default:
      throw new Error();
  }
}

export default function App() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <View style={styles.container}>
      <Text style={styles.text}>
        Scratch started? {state.started ? 'Yep :)' : 'Nop :('}
      </Text>
      <Text style={styles.text}>
        Is finished? {state.finished ? 'Yep :)' : 'Nop :('}
      </Text>
      <Text style={styles.text}>Current progress: {state.progress}</Text>
      <ScratchCard
        onChange={dispatch}
        style={{ width: 300, height: 300 }}
        finishAt={100}
        brushWidth={20}
        enabled={true}
      >
        <Text style={styles.welcome}>HEY IM YOUR JSX COMPONENT!!</Text>
      </ScratchCard>
    </View>
  );
} 
```

如果我们编译好应用程序之后一切都好的话我们应该可以看到这个组件了

[![RN Component](img/bcb48099e40029cb295d72397824fdee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZA64B56--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gjimjf5d8xr5zpbv4x2k.png)

并且工作正常

[![RN Component 2](img/7ee7af6973b326436b4cce65c712d49a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9U0uCMwy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5r217jxhr0q0u7dcbelx.png)

一切正常，现在我们可以使用本机组件和 js 组件！！

[![HAPPY](img/0fd582c686f84cbeea50d6d3773b47e5.png)](https://i.giphy.com/media/xT9DPlS09LdhDM5Fja/giphy.gif)

谢谢。如果你到这里来，我希望你没有太重，希望你从这篇文章中学到一些新的东西，任何疑问或建议，请随时告诉我！！

Link al repo de github: [Repo](https://github.com/x0s3/scratchNativeCard)