# 古腾堡组件:“我只需要一个”

> 原文：<https://dev.to/hryggrbyr/gutenberg-components-i-only-need-one-536c>

[![Christopher Lee as Francisco Scaramanga in The Man with the Golden Gun](img/e07bcac7202365b8a453d825203f2bc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_iVR6-YP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4b1jndfvbjvhk2gijpcj.jpg)

Gutenberg 附带了大量的默认组件(见下面的列表),其中大部分永远不会在我们的网站上使用。

完全有可能把它们留在那里，但这有可能导致灾难。

让它们对内容管理器可见有两种可能性；

1.  他们将很难找到他们想要使用的实际组件
2.  他们会尝试使用一个我们没有设计的组件，这会破坏前端的布局

那么，我们该怎么办...？

### 仅显示您将实际使用的组件

把它放到你的`functions.php`文件中，或者，更好的是，写一个插件来处理它:)

```
add_filter('allowed_block_types', 'txb_allowed_block_types', 10, 2);

function txb_allowed_block_types( $allowed_blocks, $post )
{
    $allowed_blocks = array(
      'core/image', // Add the blocks you want here...
      'core/paragraph',
      'core/heading',
      'acf/videum' // Add your custom blocks here
    );

    return $allowed_blocks;

} 
```

Enter fullscreen mode Exit fullscreen mode

### 古腾堡默认组件

*   段落
*   标题
*   图像
*   目录
*   引用
*   走廊
*   声音的
*   涉及
*   文件
*   录像
*   密码
*   预格式化
*   经典街区
*   自定义 HTML
*   拉动报价
*   桌子
*   诗
*   列
*   分离器
*   纽扣
*   媒体和文本
*   更大的
*   分页符
*   逆电流器
*   短代码
*   档案
*   种类
*   最新评论
*   最新帖子
*   把…嵌入
*   推特
*   油管（国外视频网站）
*   脸谱网
*   照片墙
*   WordPress
*   声音云
*   Spotify
*   闪烁（光）
*   Vimeo
*   阿尼莫托
*   乌云密布
*   大学幽默
*   拥挤信号
*   日常运动
*   搞笑或死亡
*   葫芦
*   Imgur
*   是吗
*   Kickstarter
*   Meetup.com
*   Mixcloud
*   照相桶
*   Reddit
*   复原
*   截屏
*   Scribd
*   幻灯片共享
*   走私杯
*   扬声器面板
*   翻晒
*   Tumblr
*   视频新闻
*   WordPress.tv