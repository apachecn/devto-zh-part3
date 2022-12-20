# 使用 typescript 构建 AWS Lambda 函数仅使用 packet-bundler

> 原文：<https://dev.to/terrierscript/build-aws-lambda-function-with-typescript-only-use-parcel-bundler-426a>

一般来说，在 AWS Lambda 上使用 typescript 时，我们需要像 serveless 或 apex 这样的工具。但是对于轻便的使用来说，它太重了。

我找到了一种只用包捆机来建造的方法

首先，安装 typescript

```
yarn add -D typescript 
```

并安装包裹和包裹插件 zip

```
yarn add -D parcel parcel-plugin-zip 
```

创建`handler.ts`

```
export const handler = async (): Promise<any> => {
  console.log('Hello World!');
  return {};
} 
```

我们用下面的命令为 aws 构建这个函数。

```
$ yarn parcel build handler.ts --target=node --global handler -o index.js --bundle-node-modules --no-source-maps 
```

最后，将构建命令设置为`package.json`。

```
//  package.json  "scripts":  {  "build":  "yarn parcel build handler.ts --target=node --global handler -o index.js --bundle-node-modules --no-source-maps",  "deploy:aws":  "aws lambda update-function-code --function-name my-special-function --zip-file fileb://./dist.zip",  "deploy":  "yarn build; yarn deploy:aws",  } 
```

我们现在可以用`yarn deploy`构建和部署 lambda 函数(可能需要设置 aws 凭证)！

# 参考文献

*   [https://scotch . io/@ nwayve/how-to-build-a-lambda-function-in-typescript](https://scotch.io/@nwayve/how-to-build-a-lambda-function-in-typescript)
*   [https://github . com/parcel-bundler/parcel/issues/192 # issue comment-366032738](https://github.com/parcel-bundler/parcel/issues/192#issuecomment-366032738)