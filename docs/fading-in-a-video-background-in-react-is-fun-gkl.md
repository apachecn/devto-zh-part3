# 在 React 中淡入视频背景很有趣！

> 原文：<https://dev.to/ma5ly/fading-in-a-video-background-in-react-is-fun-gkl>

使用视频作为背景很有趣。它允许迷人的网站设计。不幸的是，视频占用了大量的内存，这意味着它们比你的 HTML、Javascript 和 CSS 需要更多的时间来加载。因此，当你的访问者已经在网站上安顿下来后，视频可能会突然在背景中弹出，稍微破坏了它应该有的优雅效果。为了解决这个问题，我们可以在视频上使用一个短暂的淡入。

[![Fading in a video](img/75149b55d8555c5f2de8c3ae46885c42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MHx4BocZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dmotxp1f8ycwaxyo4i7l.gif)

*要充分欣赏这种效果，请看它在行动中的实况[这里](https://bybessy.com)T3】*

我们将在 React 中这样做，因为 Gatsby 非常受欢迎(理应如此)，但在普通 Javascript 中应该不会更难；如果不是更容易。先说视频本身。如果你有一个 React 组件，你可以在你的`JSX`中加入视频标签。

```
class MyCoolWebsite extends React.Component {
  render() {
    return (
      <video
        autoPlay
        muted
        loop
        style={{
          position: "fixed",
          width: "100%",
          left: 0,
          top: 0
        }}
      >
        <source src="urlOfMyVideo.mp4" type="video/mp4" />
      </video>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，我添加了一点内嵌样式，使视频出现在整个屏幕上。你可以把这种风格改成任何适合你的。也有一些视频属性，分别使视频自动播放，静音视频可能有的声音，并循环到永恒。接下来，我们在视频元素上添加一个`ref`。

```
 return (
      <video
        ref={ref => (this.video = ref)}
        autoPlay
        muted
        loop
        style={{
          position: "fixed",
          width: "100%",
          left: 0,
          top: 0
        }}
      >
        <source src="urlOfMyVideo.mp4" type="video/mp4" />
      </video>
    ); 
```

Enter fullscreen mode Exit fullscreen mode

这里的想法是，一旦组件被安装，视频将开始加载，这意味着我们需要以某种方式挂钩到该进程。我们刚刚放置的`ref`帮助了我们。我们可以访问它，然后我们可以向它添加一个事件侦听器。

```
 componentDidMount() {
    if (this.video) {
      this.video.addEventListener("loadeddata", () => {
        // execute code here
      });
    }
  }

  componentWillUnmount() {
    if (this.video) {
      this.video.removeEventListener("loadeddata", () => {
      });
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个特定的侦听器将在视频加载后执行一段代码，这正是我们需要的。为了保持整洁，一旦我们不再需要这个组件，我们也会删除事件监听器。

现在是淡入本身。我们将设置这个的方法，是通过使用一点 CSS 来改变视频的加载。更具体地说，我们最初将设置视频的不透明度为 0，然后一旦它完成加载，我们将设置不透明度为默认值 1。过渡属性将把它变成一个缓慢的淡入淡出(取决于你设置的秒数)。

但在此之前，我们首先需要让我们的样式意识到正在进行的加载。我们可以利用`state`来达到这个目的。希望您还记得，我们刚刚设置了一个事件监听器。在那个监听器中，我们将改变我们的默认加载`state`，我已经将它设置为`true`。

```
 state = {
    loading: true
  };
  componentDidMount() {
    if (this.video) {
      this.video.addEventListener("loadeddata", () => {
        this.setState({ loading: false });
      });
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

有了这个效果，我们就可以把我们刚刚谈到的风格联系起来。最有趣的部分显然是 opacity 属性，它有一个三元运算符来根据我们的组件状态动态更改不透明度，这反过来反映了视频的加载状态。我希望这有意义。

```
 return (
      <video
        autoPlay
        muted
        loop
        style={{
          position: "fixed",
          width: "100%",
          left: 0,
          top: 0,
          opacity: this.state.loading ? 0 : 1,
          transition: "opacity, 2s ease-in-out"
        }}
      >
        <source src="urlOfMyVideo.mp4" type="video/mp4" />
      </video>
    ); 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，我们就快完成了。现在，您可以在应用程序中设置常规背景，然后在加载后让视频背景在该背景上淡入淡出。相当不错的效果，我希望你用它做出漂亮的东西！