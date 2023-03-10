# 核子上的乡村有机发光二极管

> 原文：<https://dev.to/nickray/rustic-oled-on-the-nucleo-1o0m>

`Actual date: 2018-12-17 and 2018-12-18`

由于我的 USB 设备的努力到目前为止还没有任何结果，我把我的努力转移到让有机发光二极管启动并运行上，仍然是在 STM32L4 平台上。最终，拥有一个带有集成显示器的[单键](https://solokeys.com/preorder/)会很好！

斯科特·马斌最近在他的 HAL 中加入了 I2C 支持，所以我很快就准备好了一切。它不起作用！！！！

好吧。首先，我买了一个非常便宜的烙铁，并做了一件可怕的工作(二十多年来的第一次焊接经验)将引脚连接到我的 0.91 英寸 128x32 有机发光二极管(带一个 [SSD1306](https://github.com/jamwaffles/ssd1306) 芯片)。尽管如此，还是不起作用！

然后，我发现带有相同芯片的 0.96 英寸 128x64 有机发光二极管已经预先焊接好了...再次，没有工作！

（...)

第二天，我终于听从了[康纳的](https://conorpp.com/)和[斯科特的](https://github.com/stm32-rs/stm32l4xx-hal/issues/24)建议，尝试了不同的 I2C 地址。快速搜索建议使用`0x78`(也就是 2* `0x3c`，所有的数据表都提到了)廉价的中国克隆，如我的，和 ta-daa！成功！

[![SoloKeys logo on OLED, courtesy of Rust](img/ca76e7f923254e02ec79b341721d4510.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xY6ffZSV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ii3804sqawlqo0cb2xfz.jpeg)

## 非立即成功的进一步结果

*   我想要这个烙铁
*   我几乎想要这个示波器
*   我看得太多了
*   我点了更多(便宜)的东西...
*   我的女朋友又叫我疯子教授了，:D