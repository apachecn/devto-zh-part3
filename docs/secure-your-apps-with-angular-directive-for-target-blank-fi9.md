# 使用 target='_blank '时，使用角度指令保护您的应用程序

> 原文：<https://dev.to/azrizhaziq/secure-your-apps-with-angular-directive-for-target-blank-fi9>

嗨，今天我想和大家分享一下如果你正在使用`target='_blank'`，如何保护你的应用。

通常要链接一个外部 url，我们会这样做:
`<a href='https://dev.to'>Dev.to</a>`。请注意，这仍然有效。但不幸的是，这里有一个安全问题。你可以在这里了解更多，它还包括例子:【https://mathiasbynens.github.io/rel-noopener/#hax】

要解决这个问题，我们可以简单地给锚标记添加一个额外的属性。
`<a href='https://dev.to' rel='noopener noreferrer'>Dev.to</a>`。

然而，让我们说，在我们的应用程序中，我们有很多外部链接。
就我个人而言，我真的不喜欢给我的每个锚标签都写`rel='noopener noreferrer'`。

这样，我们就可以自动将`rel='noopener noreferrer'`添加到每个带有角度方向的锚点标签中。

```
// target-blank.directive.ts

import { Directive, HostBinding } from '@angular/core';

@Directive({
  // target every <a target="_blank">...<a>
  selector: 'a[target=_blank]',
})
export class TargetBlankDirective {
  // will add <a ... rel='noopener noreferrer'>...</a>
  @HostBinding('attr.rel') rel = 'noopener noreferrer';
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// and just use it like this
<a href='someurl' target='_blank'>someurl</a>

// will render like this
<a href='someurl' target='_blank' rel='noopener noreferrer'>someurl</a> 
```

Enter fullscreen mode Exit fullscreen mode

最后一件事，假设你在点击时有动态动作。我们要如何防止这种情况。

```
function openInNewTab(url: string): void {
    // open link in new tab
    const newTab = window.open(url, '_blank')

    // set opener to null so that no one can references it
    newTab.opener = null
}

openInNewTab('https://dev.to') 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里玩
[https://stackblitz.com/edit/angular-target-blank?embed=1&&](https://stackblitz.com/edit/angular-target-blank?embed=1&&)

总之:

| 赞成 | 骗局 |
| --- | --- |
| 自动添加 rel='noopener noreferrer ' | 新开发人员没有意识到这种指令行为 |
| 能够自动防止此问题 | 客户端禁用 JS |

你可以在这里阅读更多
[https://www . pixelstech . net/article/1537002042-The-danger-of-target % 3D _ blank-and-opener](https://www.pixelstech.net/article/1537002042-The-danger-of-target%3D_blank-and-opener)