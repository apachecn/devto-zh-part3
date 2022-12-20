# 屏幕记录器:同时记录麦克风和桌面音频

> 原文：<https://dev.to/chromiumdev/screen-recorder-recording-microphone-and-the-desktop-audio-at-the-same-time-4c0o>

我的目标是构建世界上最简单的屏幕录制软件，在过去的几个月里，我一直在慢慢地研究这个项目(我的意思是真的很慢)。

在以前的帖子中，我通过对来自所有输入源的流进行模糊处理，得到了[屏幕记录和一个声音叠加图](https://dev.to/chromiumdev/building-a-video-editor-on-the-web-part-01---screencast-50li-temp-slug-1927058)。一个令人沮丧的地方是，我不知道如何从桌面上获得音频*和*覆盖扬声器的音频。我终于找到了做这件事的方法。

首先，Chrome 中的`getDisplayMedia`现在允许音频捕获，规范中似乎有一个奇怪的疏忽，它不允许你在函数调用中指定`audio: true`，现在你可以了。

```
const audio = audioToggle.checked || false;
desktopStream = await navigator.mediaDevices.getDisplayMedia({ video:true, audio: audio }); 
```

其次，我原本以为通过在音频流中创建两个轨道，我将能够得到我想要的，然而我得知 Chrome 的`MediaRecorder` API 只能输出一个轨道，第二，它无论如何都不会工作，因为轨道就像 DVD 多音轨一样，一次只能播放一个。

对很多人来说，解决方案可能很简单，但对我来说很新鲜:使用网络音频。

原来 WebAudio API 有`createMediaStreamSource`和`createMediaStreamDestination`，两者都是解决问题所需的 API。`createMediaStreamSource`可以从我的桌面音频和麦克风中获取流，通过将这两者连接到由`createMediaStreamDestination`创建的对象中，它给了我将这一个流传输到`MediaRecorder` API 的能力。

```
const mergeAudioStreams = (desktopStream, voiceStream) => {
  const context = new AudioContext();

  // Create a couple of sources
  const source1 = context.createMediaStreamSource(desktopStream);
  const source2 = context.createMediaStreamSource(voiceStream);
  const destination = context.createMediaStreamDestination();

  const desktopGain = context.createGain();
  const voiceGain = context.createGain();

  desktopGain.gain.value = 0.7;
  voiceGain.gain.value = 0.7;

  source1.connect(desktopGain).connect(destination);
  // Connect source2
  source2.connect(voiceGain).connect(destination);

  return destination.stream.getAudioTracks();
}; 
```

简单。

完整代码可以在 [my glitch](https://glitch.com/edit/#!/screen-record-voice) 上找到，演示可以在这里找到:【https://screen-record-voice.glitch.me/】T2