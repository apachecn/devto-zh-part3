# 在节点中使用 AWS Lambda 和 S3 上传文件

> 原文：<https://dev.to/marton_veto/file-upload-with-aws-lambda-and-s3-in-node-h1n>

如果你在 Node 中有一个 Lambda 函数，并且想把文件上传到 S3 桶中，你有无数的选项可以选择。在本文中，我将提出一个解决方案，它不使用 web 应用程序框架(如 Express ),而是通过 Lambda 函数将文件上传到 S3。HTTP 主体作为**多部分/格式数据**发送。

# 代码

为了解析多部分/形式数据请求，我使用了**[λ-多部分](https://www.npmjs.com/package/lambda-multipart)** 包。这个包可以解析文本和文件内容，这是我如何使用它:

```
const parseMultipartFormData = async event => {
  return new Promise((resolve, reject) => {
    const parser = new Multipart(event);

    parser.on("finish", result => {
      resolve({ fields: result.fields, files: result.files });
    });

    parser.on("error", error => {
      return reject(error);
    });
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

在`files`列表中，我将有一个`Buffer`对象的列表。

我是这样调用它并循环所有文件来上传它们的:

```
 const { fields, files } = await parseMultipartFormData(event);

  await Promise.all(
    files.map(async file => {
      await uploadFileIntoS3(file);
    })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

最后，上传一个文件到 S3:

```
const uploadFileIntoS3 = async file => {
  const ext = getFileExtension(file);
  const options = {
    Bucket: process.env.file_s3_bucket_name,
    Key: `${uuidv4()}.${ext}`,
    Body: file
  };

  try {
    await s3.upload(options).promise();
  } catch (err) {
    console.error(err);
    throw err;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我使用 *uuid* 库来获得一个惟一的标识符，我将用它作为文件名。请注意，如果您的文件是`Buffer`对象，您可以将它们传递给 S3 SDK 的 [`upload`](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#upload-property) 方法，但是您不能将`Buffer`对象传递给 [`putObject`](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#putObject-property) 方法！在`catch`块中，你应该添加一些有意义的错误处理。我只是记录了错误并重新抛出，以便能够在调用者端看到它。

您可以添加一些文件验证来检查文件的 MIME 类型和大小。⚠️但是注意了，目前，Lambda 有多重限制。其中之一是它只支持小于 6MB 的 HTTP 请求，所以如果你想上传大于这个限制的文件，你不能使用这个解决方案。

不要忘记创建一些 IAM 角色(并将其关联到 Lambda 函数)来将对象放入 S3 桶。

这是我获取文件扩展名的方法:

```
const getFileExtension = file => {
  const headers = file["headers"];
  if (headers == null) {
    throw new Error(`Missing "headers" from request`);
  }

  const contentType = headers["content-type"];
  if (contentType == "image/jpeg") {
    return "jpg";
  }

  throw new Error(`Unsupported content type "${contentType}".`);
}; 
```

Enter fullscreen mode Exit fullscreen mode

基本上，就是这样。你可以在这里找到完整的源代码。我使用无服务器框架来部署我的 Lambda 函数和创建 S3 桶。