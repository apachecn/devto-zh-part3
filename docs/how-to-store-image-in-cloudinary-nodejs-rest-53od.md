# 如何在 Cloudinary Nodejs REST 中存储图像

> 原文：<https://dev.to/jakzaizzat/how-to-store-image-in-cloudinary-nodejs-rest-53od>

[![Cover](img/69e778a2ae9d68dbab52494426172980.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wZQJ-Y1E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jakzaizzat.com/wp-content/uploads/2018/10/How-to-store-image-in-Cloudinary-Nodejs-REST.png)

上周，我正在使用这个 VEMN 栈(Vue，Express，Mongo，Node)构建我的边项目。我的后端需要使用 REST 与前端交互。我需要存储最终用户上传的图像资产。我做了一些调查，寻找为 web 应用程序存储图像的最佳方式。我发现有两家不错的公司提供这些服务，它们是 Cloudinary 和 Imgix。

与 Cloudinary 相比，Imgix 的定价合理，但 Cloudinary 有一个免费的软件包。由于我的副业项目仍在 MVP 中，我想验证这个想法，所以我决定使用 Cloudinary。

## 这就是你需要的

先在 cloudinary 注册一个账户。别担心，这是免费的。
如何在 Cloudinary Nodejs REST 2 中存储图像

安装此软件包

*   [云淡风轻](https://www.npmjs.com/package/cloudinary)
*   [CloudinaryStorage](https://www.npmjs.com/package/multer-storage-cloudinary)
*   [Multer](https://www.npmjs.com/package/multer)

我让它变得更简单，只需运行这个命令。

```
npm install --save cloudinary cloudinaryStorage multer 
```

完成所有工作后，您应该将这个中间件注入到您的路由中。

## 配置

首先导入包，然后编写一个 cloudinary 配置。您可以在仪表板上获得云名称、API 密钥和 API 密钥。

```
const cloudinary = require('cloudinary');
const cloudinaryStorage = require('multer-storage-cloudinary');
const multer = require('multer');
const config = require('../../config/config');

cloudinary.config({
  cloud_name: config.cloudinary.name,
  api_key: config.cloudinary.api_key,
  api_secret: config.cloudinary.api_secret
}); 
```

为您自己的项目定义存储。您可以定义文件夹名称、格式和图像转换。

```
const storage = cloudinaryStorage({
  cloudinary,
  folder: 'jomwedding',
  allowedFormats: ['jpg', 'png'],
  transformation: [{ width: 500, height: 500, crop: 'limit' }]
});
const parser = multer({ storage }); 
```

## 中间件

当一切完成后，将您的中间件注入到您的路线中。

```
router.route('/image')
  .post(parser.single('image'), userCtrl.uploadImage); 
```

确保您使用多部分形式来发送图像

## 我休息的例子

图像将被存储到云端，然后才能在您的控制器中访问。访问您存储的图像可以在 req.file 中找到。

```
function uploadImage(req, res, next) {
  console.log(req.file);
  const image = {};
  image.url = req.file.url;
  image.id = req.file.public_id;
} 
```

如果你有任何问题，请告诉我。

原文提交自[我的博客](https://jakzaizzat.com/how-to-store-image-in-cloudinary-nodejs-rest/)