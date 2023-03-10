# 使用 NextJS 将静态资产部署到 AWS S3

> 原文：<https://dev.to/zanonnicola/deploy-static-assets-to-aws-s3-with-nextjs-762>

> 最初发表在我的[博客](https://www.inextenso.dev/deploy-static-assets-to-aws-s3-with-nextjs)

## 要求

*   节点> = 8
*   AWS 帐户
*   S3 水桶

如果你需要加快你的内容交付和提高你的网站性能，CDN(内容交付网络)这个词很可能会出现在你的谷歌搜索中。

### 什么是 CDN？

内容交付网络或内容分发网络(CDN)是代理服务器及其数据中心的地理分布式网络。目标是通过相对于最终用户在空间上分布服务来提供高可用性和高性能。

[来源:维基百科](https://en.wikipedia.org/wiki/Content_delivery_network)

如果你想了解更多关于 CDN 如何工作的信息，我建议你阅读这篇文章:[CDN 如何工作](https://www.keycdn.com/support/how-does-a-cdn-work)

> 根据 built with 的数据，超过 84%的 10K 顶级网站都在使用 CDN。

### 但是，为什么呢？

如今速度很重要。我相信你已经遇到了一些静态的东西:

> Y 公司的产品 X 发现，如果一个页面的加载时间超过 3 秒，53%的移动网站访问就会被放弃。

我不会花太多时间来说服你为什么应该关注性能，因为你已经阅读了这篇文章，但是如果你需要更多的证据，请在这里阅读:[https://developers . Google . com/web/fundamentals/performance/why-performance-matters/](https://dev.towhy-performance-matters)

如果你正在用 NextJS 开发一个 React 应用程序，你已经走上了快速加载的道路。让我们看看如何在 NextJS 工作流中集成 CDN。

## 解

我们将把 NextJS 构建脚本生成的所有静态资产上传到亚马逊 S3 桶。多亏了 [CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) ,我们在桶中的对象将被全球分发

幸运的是，随着 [Next 7](https://nextjs.org/blog/next-7) 的发布，`.next`文件夹中生成的构建资产将匹配你下一个应用的 URL 结构:

```
// https://cdn.example.com/_next/static/<buildid>/pages/index.js
// mapped to: .next/static/<buildid>/pages/index.js 
```

我们只需要照原样复制交叉文件:)

有几种方法可以实现这一点。如果你用谷歌搜索一下，你会发现一些像[这个](https://github.com/zeit/next.js/issues/3621)一样的公开问题，以及一些替代的解决方案。

我提议的这个是专门为我的用例设计的，但它也可能对你的用例有所帮助。

## 脚本

```
 // uploadTos3.js

const fs = require('fs');
const readDir = require('recursive-readdir');
const path = require('path');
const AWS = require('aws-sdk');
const mime = require('mime-types');

/*

You will run this script from your CI/Pipeline after build has completed.

It will read the content of the build directory and upload to S3 (live assets bucket)

Every deployment is immutable. Cache will be invalidated every time you deploy.

*/

AWS.config.update({
  region: 'eu-central-1',
  accessKeyId: process.env.AWS_ACCESS_KEY_ID,
  secretAccessKey: process.env.SECRET_ACCESS_KEY,
  maxRetries: 3
});

const directoryPath = path.resolve(__dirname, './.next');

// Retrive al the files path in the build directory
const getDirectoryFilesRecursive = (dir, ignores = []) => {
  return new Promise((resolve, reject) => {
    readDir(dir, ignores, (err, files) => (err ? reject(err) : resolve(files)));
  });
};

// The Key will look like this: _next/static/<buildid>/pages/index.js
// the <buildid> is exposed by nextJS and it's unique per deployment.
// See: https://nextjs.org/blog/next-7/#static-cdn-support
const generateFileKey = fileName => {
  // I'm interested in only the last part of the file: '/some/path/.next/build-manifest.json',
  const S3objectPath = fileName.split('/.next/')[1];
  return `next-assets/_next/${S3objectPath}`;
};

const s3 = new AWS.S3();

const uploadToS3 = async () => {
  try {
    const fileArray = await getDirectoryFilesRecursive(directoryPath, [
      'BUILD_ID'
    ]);
    fileArray.map(file => {
      // Configuring parameters for S3 Object
      const S3params = {
        Bucket: 's3-service-broker-live-ffc6345a-4627-48d4-8459-c01b75b8279e',
        Body: fs.createReadStream(file),
        Key: generateFileKey(file),
        ACL: 'public-read',
        ContentType: mime.lookup(file),
        ContentEncoding: 'utf-8',
        CacheControl: 'immutable,max-age=31536000,public'
      };
      s3.upload(S3params, function(err, data) {
        if (err) {
          // Set the exit code while letting
          // the process exit gracefully.
          console.error(err);
          process.exitCode = 1;
        } else {
          console.log(`Assets uploaded to S3: `, data);
        }
      });
    });
  } catch (error) {
    console.error(error);
  }
};

uploadToS3(); 
```

```
 // next.config.js

const isProd = process.env.NODE_ENV === 'production';
module.exports = {
  // You may only need to add assetPrefix in the production.
  assetPrefix: isProd ? 'https://d3iufi34dfeert.cloudfront.net' : ''
} 
```

几个音符:

*   您必须在下一个构建完成后运行该脚本
*   如果你使用 Next `8.0.4`，你的构建将是[确定性的](https://nextjs.org/blog/next-8-0-4#build-performance):如果代码没有改变，构建输出将每次都保持相同的结果。
*   如果我从 AWS CLI 中得到任何错误，我的构建就会失败
*   我正在将所有资产上传到`next-assets`文件夹。CF 分布将以此为基础路径。

我最终的构建脚本看起来像这样:

```
set -e

npm ci

npm run test npm run build
npm prune --production

npm run uploadTos3 
```

就是这样:)