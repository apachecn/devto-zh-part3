# 组装了托福键盘

> 原文：<https://dev.to/jgs/tofu--495o>

[![](img/3e2faa25658664da5abf40cfe8104492.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFLMdTxw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iahg4votqnz28dm09lvw.jpg)

[https://kbd fans . cn/collections/DIY-kit/products/豆腐-hhkb-layout-hot-swap-DIY-kit](https://kbdfans.cn/collections/diy-kit/products/tofu-hhkb-layout-hot-swap-diy-kit)

买了 KBDFans 的 Tohu HHKB Layout 的光盘工具包。 游戏主机 Windows 机一直使用的是 Majestouch 的蓝轴，但是买的是使用 JIS 键盘的时候在自己的外设上使用的最后一个 JIS 键盘。 手几乎忘记了 JIS 的排列，压力很大，所以想换成 US 排列的键盘。

60%的键盘群雄割据，在种类繁多的情况下选择托福，大概是因为 PCB 是热插拔的，板块上有 HHK 排列的。 用 Aliexpress 下单后比想象中到得早。 五天左右。

首先组装稳定器

[![](img/7f4d8daecf569c808bd548d56ff661ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bfAQRuVu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w3oazsbcf9sg7b1cuia9.jpg)

之后忘记拍照了，什么都没有，只是把键帽嵌在板上插入 PCB。 右上角很紧，很难插入。

开关是游舍工坊先生，选择了 Kailh Speed Rose Pink。

[https://yushakobo.jp/shop/kailh-speed/](https://yushakobo.jp/shop/kailh-speed/)

PCB 上有 LED，打开 LED 后很漂亮，所以犹豫着要不要选清晰的开关，但是重视家用和实用。 因为是热交换，所以之后可以换。

因为说可以使用 QMK 的固件，所以想用 Mac 写，但是没能成功。

[https://kbdfans.cn/pages/qmk-instructions](https://kbdfans.cn/pages/qmk-instructions)

参考，在 Windows 上构建了环境。 因为 qmkeyboard 的 GUI 的编辑器不好用，所以用 Mac 构建了 hex，用 Dropbox 在 Windows 上共享写入。 在 GUI 的编辑器中输入软复位的键的话，就不需要打开后盖按开关了，所以在 hex 中一定要输入软复位的键，非常方便。

确认按下了什么键是为了

[http://keyboardchecker.com/](http://keyboardchecker.com/)

用了筷子。 简单易用。

基本上是参考 HHK 的键配置的同时，在图层上用 wsad 进行箭头插入，在图层的左右 Shift 上配置变换/无变换键，在 MS-IME 的设定中按下变换/无变换后，事先设定 IME 的 on/off，就像 Mac 一样决定字母数/假名

另外，在 Windows 的设定中，即使插入 US 的键盘，也可以作为 JIS 的键盘来处理，所以从 Windows 的设定开始，从“时间和语言”的地方将键盘的布局变更为 US，就可以直接作为 US 的键盘输入。

现在的麻烦点是，PCB 的左右有 USB-C 的端口，右边很不稳定，想向左插，但是电缆不够长，只能勉强向右插(想在网络星期一买电缆的胆量……)，稳定器的列举 在应急措施中安装了 1.5u 左右的键帽，但是空间有点远，不方便... 我一直在想，因为手头 7u 的键帽只有 Pimp 的 Grabbag 上的那个，所以键帽是不是歪了？怎么看都觉得稳定器不在按键开关的平行线上，安装错了吗... 想一步一步地改正。

之后，几乎同时组装的 crkbd 也接触到了，开始觉得不需要多个 u 的键，所以像 Planck 这样的只有 1u，60%那样的键盘也许也不错。 但是，因为没有使用过 Soliner，所以要是有 Normal staggered 的 40-60%就好了。 也许有 Zinc 的数字串是理想的。