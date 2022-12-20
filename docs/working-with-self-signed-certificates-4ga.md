# 使用自签名证书

> 原文：<https://dev.to/mobarakat/working-with-self-signed-certificates-4ga>

我正致力于开发与外部设备通信的软件。连接应该是安全的。这里涉及到使用 ssl 证书。

为此，我需要做三件事:

1)为证书颁发机构(CA)、服务器和客户端创建所有需要的证书，以模拟连接。

2)使用 [Nodejs](https://nodejs.org) 创建服务器和客户端来测试证书。

3)在我的 c++项目中，我使用的是 [CPP Rest API](https://github.com/microsoft/cpprestsdk) 。我希望它将我的客户端证书嵌入到与服务器的连接中，以授权连接并成功地与服务器通信。

我将介绍每一步:

### 创建证书

#### 1)下载安装 Openssl

*   *更多详细信息，请点击[这里](https://github.com/openssl/openssl)T3】*

#### 2)创建认证机构[CA]配置文件

这是一个可选的步骤，但是使用一个文件将信息传递给 openssl 比每次都插入文件更容易。

我试图在这里创建一个简单的例子

*   *您可以在这里查看文件格式*

#### 3)创建 CA 证书和密钥

```
openssl req -new -x509 -config cert-authority.cnf -keyout cert-authority-key.pem -out cert-authority-crt.pem 
```

*输出:* `cert-authority-key.pem, cert-authority-crt.pem`

# 服务器

#### 1)创建服务器私钥

```
openssl genrsa -out server-key.pem 4096 
```

*输出:* `server-key.pem`

#### 2)创建服务器配置文件

我试图在这里创建一个简单的例子

#### 3)创建服务器证书签名请求

```
openssl req -new -config server.cnf -key server-key.pem -out server-csr.pem 
```

*输出:* `server-csr.pem`

#### 4)签署服务器证书

```
openssl x509 -req -extfile server.cnf -passin "pass:12345" -in server-csr.pem -CA cert-authority-crt.pem -CAkey cert-authority-key.pem -CAcreateserial -out server-crt.pem 
```

# 客户端

#### 1)创建客户端私钥

```
openssl genrsa -out client-key.pem 4096 
```

*输出:* `client-key.pem`

#### 2)创建客户端配置文件

我试图在这里创建一个简单的例子

#### 3)创建客户端证书签名请求

```
openssl req -new -config client.cnf -key client-key.pem -out client-csr.pem 
```

*输出:* `client-csr.pem`

#### 4)签署客户证书

```
openssl x509 -req -extfile client.cnf -passin "pass:12345" -in client-csr.pem -CA cert-authority-crt.pem -CAkey cert-authority-key.pem -CAcreateserial -out client-crt.pem 
```

#### 5)验证客户端证书

您可以使用 CA 或服务器证书来验证客户端证书，如下所示:

```
openssl verify -CAfile cert-authority-crt.pem client-crt.pem 
```

### 使用 Nodejs 创建服务器和客户端

#### 服务器

```
var fs = require('fs'); 
var https = require('https'); 

var options = { 
    key: fs.readFileSync('server-key.pem'), 
    cert: fs.readFileSync('server-crt.pem'), 
    ca: fs.readFileSync('cert-authority-crt.pem'), 
    strictSSL: true,
    requestCert: true, 
    rejectUnauthorized: true
}; 
var srv = https.createServer(options, function (req, res) { 
    console.log('Recieve an request from authorized client!'); 
    res.writeHead(200); 
    res.end("Hello secured world!"); 
}).listen(3000, function() {
     console.log('Hello! I am at https://localhost:'+ srv.address().port);
}); 
```

#### 客户端

```
var fs = require('fs'); 
var https = require('https'); 
var options = { 
    hostname: 'localhost', 
    port: 3000, 
    path: '/', 
    method: 'GET', 
    key: fs.readFileSync(__dirname +'/client-key.pem'), 
    cert: fs.readFileSync(__dirname +'/client-crt.pem'), 
    ca: fs.readFileSync(__dirname +'/cert-authority-crt.pem') }; 
var req = https.request(options, function(res) { 
    res.on('data', function(data) { 
        process.stdout.write(data); 
    }); 
}); 
req.end(); 
req.on('error', function(e) { 
    console.error(e); 
}); 
```

### C++代码

#### 1)进入代码前的注释

创建证书后，我们需要将您的证书颁发机构安装到您的计算机中。

```
openssl pkcs12 -export -out cert-authority.p12 -inkey cert-authority-key.pem -in cert-authority-cert.pem 
```

双击 cert-authority.p12 并在“受信任的根证书颁发机构”下安装 authourity

现在用同样的方法转换你的客户端证书，稍后在 c++中加载:

```
openssl pkcs12 -export -out client.p12 -inkey client-key.pem -in client-cert.pem 
```

最后，不要在你的链接器中包含下面的库

```
Crypt32.lib
winhttp.lib 
```

#### 2)加载证书功能

```
void loadOrFindCertificate() {
  if (_pCertContext) return;

  HANDLE _certFileHandle = NULL;

  /*Open File*/
  _certFileHandle = CreateFile(L"client.p12", GENERIC_READ, 0, 0, OPEN_ALWAYS, FILE_ATTRIBUTE_NORMAL, 0);

  if (INVALID_HANDLE_VALUE == _certFileHandle)
    return;

  DWORD certEncodedSize = GetFileSize(_certFileHandle, NULL);

  /*Check if file size */
  if (!certEncodedSize) {
    CloseHandle(_certFileHandle);
    return;
  }

  BYTE* certEncoded = new BYTE[(int)certEncodedSize];

  /*Read File */
  auto result = ReadFile(_certFileHandle, certEncoded, certEncodedSize, &certEncodedSize, 0);

  if (!result) {
    CloseHandle(_certFileHandle);
    return;
  }

  CRYPT_DATA_BLOB data;
  data.cbData = certEncodedSize;
  data.pbData = certEncoded;

  // Convert key-pair data to the in-memory certificate store
  WCHAR pszPassword[] = L"12345";
  HCERTSTORE hCertStore = PFXImportCertStore(&data, pszPassword, 0);
  SecureZeroMemory(pszPassword, sizeof(pszPassword));

  if (!hCertStore) {
    CloseHandle(_certFileHandle);
    return;
  }

  //get handle of loaded certificate
  _pCertContext = CertFindCertificateInStore
  (hCertStore, X509_ASN_ENCODING | PKCS_7_ASN_ENCODING, 0, CERT_FIND_ANY, NULL, NULL);

  CloseHandle(_certFileHandle);

} 
```

#### 3)向服务器发送请求进行测试

```
// Create http_client configuration.
  web::http::client::http_client_config config;
  config.set_timeout(std::chrono::seconds(2));

  config.set_validate_certificates(true);

  auto func = [&](web::http::client::native_handle handle) {

    loadOrFindCertificate();

    //Attach certificate with request
    if (_pCertContext)
      WinHttpSetOption(handle, WINHTTP_OPTION_CLIENT_CERT_CONTEXT,
      (LPVOID)_pCertContext, sizeof(CERT_CONTEXT));
  };

  config.set_nativehandle_options(func);

  // Create http_client to send the request.
  web::http::client::http_client client(U("https://localhost:4150"), config);

  // Build request URI and start the request.
  auto requestTask = client.request(web::http::methods::GET)
    // Handle response headers arriving.
  .then([=](web::http::http_response response)
  {
    auto status(response.status_code());
    printf("Received response status code:%u\n", status);

    /* Extract plain text only if status code signals success */
    if (status >= 200 && status < 300)
      return response.extract_string(true);
    else
      return Concurrency::task<utility::string_t>([=] { return utility::string_t(); });

  }).then([=](utility::string_t val) {
    printf("Received response message:%s\n", utility::conversions::to_utf8string(val).c_str());
  });

  // Wait for all the outstanding I/O to complete and handle any exceptions
  try
  {
    requestTask.wait();
  }
  catch (const std::exception &e)
  {
    printf("Error exception:%s\n", e.what());
  } 
```

### 源代码

[使用 openssl 创建服务器/客户端证书](https://github.com/mobarakat/mb-create-selfsignedcertificate)
[使用 NodeJS 创建服务器和客户端](https://github.com/mobarakat/mb-nodjs-client_server-selfsignedcertificate)
[使用 CPP REST API 加载证书](https://github.com/mobarakat/mb-cpp-client-selfsignedcertificate)