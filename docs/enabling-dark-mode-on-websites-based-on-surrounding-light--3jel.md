# 根据环境光线在网站上启用黑暗模式

> 原文：<https://dev.to/ananyaneogi/enabling-dark-mode-on-websites-based-on-surrounding-light--3jel>

现在你可以根据周围的光线在网站上切换“黑暗模式”和“正常模式”了！基于用户浏览你的网站的条件，它真的可以帮助策划一个更好的用户体验。

所以，你应该这么做-

添加简短的 javascript。

```
 if ( 'AmbientLightSensor' in window ) {
  const sensor = new AmbientLightSensor();
  sensor.onreading = () => {
    console.log('illuminance level :', sensor.illuminance);
      if(sensor.illuminance < 15) {
            document.querySelector('body').classList.add('dark');
      }
      else {
            document.querySelector('body').classList.remove('dark');
      }
  };
  sensor.onerror = (event) => {
    console.log(event.error);
  };
  sensor.start();
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，在你刚刚设置的类下为你的黑暗模式添加特定的 css。就像这里一样，我根据环境光的亮度添加和删除了类别`dark`。
您可以试验一下`illuminance`值，看看在您的情况下什么更好。

我还找到了[这个由](https://twitter.com/tomlokhorst/status/837037624003031040) [@tomlokhorst](https://twitter.com/tomlokhorst/) 做的很酷的实验，他使用可变字体根据环境光改变字体粗细。
然而，他在帖子中进一步提到，他没有使用前面提到的`AmbientLightSensor`，但它仍然是一个很好的用例。

* * *

*免责声明:*浏览器对该功能的支持度较低。
在 chrome 上，它只能在国旗下使用。查看[卡努斯](https://caniuse.com/#feat=ambient-light)的统计数据了解更多细节。不管网络的未来是光明的还是黑暗的，不管你喜欢它！:)

**编辑-**
如果你得到一个错误，类似于`AmbientLightSensor does not exist on type Window`在试图使这个工作有角度，[参考这个评论](https://dev.to/ananyaneogi/comment/8107)寻找解决方案。

## 引用

*   [环境光线传感器 W3C 规范](https://w3c.github.io/ambient-light/)
*   既然我提到了可变字体，[这里有一个有趣的阅读](http://www.alphabettes.org/responsive-variable-fonts/)