# 制造 transferZ(零件号:1)

> 原文：<https://dev.to/itzmeanjan/making-of-transferz-part-1-n-3mjf>

我们开始吧...

## 想法

构建一个用于在你的设备之间无线传输文件的 [flutter](https://flutter.dev/) 应用程序，大部分是用 [Dart 语言](https://www.dartlang.org/)编写的，真的很棒。

### 信息

transferZ 是一个开源项目，在这里可以找到整个源代码[。请随意提交您的简历。](https://github.com/itzmeanjan/transferZ)

现在，你可以[下载](https://github.com/itzmeanjan/transferZ/releases/download/v1.0/transferZ-1.0.apk)并在你的设备上测试 *transferZ* 。

今天我们将实现这个 Flutter 应用程序的客户端-服务器部分。

在这个系列的最后，你将有一个工作的文件传输应用程序。

> 想这么做吗？
> 
> 是
> 
> 好吧，跟我来😉

## 型号

一个器件可以同时在这两种模式下工作。

1.  接收
2.  发送

在第一种情况下，设备将表现得像一个*客户端*，在后一种情况下，它将只是一个*服务器*。

当处于*接收*模式时，第一个*客户端*获取一个文件列表，这些文件准备好被*服务器*共享。

然后，它遍历文件列表并一个接一个地获取它们，而*服务器*继续监听传入的请求，如果请求来自允许的对等点，就为它们提供服务。

是的，就这么简单😄。

## 初始化

让我们先写一些代码，让我们的设备像一个客户端一样工作。

### 接收模式

下面的 *import* 语句将为我们获取所需的 Dart 类。

```
import 'dart:io';
import 'dart:convert' show json, utf8;
import 'dart:async'; 
```

一个名为 Client 的类将包含所有*客户端*功能，在*接收模式*中显示。

```
class Client {
  String _peerIP; // this the server IP, where we'll connect
  int _peerPort; // port on which server listens

  HttpClient _httpClient;

  Client(this._peerIP, this._peerPort) {
    _httpClient = HttpClient(); // gets initialized in constructor block
  }
} 
```

现在在我们的客户端类中添加一个名为 *connect* 的方法。

```
// method GETs a certain path, and returns a HttpClientRequest object, which is to be closed later, to send that request to server.
Future<HttpClientRequest> connect(String path) async => await _httpClient
      .get(this._peerIP, this._peerPort, path)
      .catchError((e) => null); 
```

当我们为我们的*客户端*建模时，首先获取一个文件列表，这些文件准备好由*服务器*共享，我们将在客户端类中需要一个名为 *fetchFileNames* 的方法。

```
Future<List<String>> fetchFileNames(HttpClientRequest req) async {
    // a map, stores JSON response from server
    var fileNames = <String, List<String>>{};
    // Completer class helps us to return a Future from this method and when Completer completes, result is send back.
    var completer = Completer<List<String>>();
    await req.close().then((HttpClientResponse resp) {
      if (resp.statusCode == 200)
        // in case of success, listen for data passed through Stream
        resp.listen(
          (data) => fileNames = serializeJSON(utf8.decode(data)), // decode response and then serialize it into Map<String, List<String>> 
          onDone: () => completer.complete(fileNames['files']), // extract list of file names and send that back
          onError: (e) => completer.complete(<String>[]), // send a blank list
          cancelOnError: true,
        );
      else
        // if HttpStatus other than 200, simply return a blank list
        resp.listen(
          (data) {},
          onDone: () => completer.complete(<String>[]),
          onError: (e) => completer.complete(<String>[]),
          cancelOnError: true,
        );
    });
    return completer.future;
  } 
```

关闭从前面的*连接*方法接收的 *HttpClientRequest* 对象，给我们一个 *HttpClientResponse* 对象，用于读取来自*服务器*的响应。

现在我们需要定义另一个方法 *serializeJSON* ，它将帮助我们将 JSON 响应转换成 Map <字符串，List <字符串> >。

```
Map<String, List<String>> serializeJSON(String data) =>
    return Map<String, dynamic>.from(json.decode(data))
        .map((key, val) => MapEntry(key, List<String>.from(val))); 
```

> 我们现在有什么？一个字符串列表，是从服务器获取的文件名。

让我们从*服务器*获取这些文件。在*客户端*类中添加一个新方法 *fetchFile* 。

```
Future<bool> fetchFile(HttpClientRequest req, String targetPath) async {
    var completer = Completer<bool>(); // returns a future of boolean type, to denote success or failure
    await req.close().then((HttpClientResponse resp) {
      if (resp.statusCode == 200)
        File(targetPath).openWrite(mode: FileMode.write).addStream(resp).then(
              (val) => completer.complete(true),
              onError: (e) => completer.complete(false),
            ); // file opened in write mode, and added response Stream into file.
      else
        resp.listen(
          (data) {},
          onDone: () => completer.complete(false),
          onError: (e) => completer.complete(false),
          cancelOnError: true,
        ); // in case of error, simply return false to denote failure
    });
    return completer.future;
  } 
```

如果文件被正确提取，方法返回 true，否则返回 false

还有最后一个方法，名为 *disconnect* 用于关闭 HttpClient 连接。

```
disconnect() =>
      // never force close a http connection, by passing force value attribute true.
      _httpClient.close(); 
```

我们已经完成了客户端声明😎。

### 发送模式

回到*服务器*。让我们首先定义一个类，名为 Server。

```
import 'dart:io';
import 'dart:convert' show json;

class Server {
  String _host; // IP where on which server listens
  int _port; // port on which server listens for incoming connections
  List<String> _filteredPeers; // list of IP, which are permitted to request files
  List<String> _files; // files ready to be shared
  ServerStatusCallBack _serverStatusCallBack; // Server status callback, lets user know, what's happening
  bool isStopped = true; // server state denoter

  Server(this._host, this._port, this._filteredPeers, this._files,
      this._serverStatusCallBack); // constructor defined 
```

为了控制*服务器*，我们需要在这个类中声明另一个变量。

```
 HttpServer _httpServer; 
```

让我们开始监听传入的连接。

```
initServer() async {
    await HttpServer.bind(this._host, this._port).then((HttpServer server) {
    // binds server on specified IP and port
      _httpServer = server; // initializes _httpServer, which will be useful for controlling server functionalities
      isStopped = false; // server state changed
      _serverStatusCallBack.generalUpdate('Server started'); // lets user know about it, using callback function
    }, onError: (e) {
      isStopped = true;
      _serverStatusCallBack.generalUpdate('Failed to start Server');
    });
    // listens for incoming request in asynchronous fashion
    await for (HttpRequest request in _httpServer) {
      handleRequest(request); // handles incoming request
    }
  } 
```

为了处理传入的连接，在我们的服务器类中添加一个名为 *handleRequest* 的方法。

```
handleRequest(HttpRequest request) {
    // first checks whether device allowed to request file or not
    if (isPeerAllowed(request.connectionInfo.remoteAddress.address)) {
      if (request.method == 'GET')
        handleGETRequest(request); // only GET is permitted
      else
        request.response
          ..statusCode = HttpStatus.methodNotAllowed
          ..headers.contentType = ContentType.json
          ..write(json.encode(
              <String, int>{'status': 'GET method only'}))
          ..close().then(
              (val) => _serverStatusCallBack.updateServerStatus({
                    request.connectionInfo.remoteAddress.host:
                        'GET method only'}), onError: (e) {
            _serverStatusCallBack.updateServerStatus({
              request.connectionInfo.remoteAddress.host:
                  'Transfer Error'
            });
          }); // otherwise let client know about it by sending a JSON response, where HTTP statusCode is set as HttpStatus.methodNotAllowed
    } else
      request.response
        ..statusCode = HttpStatus.forbidden
        ..headers.contentType = ContentType.json
        ..write(
            json.encode(<String, int>{'status': 'Access denied'}))
        ..close().then(
            (val) =>
                _serverStatusCallBack.generalUpdate('Access Denied'),
            onError: (e) {
          _serverStatusCallBack.generalUpdate('Transfer Error');
        }); // if client is not permitted to access
  } 
```

在*ispeerlowed*中检查对等体是否可以被授权访问。

```
bool isPeerAllowed(String remoteAddress) =>
      this._filteredPeers.contains(remoteAddress); // _filteredPeers was supplied during instantiating this class 
```

用 *handleGETRequest* 处理过滤后的 GET 请求的时间。

```
handleGETRequest(HttpRequest getRequest) {
    if (getRequest.uri.path == '/') {
    // client hits at `http://hostAddress:port/`, for fetching list of files to be shared 
      getRequest.response
        ..statusCode = HttpStatus.ok // statusCode 200
        ..headers.contentType = ContentType.json // JSON response sent
        ..write(json.encode(<String, List<String>>{"files": this._files})) // response to be processed by client for converting JSON string back to Map<String, List<String>>
        ..close().then(
            (val) => _serverStatusCallBack.updateServerStatus({
                  getRequest.connectionInfo.remoteAddress.host:
                      'Accessible file list shared'
                }), onError: (e) {
          _serverStatusCallBack.updateServerStatus({
            getRequest.connectionInfo.remoteAddress.host:
                'Transfer Error'
          }); // in case of error
        });
    } else {
      if (this._files.contains(getRequest.uri.path)) {
    // if client hits at `http://hostIP:port/filePath`, first it's checked whether this file is supposed to be shared or not
        String remote = getRequest.connectionInfo.remoteAddress.host;
        getRequest.response.statusCode = HttpStatus.ok;
        _serverStatusCallBack.updateServerStatus({
          getRequest.connectionInfo.remoteAddress.host:
              'File fetch in Progress'
        });
        // then file is opened and added into response Stream
        getRequest.response
            .addStream(File(getRequest.uri.path).openRead())
            .then(
          (val) {
            getRequest.response.close(); // close connection
            _serverStatusCallBack
                .updateServerStatus({remote: 'File fetched'});
          },
          onError: (e) {
            _serverStatusCallBack
                .updateServerStatus({remote: 'Transfer Error'}); // in case of error
          },
        );
      }
    }
  } 
```

最后但同样重要的是，一种停止服务器的方法。

```
stopServer() {
    isStopped = true;
    _httpServer?.close(force: true);
    _serverStatusCallBack.generalUpdate('Server stopped');
  } 
```

这都是关于*服务器*😎。

现在定义一个抽象类 *ServerStatusCallBack* ，以促进回调功能。这个抽象类需要被实现，我们计划从这里开始，控制和停止*服务器*。

```
abstract class ServerStatusCallBack {
  updateServerStatus(Map<String, String> msg); // a certain peer specific message

  generalUpdate(String msg); // general message about current server state
} 
```

## 运行

是时候运行我们的客户了。

### 接收模式

目前，我将简单地从命令行应用程序运行客户端，这将在后续文章中更新，当我们在 flutter 中到达 UI 上下文时。

```
main() {
  var client = Client('x.x.x.x', 8000); // x.x.x.x -> server IP and 8000 is port number
// feel free to change port number, try keeping it >1024
  client.connect('/').then((HttpClientRequest req) {
    if (req != null) // checks whether server is down or not
      client.fetchFileNames(req).then((List<String> targetFiles) { // first fetch list of files by sending a request in `/` path
        targetFiles.forEach((path) { // fetch files, one by one
          client.connect(path).then((HttpClientRequest req) { // request for a file, with that file's name as request path
            if (req != null) { // if server finds request eligible
              print('fetching ${getTargetFilePath(path)}');
              client
                  .fetchFile(req, getTargetFilePath(path)) // fetch file
                  .then((bool isSuccess) { // after it's done, check for transfer status
                print(isSuccess
                    ? 'Successfully downloaded file'
                    : 'Failed to download file');
                if (targetFiles.last == path) {
                  print('complete');
                  client.disconnect(); // disconnect client, when we find that all files has been successfully fetched
                }
              });
            } else
              print('Connection Failed');
          });
        });
        if (targetFiles.isEmpty) {
          print('incomplete');
          client.disconnect(); // there might be a situation when client not permitted to access files, then we get an empty list of files, it's handled here
        }
      });
    else
      print('Connection Failed'); // couldn't connect to server, may be down
  });
}

// returns path where client will store fetched files
String getTargetFilePath(String path) =>
    '/path-to-target-directory-to-store-fetched-file/${path.split('/').last}'; 
```

### 发送模式

在服务器的情况下，我们将暂时保持它的命令行可执行性。

让我们首先定义一个类 *ServerDriver* ，它实现 *ServerStatusCallBack* ，获取服务器状态更新。

```
class ServerDriver extends ServerStatusCallBack {
  Server server;

  @override
  updateServerStatus(Map<String, String> msg) =>
    msg.forEach((key, val) =>
      print('$key -- $val')); // peer specific message, where key of Map<String, String> is peerIP

  @override
  generalUpdate(String msg) => print(msg); // general status update about server

  init() {
    server = Server(
        '0.0.0.0',
        8000,
        <String>['192.168.1.103', '192.168.1.102'], // these are allowed peers( clients ), can request for fetching files
        <String>[
          '/path-to-file/image.png',
          '/path-to-file/spidy_love.zip',
        ],
        this); // this -- because ServerStatusCallBack is implemented in this class
  }

  start() {
    server.initServer(); // starts receiving incoming requests
  }
} 
```

最后 *main()* 函数创建了 *ServerDriver* 的一个实例，启动服务器。

```
main() {
  var serverDriver = ServerDriver();
  serverDriver.init(); // binds server in a speficied address and port
  serverDriver.start(); // starts accepting incoming requests
} 
```

[![Client Server running on a Desktop](img/db5e816ae5c564e15e3746e59219b9d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2rkBtm-f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5zgk835gm2f3pmygj48.png)

记住，这个客户机-服务器程序在局域网(LAN)中工作。有兴趣在万工作，给它一个尝试👍。

## 接下来

目前我们有两个工作程序，一个客户端和一个服务器，完全用 [Dart 语言](https://www.dartlang.org/)编写，最终将被放入 [Flutter](https://flutter.dev/) 应用[T5】transferZT7】中。](https://github.com/itzmeanjan/transferZ)

在本系列的下一篇文章中，我们将构建 [*transferZ*]((https://github.com/itzmeanjan/transferZ)) 的*对等发现*部分。

同时叉这个[回购](https://github.com/itzmeanjan/transferZ)，试着玩一下。

你可以考虑在 [Twitter](https://twitter.com/meanjanry) 和 [GitHub](https://github.com/itzmeanjan) 上关注我，了解更多消息。

再见😉。