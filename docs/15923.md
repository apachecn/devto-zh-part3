# ParcelJS 中字体 URL 的解决方法

> 原文：<https://dev.to/davinaleong/workaround-for-font-face-urls-in-parceljs-3dea>

你好世界！

对于那些试图让`font-face`中的 URL 在 **ParcelJS** 中正常工作却遇到问题的人，我愿意分享我的(hacky)解决方案。

# 步骤

1.  在您的终端中运行`parcel`,确保您所有的`HTML`文件都已经创建在第一次运行该命令时生成的`/dist`文件夹中。
2.  杀死`parcel`进程。(Win 和 Mac: `Ctrl+C`)
3.  在`/dist`文件夹中创建一个文件夹来存储你的字体(例如:`/fonts`)，并将你的字体文件移入其中。
4.  用您的`@font-face`代码创建一个`.css`文件。(我把它命名为`font-faces.css` ) *(下面是样例代码)*
5.  通过`<link>`标签将`font-faces.css`导入到每个`HTML`文件中。
6.  再次运行`parcel <your-html-file.html>`并刷新您的网络浏览器。如果操作正确，您应该看到您的字体加载正确。

样本代码:

```
@font-face{
  font-family: "Montserrat";
  font-weight: 100;
  font-style: normal;
  src: url("./fonts/montserrat/Montserrat-Thin.ttf"), format("truetype");
} 
```

Enter fullscreen mode Exit fullscreen mode

# 一些注意事项/提示

*   在编写此变通办法时使用了 ParcelJS 的版本`1.10.3`。
*   你必须求助于 plain ol' `css`来通过`@font-face`导入字体。
*   *不*将`<link>`到`font-faces.css`包含在原`HTML`文件中。因为这可能导致与试图在`SASS`中捆绑`@font-face`相同的错误。将其包含在`/dist`文件夹的`HTML`文件中。

希望这对面临这些问题的人有所帮助。

# 其他

如果这对任何人有帮助，我也分享我写的要点来帮助快速生成`@font-face`代码。要使用它，只需在您的终端中运行`node gen-fontface`，并将输出复制到您的`CSS`文件中。