# 在浏览器中压缩图像？轻松点。

> 原文：<https://dev.to/antonreshetov/compress-image-in-browser-easy-5238>

首先，我要感谢对 MySigMail - UI 电子邮件签名生成器项目感兴趣的人们。

在 repo 中出现了一个有趣的 PR，它实现了一种在浏览器中压缩图像的方法，而不使用服务器。

我想分享一下代码:

```
function compressImage (base64) {
  const canvas = document.createElement('canvas')
  const img = document.createElement('img')

  return new Promise((resolve, reject) => {
    img.onload = function () {
      let width = img.width
      let height = img.height
      const maxHeight = 200
      const maxWidth = 200

      if (width > height) {
        if (width > maxWidth) {
          height = Math.round((height *= maxWidth / width))
          width = maxWidth
        }
      } else {
        if (height > maxHeight) {
          width = Math.round((width *= maxHeight / height))
          height = maxHeight
        }
      }
      canvas.width = width
      canvas.height = height

      const ctx = canvas.getContext('2d')
      ctx.drawImage(img, 0, 0, width, height)

      resolve(canvas.toDataURL('image/jpeg', 0.7))
    }
    img.onerror = function (err) {
      reject(err)
    }
    img.src = base64
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

感谢 [@mykeels](https://github.com/mykeels)

MySigMail 总是很高兴有投稿人:)

现在我正在开发第二版，其中将会有一个所见即所得的电子邮件编辑器。

因此，我会很高兴，如果有愿意帮助我在第 1 版的签名模板的发展。

[https://github.com/antonreshetov/mysigmail](https://github.com/antonreshetov/mysigmail)