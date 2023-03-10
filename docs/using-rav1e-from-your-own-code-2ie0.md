# 从您自己的代码中使用 rav1e

> 原文：<https://dev.to/luzero/using-rav1e-from-your-own-code-2ie0>

## AV1，Rav1e，Crav1e，介绍

> AV1 是一款现代视频编解码器，由[许多](https://aomedia.org/membership/members/)不同的大型和小型多媒体公司联合推出。
> 我是 [VideoLan](https://videolan.org) 组织的一员，最近我在这个编解码器上花了不少时间。

* * *

> [rav1e](https://github.com/xiph/rav1e/) :最安全最快的 AV1 编码器，由众多志愿者和 Mozilla/Xiph 开发者打造。
> 用 [rust](https://rustlang.org) 写成，力求提供良好的速度、质量和可维护性。

* * *

> [crav1e](https://github.com/lu-zero/crav1e) :一个配套的[机箱](https://doc.rust-lang.org/book/crates-and-modules.html)，由[your truly](https://github.com/lu-zero)编写，提供了一个 C-API，所以编码器可以被 C 库和程序使用。

* * *

这篇文章将给出一个现在可用的 API 的快速概述，它主要是帮助人们开始使用它，并希望报告问题和难题。

## Rav1e API

当前的 API 是围绕以下 4 个结构和 1 个枚举构建的:

*   `struct Frame`:原始像素数据
*   `struct Packet`:编码后的比特流
*   `struct Config`:编码器配置
*   `struct Context`:编码器状态

* * *

*   `enum EncoderStatus`:由`Context`方法返回的致命和非致命条件。

### 配置

`Config`结构目前是简单构造的。

```
 struct Config {
        enc: EncoderConfig,
        threads: usize,
    } 
```

`EncoderConfig`存储对实际比特流有影响的所有设置，而`threads`等设置被保存在外部。

```
 let mut enc = EncoderConfig::with_speed_preset(speed);
    enc.width = w;
    enc.height = h;
    enc.bit_depth = 8;
    let cfg = Config { enc, threads: 0 }; 
```

> **注意**:上面的一些字段可能会被打乱，直到 API 被标记为稳定。

#### 方法

##### 新建 _ 上下文

```
 let cfg = Config { enc, threads: 0 };
    let ctx: Context<u8> = cfg.new_context(); 
```

它产生新的编码上下文。其中`bit_depth`是 *8* ，可以使用优化的`u8`码路径，否则必须使用`u16`。

### 上下文

它是由`Config::new_context`产生的，它的实现细节是隐藏的。

#### 方法

`Context`可分为**必需的**、**可选的**和**方便的**。

```
 // Essential API
    pub fn send_frame<F>(&mut self, frame: F) -> Result<(), EncoderStatus>
      where F: Into<Option<Arc<Frame<T>>>>, T: Pixel;
    pub fn receive_packet(&mut self) -> Result<Packet<T>, EncoderStatus>; 
```

编码器通过处理通过`send_frame`送入的每一个`Frame`并产生每一个可以被`receive_packet`取回的`Packet`来工作。

```
 // Optional API
    pub fn container_sequence_header(&mut self) -> Vec<u8>;
    pub fn get_first_pass_data(&self) -> &FirstPassData; 
```

根据容器格式，`AV1`序列头可以存储在外部数据中。`container_sequence_header`产生预格式化的数据，简单存储在`mkv`或`mp4`中。

`rav1e`支持多遍编码，通过调用`get_first_pass_data`可以检索第一遍的编码数据。

```
 // Convenience shortcuts
    pub fn new_frame(&self) -> Arc<Frame<T>>;
    pub fn set_limit(&mut self, limit: u64);
    pub fn flush(&mut self) { 
```

*   `new_frame()`根据上下文中的尺寸和像素格式信息生成一个帧。
*   `flush()`在功能上等同于调用`send_frame(None)`。
*   一旦`limit`帧被发送到编码器，`set_limit()`在功能上等同于调用`flush()`。

### 工作流程

工作流程如下:

1.  设置:

    *   准备一个`Config`
    *   从`Config`调用`new_context`产生一个`Context`
2.  编码循环:

    *   使用`receive_packet`拉动每个`Packet`。
    *   如果`receive_packet`返回`EncoderStatus::NeedMoreData`
        *   使用`send_frame`将每个`Frame`送入`Context`
3.  完成编码

    *   发布一个`flush()`以将每个未决的`Frame`编码成一个最终的`Packet`。
    *   调用`receive_packet`直到`EncoderStatus::LimitReached`返回。

## Crav1e API

除了几个关键的区别之外， [crav1e](https://github.com/lu-zero/crav1e) API 提供了相同的结构和特性:

*   `Frame`、`Config`和`Context`结构是*不透明的*。

```
typedef struct RaConfig RaConfig;
typedef struct RaContext RaContext;
typedef struct RaFrame RaFrame; 
```

*   公开的`Packet`结构比原来的`rav1e`结构简单得多。

```
typedef struct {
    const uint8_t *data;
    size_t len;
    uint64_t number;
    RaFrameType frame_type;
} RaPacket; 
```

*   编码状态包括一个`Success`条件。

```
typedef enum {
    RA_ENCODER_STATUS_SUCCESS = 0,
    RA_ENCODER_STATUS_NEED_MORE_DATA,
    RA_ENCODER_STATUS_ENOUGH_DATA,
    RA_ENCODER_STATUS_LIMIT_REACHED,
    RA_ENCODER_STATUS_FAILURE = -1,
} RaEncoderStatus; 
```

### RaConfig

因为配置是`opaque`的，所以有许多功能来组合它:

*   `rav1e_config_default`分配默认配置。
*   `rav1e_config_parse`和`rav1e_config_parse_int`为通过`key`字符串选择的特定字段设置特定的`value`。
*   `rav1e_config_set_${field}`是用于复杂信息如颜色描述的专门设置器。

```
RaConfig *rav1e_config_default(void);

/**
 * Set a configuration parameter using its key and value as string.
 * Available keys and values
 * - "quantizer": 0-255, default 100
 * - "speed": 0-10, default 3
 * - "tune": "psnr"-"psychovisual", default "psnr"
 * Return a negative value on error or 0.
 */
int rav1e_config_parse(RaConfig *cfg, const char *key, const char *value);

/**
 * Set a configuration parameter using its key and value as integer.
 * Available keys and values are the same as rav1e_config_parse()
 * Return a negative value on error or 0.
 */
int rav1e_config_parse_int(RaConfig *cfg, const char *key, int value);

/**
 * Set color properties of the stream.
 * Supported values are defined by the enum types
 * RaMatrixCoefficients, RaColorPrimaries, and RaTransferCharacteristics
 * respectively.
 * Return a negative value on error or 0.
 */
int rav1e_config_set_color_description(RaConfig *cfg,
                                       RaMatrixCoefficients matrix,
                                       RaColorPrimaries primaries,
                                       RaTransferCharacteristics transfer);

/**
 * Set the content light level information for HDR10 streams.
 * Return a negative value on error or 0.
 */
int rav1e_config_set_content_light(RaConfig *cfg,
                                   uint16_t max_content_light_level,
                                   uint16_t max_frame_average_light_level);

/**
 * Set the mastering display information for HDR10 streams.
 * primaries and white_point arguments are RaPoint, containing 0.16 fixed point values.
 * max_luminance is a 24.8 fixed point value.
 * min_luminance is a 18.14 fixed point value.
 * Returns a negative value on error or 0.
 */
int rav1e_config_set_mastering_display(RaConfig *cfg,
                                       RaPoint primaries[3],
                                       RaPoint white_point,
                                       uint32_t max_luminance,
                                       uint32_t min_luminance);

void rav1e_config_unref(RaConfig *cfg); 
```

最低设置代码如下:

```
 int ret = -1;
    RaConfig *rac = rav1e_config_default();
    if (!rac) {
        printf("Unable to initialize\n");
        goto clean;
    }

    ret = rav1e_config_parse_int(rac, "width", 64);
    if (ret < 0) {
        printf("Unable to configure width\n");
        goto clean;
    }

    ret = rav1e_config_parse_int(rac, "height", 96);
    if (ret < 0) {
        printf("Unable to configure height\n");
        goto clean;
    }

    ret = rav1e_config_parse_int(rac, "speed", 9);
    if (ret < 0) {
        printf("Unable to configure speed\n");
        goto clean;
    } 
```

### 上下文

根据`rav1e` API，上下文结构由配置产生，并且使用相同的*发送-接收*模型。
方便的方法没有公开，帧分配功能实际上是必不可少的。

```
// Essential API
RaContext *rav1e_context_new(const RaConfig *cfg);
void rav1e_context_unref(RaContext *ctx);

RaEncoderStatus rav1e_send_frame(RaContext *ctx, const RaFrame *frame);
RaEncoderStatus rav1e_receive_packet(RaContext *ctx, RaPacket **pkt); 
```

```
// Optional API
uint8_t *rav1e_container_sequence_header(RaContext *ctx, size_t *buf_size);
void rav1e_container_sequence_header_unref(uint8_t *sequence); 
```

### 拉菲

由于框架结构在 C 中是不透明的，我们有以下函数来创建、填充和处理框架。

```
RaFrame *rav1e_frame_new(const RaContext *ctx);
void rav1e_frame_unref(RaFrame *frame);

/**
 * Fill a frame plane
 * Currently the frame contains 3 planes, the first is luminance followed by
 * chrominance.
 * The data is copied and this function has to be called for each plane.
 * frame: A frame provided by rav1e_frame_new()
 * plane: The index of the plane starting from 0
 * data: The data to be copied
 * data_len: Lenght of the buffer
 * stride: Plane line in bytes, including padding
 * bytewidth: Number of bytes per component, either 1 or 2
 */
void rav1e_frame_fill_plane(RaFrame *frame,
                            int plane,
                            const uint8_t *data,
                            size_t data_len,
                            ptrdiff_t stride,
                            int bytewidth); 
```

### RaEncoderStatus

编码器状态枚举由`rav1e_send_frame`和`rav1e_receive_packet`返回，并且已经可以任意查询其状态的上下文。

```
RaEncoderStatus rav1e_last_status(const RaContext *ctx); 
```

为了模拟 rust [调试](https://doc.rust-lang.org/std/fmt/trait.Debug.html)功能，提供了一个`to_str`功能。

```
char *rav1e_status_to_str(RaEncoderStatus status); 
```

### 工作流程

*C* API 工作流程类似于 *Rust* 的工作流程，尽管由于错误检查的原因更加冗长。

```
 RaContext *rax = rav1e_context_new(rac);
    if (!rax) {
        printf("Unable to allocate a new context\n");
        goto clean;
    } 
```

```
 RaFrame *f = rav1e_frame_new(rax);
    if (!f) {
        printf("Unable to allocate a new frame\n");
        goto clean;
    } 
```

```
while (keep_going(i)){
     RaPacket *p;
     ret = rav1e_receive_packet(rax, &p);
     if (ret < 0) {
         printf("Unable to receive packet %d\n", i);
         goto clean;
     } else if (ret == RA_ENCODER_STATUS_SUCCESS) {
         printf("Packet %"PRIu64"\n", p->number);
         do_something_with(p);
         rav1e_packet_unref(p);
         i++;
     } else if (ret == RA_ENCODER_STATUS_NEED_MORE_DATA) {
         RaFrame *f = get_frame_by_some_mean(rax);
         ret = rav1e_send_frame(rax, f);
         if (ret < 0) {
            printf("Unable to send frame %d\n", i);
            goto clean;
        } else if (ret > 0) {
        // Cannot happen in normal conditions
            printf("Unable to append frame %d to the internal queue\n", i);
            abort();
        }
     } else if (ret == RA_ENCODER_STATUS_LIMIT_REACHED) {
         printf("Limit reached\n");
         break;
     }
} 
```

## 在关闭

这篇文章主要是一个很好的借口来尝试[dev to](https://dev.to)并写下一些笔记，澄清我对 API 方面到目前为止已经做了什么以及我应该改变和改进什么的想法。

如果你设法读到这里，你的反馈真的很受欢迎，请随时评论，尝试软件并为 [crav1e](https://github.com/lu-zero/crav1e/issues/new) 和 [rav1e](https://github.com/xiph/rav1e/issues/new) 打开问题。

### 即将到来

*   工作 **crav1e** 让我看到了[c-inter operability story](https://blogs.gentoo.org/lu_zero/2018/12/30/making-and-using-c-compatible-libraries-in-rust-present-and-future/)of*rust*的什么是好的，什么是缺乏的，现在[这个](https://github.com/rust-lang/cargo/pull/6298)登陆了，我可以开始为它制作和发布更好的工具，也许我会在这里谈论更多。
*   很快 **rav1e** 将获得更多面向线程的特性，一些基准测试实验也将很快进行。

### 感谢

*   特别感谢[德里克](https://github.com/dwbuiten)和[维托里奥](https://github.com/kodabb/)花了大量时间将`crav1e`集成到更大的软件中，并对最初迭代中缺失和损坏的部分给出了宝贵的反馈。
*   感谢 [David](http://github.com/barrbrain/) 的评论和编辑工作。
*   也感谢 [Matteo](https://dev.to/naufraghi) 把我介绍给 [dev.to](https://dev.to) 。