# 使用 upload.express 共享一个命令文件

> 原文：<https://dev.to/maxencehenneron/one-command-file-sharing-with-upload-express-1io6>

在这个简短的教程中，我将介绍我们在 [upload.express](https://upload.express) 上实现的最新功能:一个单命令终端文件共享。

如果你想上传一个文件到你当前的工作目录，你只需要输入下面的命令:

```
curl --upload-file ./file_name.ext https://upload.express/upload/file_name.ext 
```

它将返回一个指向上传文件的链接。

## 创建别名

记住 curl 命令参数可能需要一段时间。您可以通过在~/中创建别名来简化命令。bashrc 或者~/。zshrc 文件。

只需在文件末尾添加以下代码:

```
upload() {
    if [ $# -eq 0 ]; then echo -e "No arguments specified. Usage:\necho upload /path/to/file.md"; return 1; fi curl --progress-bar --upload-file "$1" https://upload.express/upload/$(basename $1) | tee /dev/null;
}

alias upload=upload 
```

您现在可以使用命令`upload file_path`来共享任何文件。