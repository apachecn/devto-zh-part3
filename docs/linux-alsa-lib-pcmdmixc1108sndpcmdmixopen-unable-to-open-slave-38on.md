# Linux:ALSA lib PCM _ dmix . c:1108:(snd _ PCM _ dmix _ open)无法打开从机

> 原文：<https://dev.to/setevoy/linux-alsa-lib-pcmdmixc1108sndpcmdmixopen-unable-to-open-slave-38on>

[![](img/68333939c95a5939a0f84006f4da18c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8fVOoVHf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2011/08/linux-logo.png) 有一个新鲜的 Arch Linux 系统设置。

经过 Viber 的一些修复(见 [Viber:无连接–Qt . network . SSL:OpenSSL 不兼容版本](https://rtfm.co.ua/en/viber-no-connection-qt-network-ssl-incompatible-version-of-openssl/)帖子)——声音系统有时会停止工作。

接下来看起来是这样的:运行`qmmp`，按*播放*，然后:

```
...
DecoderMAD: Fixed rate detected
DecoderMAD: Total time: 297900
ReplayGain: disabled
Dithering: disabled
ALSA lib pcm\_dmix.c:1108:(snd\_pcm\_dmix\_open) unable to open slave
OutputALSA: Error opening PCM device default
OutputWriter: unable to initialize output
StateHandler: Current state: FatalError; previous state: Stopped
DecoderMAD: deleting input\_buf
OutputALSA: setupMixer()
OutputALSA: setupMixer() success
StateHandler: Current state: Stopped; previous state: FatalError
... 
```

### 解

检查音频设备和驱动程序:

```
$ lspci -knn|grep -iA2 audio
00:1b.0 Audio device [0403]: Intel Corporation 8 Series/C220 Series Chipset High Definition Audio Controller [8086:8c20] (rev 05)
Subsystem: ASUSTeK Computer Inc. 8 Series/C220 Series Chipset High Definition Audio Controller [1043:8576]
Kernel driver in use: snd\_hda\_intel
Kernel modules: snd\_hda\_intel
--
01:00.1 Audio device [0403]: NVIDIA Corporation GP107GL High Definition Audio Controller [10de:0fb9] (rev a1)
Subsystem: ASUSTeK Computer Inc. GP107GL High Definition Audio Controller [1043:8613]
Kernel driver in use: snd\_hda\_intel
Kernel modules: snd\_hda\_intel 
```

创建 [`/etc/modprobe.d/default.conf`](https://linux.die.net/man/5/modprobe.conf) 文件:

```
options snd_hda_intel index=1 
```

重启系统。

然后以 [`speaker-test`](https://manpages.debian.org/testing/alsa-utils/speaker-test.1.en.html) 为例检查音频:

```
$ speaker-test -c2
speaker-test 1.1.8
Playback device is default
Stream parameters are 48000Hz, S16\_LE, 2 channels
Using 16 octaves of pink noise
Rate set to 48000Hz (requested 48000Hz)
Buffer size range from 96 to 1048576
Period size range from 32 to 349526
Using max buffer size 1048576
Periods = 4
was set period\_size = 262144
was set buffer\_size = 1048576
0 - Front Left
1 - Front Right
^CTime per period = 10.940188 
```

完成了。

### 类似的帖子

*   <small>02/28/2019</small>[Linux:ALSA lib PCM _ dmix . c:1108:(snd _ PCM _ dmix _ open)无法打开从机](https://rtfm.co.ua/linux-alsa-lib-pcm_dmix-c1108snd_pcm_dmix_open-unable-to-open-slave/) <small>(0)</small>
*   T003/29/2017 t1t2arch:SKYPE-T3T 4 音频和麦克风
*   <small>03/17/2017</small>[Arch:nm-applet service unknown:org . free desktop . secrets 这个名字不是任何人提供的。服务文件](https://rtfm.co.ua/arch-nm-applet-serviceunknown-the-name-org-freedesktop-secrets-was-not-provided-by-any-service-files/) <small>(0)</small>
*   <small>05/06/2017</small>[Arch Linux:запускаем暗黑破坏神 2](https://rtfm.co.ua/arch-linux-zapuskaem-diablo-2/) <small>(3)</small>
*   <small>04/20/2017</small> [拱门:VNC-клиент](https://rtfm.co.ua/arch-vnc/) <small>(0)</small>