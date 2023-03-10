# fc-白板、网络白板截屏

> 原文：<https://dev.to/wxyyxc1992/fc-whiteboard-web-whiteboard-screencasting-2b42>

# fc-白板

带有背景幻灯片的 Web 白板屏幕广播(实时和回放模式)可用作在线辅导或远程协作的图形输入板。

[![](img/9fe5d10bc6b49f233894dcea18c5fbb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IIKoCLdK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/CLG53MF8/image.png)

# 用法

## 白板直播模式

[![](img/81935e02494e4efdef21bdfaedbfe811.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gW6FWXGX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.postimg.cc/65t7MNBQ/Kapture-2019-04-17-at-13-47-52.gif)

源代码可以在[代码沙箱](https://codesandbox.io/s/3q1z35q53p)或者[演示](https://3q1z35q53p.codesandbox.io/)中找到；

```
import { EventHub, Whiteboard, MirrorWhiteboard } from 'fc-whiteboard';

const eventHub = new EventHub();

eventHub.on('sync', (changeEv: SyncEvent) => {
  console.log(changeEv);
});

const images = [
  'https://upload-images.jianshu.io/upload_images/1647496-6bede989c09af527.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240',
  'http://upload-images.jianshu.io/upload_images/1647496-d281090a702045e5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240',
  'http://upload-images.jianshu.io/upload_images/1647496-611a416be07d7ca3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240'
];

const whiteboard = new Whiteboard(document.getElementById('root') as HTMLDivElement, {
  sources: images,
  eventHub,
  // Enable this option to disable incremental sync, just use full sync
  onlyEmitSnap: false
});

whiteboard.open();

const mirrorWhiteboard = new MirrorWhiteboard(
  document.getElementById('root-mirror') as HTMLDivElement,
  {
    sources: images,
    eventHub
  }
);

mirrorWhiteboard.open(); 
```

## 白板回放模式

```
import { ReplayWhiteboard } from 'fc-whiteboard';
import * as events from './events.json';

let hasSend = false;

const whiteboard = new ReplayWhiteboard(document.getElementById('root') as HTMLDivElement);

whiteboard.setContext(events[0].timestamp, async (t1, t2) => {
  if (!hasSend) {
    hasSend = true;
    return events as any;
  }

  return [];
});

whiteboard.open(); 
```

持续事件如下:

```
[  {  "event":  "borderSnap",  "id":  "08e65660-6064-11e9-be21-fb33250b411f",  "target":  "whiteboard",  "border":  {  "id":  "08e65660-6064-11e9-be21-fb33250b411f",  "sources":  [  "https://upload-images.jianshu.io/upload_images/1647496-6bede989c09af527.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240",  "http://upload-images.jianshu.io/upload_images/1647496-d281090a702045e5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240",  "http://upload-images.jianshu.io/upload_images/1647496-611a416be07d7ca3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"  ],  "pageIds":  [  "08e65661-6064-11e9-be21-fb33250b411f",  "08e6a480-6064-11e9-be21-fb33250b411f",  "08e6cb91-6064-11e9-be21-fb33250b411f"  ],  "visiblePageIndex":  0,  "pages":  [  {  "id":  "08e65661-6064-11e9-be21-fb33250b411f",  "markers":  []  },  {  "id":  "08e6a480-6064-11e9-be21-fb33250b411f",  "markers":  []  },  {  "id":  "08e6cb91-6064-11e9-be21-fb33250b411f",  "markers":  []  }  ]  },  "timestamp":  1555431837  }  ...  ] 
```

## 单独使用画板

```
<img id="root" src="https://upload-images.jianshu.io/upload_images/1647496-6bede989c09af527.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"></img> 
```

```
import { Drawboard } from 'fc-whiteboard/src';

const d = new Drawboard({
  imgEle: document.getElementById('root') as HTMLImageElement
});

d.open(); 
```

# 动机&学分

*   [标记](https://markerjs.com/)