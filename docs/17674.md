# 如何使用 webmock 向 Google API 发送上传请求

> 原文：<https://dev.to/jastkand/how-to-stub-an-upload-request-to-google-api-using-webmock-32oo>

当`X-Goog-Upload-Status`没有设置为`'final'`时，`google-api-client` gem 不会对上传请求返回任何响应。

当你想要存根一个 API 请求时，你通常会写这样的代码:

```
stub_request(:post, 'https://www.googleapis.com/upload/gmail/v1/users/me/messages/send')
  .with(headers: { 'X-Goog-Upload-Header-Content-Type': 'message/rfc822' })
  .to_return(body: upload_response.to_json,
             headers: {
               content_type: 'application/json'
             }) 
```

但是它不适用于 Google Upload API 请求——它将返回`nil`作为响应。为了解决这个问题，您应该将`'X-Goog-Upload-Status': 'final'`添加到响应头中。所以代码应该是这样的:

```
stub_request(:post, 'https://www.googleapis.com/upload/gmail/v1/users/me/messages/send')
  .with(headers: { 'X-Goog-Upload-Header-Content-Type': 'message/rfc822' })
  .to_return(body: upload_response.to_json,
             headers: {
               content_type: 'application/json',
               'X-Goog-Upload-Status': 'final'
             }) 
```