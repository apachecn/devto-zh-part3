# (NodeJS + Express + Multer)我如何为用户上传的文件提供服务的任何建议？

> 原文：<https://dev.to/antonioavelar/nodejs-express-multer-how-can-i-serve-user-uploaded-files-1p4>

嗨！，我正在构建一个用户可以上传图片的 Web 应用程序。对于接收二进制文件的任务，我使用 Multer。
所以...默认情况下，Multer 将图片存储在文件系统中，不带文件扩展名。我正在存储文件数据(原始名称、mimetype、扩展名、路径，...)在数据库中。

我应该如何向用户提供这些文件？
我是否应该使用一个路径来获取图像数据(扩展名，mimetype，...)然后用正确的 mime 类型发送文件？
还是应该用 express.static("path/to/folder ")？