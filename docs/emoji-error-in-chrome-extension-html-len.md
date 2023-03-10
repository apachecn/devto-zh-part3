# Chrome 扩展 HTML 中的表情符号错误

> 原文：<https://dev.to/dance2die/emoji-error-in-chrome-extension-html-len>

*照片由[伯纳德·赫尔曼](https://unsplash.com/photos/bSpqe48INMg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/emoji?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

我在试图在默认弹出 HTML 的 Chrome 扩展标题中显示表情符号时遇到了一个问题，`page_action.html`(由`manifest.json`中的`page_action -> default_popup`指定)。

但得到的却是混乱的字符。

<figure>[![Emoji displayed as gobbledygook](img/80fbd53e566933572909b0a0abe9fd57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O3t0Hxqh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.slightedgecoder.com/wp-content/uploads/2019/02/2019-02-16_23-00-15.png%3Fw%3D1170%26ssl%3D1) 

<figcaption>表情符号显示为天书</figcaption>

</figure>

问题是我不得不通过添加一个 charset meta 标签来告诉 Chrome 我使用的是 unicode。