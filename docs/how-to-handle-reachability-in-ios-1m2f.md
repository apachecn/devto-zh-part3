# 如何在 iOS 中处理可达性

> 原文：<https://dev.to/onmyway133/how-to-handle-reachability-in-ios-1m2f>

以下是我对 iOS 中可达性处理的了解，也就是检查互联网连接。希望你也会觉得有用。

这篇文章从客观时代的技术开始，但是许多概念仍然适用

## 天真的道

你在 UIKit 中已经知道的一些 API 可以用来检查互联网连接。大多数都是同步代码，所以你最好在后台线程中调用它们

```
- (BOOL)connectedToInternet
{
    NSString *string = [NSString stringWithContentsOfURL:[NSURL URLWithString:@"http://www.google.com"]
                                                encoding:NSUTF8StringEncoding
                                                   error:nil];

    return string ? YES : NO;
} 
```

```
- (BOOL)connectedToInternet
{
   NSURL *url = [NSURL URLWithString:@"http://www.google.com"];
   NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
   [request setHTTPMethod:@"HEAD"];
   NSHTTPURLResponse *response;
   [NSURLConnection sendSynchronousRequest:request returningResponse:&response error: NULL];

   return ([response statusCode] == 200) ? YES : NO;
} 
```

## 使用系统配置框架

导入 SystemConfiguration 框架后，您可以使用 SCNetworkReachabilityGetFlags 同步获取可达性状态，或者提供对 SCNetworkReachabilitySetCallback 的回调，以获得可达性状态更改的通知。

注意`SCNetworkReachabilityGetFlags`是同步的。

> 默认情况下，系统配置框架可达性 API()同步运行。因此，像 SCNetworkReachabilityGetFlags 这样看似无害的例程可能会让您被看门狗杀死。如果您正在使用可达性 API，那么您应该异步使用它。这包括使用 SCNetworkReachabilityScheduleWithRunLoop 例程来调度运行循环
> 上的可达性查询

```
- (BOOL) isConnectionAvailable
{
    SCNetworkReachabilityFlags flags;
        BOOL receivedFlags;

        SCNetworkReachabilityRef reachability = SCNetworkReachabilityCreateWithName(CFAllocatorGetDefault(), [@"dipinkrishna.com" UTF8String]);
        receivedFlags = SCNetworkReachabilityGetFlags(reachability, &flags);
        CFRelease(reachability);

        if (!receivedFlags || (flags == 0) )
        {
            return FALSE;
        } else {
        return TRUE;
    }
} 
```

请注意，SCNetworkReachabilitySetCallback 仅在可达性状态发生变化时才发出通知

> 将客户端分配给指定的目标，当目标的可访问性发生变化时，该目标将接收回调

## 使用一些库

图书馆使我们的生活变得更容易，但要想和它们相处得好，你必须了解它们。Github 上有很多可达性库，但这里我想提一下最受欢迎的:来自 tonymillion 的可达性和来自 mattt 的 AFNetworkReachabilityManager(AFNetworking 的子模块)。两者都使用系统配置。

## 可达性

有些人是这样使用可达性的

```
- (void)testInternetConnection
{   
    internetReachableFoo = [Reachability reachabilityWithHostname:@"www.google.com"];

    // Internet is reachable
    internetReachableFoo.reachableBlock = ^(Reachability*reach)
    {
        // Update the UI on the main thread
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"Yayyy, we have the interwebs!");
        });
    };

    // Internet is not reachable
    internetReachableFoo.unreachableBlock = ^(Reachability*reach)
    {
        // Update the UI on the main thread
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"Someone broke the internet :(");
        });
    };

    [internetReachableFoo startNotifier];
} 
```

查看方法“startNotifier ”,您将看到它只使用 SCNetworkReachabilitySetCallback，这意味着只有在可达性状态发生变化时才会调用这个回调。

如果您想直接了解可达性状态，例如，应用程序启动时的可达性状态，您必须使用“isReachable”方法。这个方法使用同步的 SCNetworkReachabilityGetFlags，并锁定调用线程。

可达性有 reachabilityForLocalWiFi，有意思:)

```
+(Reachability*)reachabilityForLocalWiFi
{
    struct sockaddr_in localWifiAddress;
    bzero(&localWifiAddress, sizeof(localWifiAddress));
    localWifiAddress.sin_len            = sizeof(localWifiAddress);
    localWifiAddress.sin_family         = AF_INET;
    // IN_LINKLOCALNETNUM is defined in <netinet/in.h> as 169.254.0.0
    localWifiAddress.sin_addr.s_addr    = htonl(IN_LINKLOCALNETNUM);

    return [self reachabilityWithAddress:&localWifiAddress];
} 
```

## afnetworkreachabilitymanager

有了 AFNetworkReachabilityManager，你所要做的就是

```
- (void)trackInternetConnection
{
    [[AFNetworkReachabilityManager sharedManager] startMonitoring];
    [[AFNetworkReachabilityManager sharedManager] setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
        // Handle the status
    }];
} 
```

AFNetworkReachabilityManager 的好处在于，在“startMonitoring”方法中，它既使用 SCNetworkReachabilitySetCallback，又调用 AFNetworkReachabilityStatusForFlags 来获取后台线程中的初始可达性状态，并调用 AFNetworkReachabilityStatusBlock。所以从用户的角度来看，我们关心的只是 AFNetworkReachabilityStatusBlock 处理程序。

AFNetworking 具有可达性具有的所有特性，并且其代码结构良好。另一件很酷的事情是它已经在你的 AFNetworking pod 中了。现在很难找到没有人脉的项目

## isReachableViaWWAN vs isReachableViaWiFi

看一看 AFNetworkReachabilityStatusForFlags 方法，你就知道这个故事了

```
static AFNetworkReachabilityStatus AFNetworkReachabilityStatusForFlags(SCNetworkReachabilityFlags flags) {
    [...]
    status = AFNetworkReachabilityStatusUnknown;
    if (isNetworkReachable == NO) {
        status = AFNetworkReachabilityStatusNotReachable;
    }
#if TARGET_OS_IPHONE
    else if ((flags & kSCNetworkReachabilityFlagsIsWWAN) != 0) {
        status = AFNetworkReachabilityStatusReachableViaWWAN;
    }
#endif
    else {
        status = AFNetworkReachabilityStatusReachableViaWiFi;
    }

    return status;
} 
```

`isReachableViaWWAN`应该是针对 iOS 设备的

## 如何使用 AFNetworkReachabilityManager

我在这里问了一个问题[第 2262 期](https://github.com/AFNetworking/AFNetworking/issues/2262)，你应该看看

安全的方法是不使用 sharedManager，而是使用 managerForDomain

```
AFNetworkReachabilityManager *afReachability = [AFNetworkReachabilityManager managerForDomain:@"www.google.com"];
    [afReachability setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
        if (status < AFNetworkReachabilityStatusReachableViaWWAN) {
            [FTGAlertView showMessage:@"No internet connection"];
        }
    }];

    [afReachability startMonitoring]; 
```

您应该阅读下面参考资料中的问题 7 和 8，以了解有关 SCNetworkReachabilityCreateWithName vs scnetworkreachabilitycreatewithdress 以及零地址的更多信息

## [T1】reachability . swift](#reachabilityswift)

在 Swift 中，有一种流行的[可达性。swift](https://github.com/ashleymills/Reachability.swift) 用于检查网络可达性状态

## 连通性

有时候，一个更可靠的方法是 ping 特定的服务器，这就是 Connectivy 的工作方式

此外，阅读更多信息[解决 iOS 上的强制网络门户问题](https://medium.com/@rwbutler/solving-the-captive-portal-problem-on-ios-9a53ba2b381e)

> 为了检测它已经连接到带有强制网络门户的 Wi-Fi 网络，iOS 联系了苹果托管的许多端点——一个例子是[https://www.apple.com/library/test/success.html](https://www.apple.com/library/test/success.html)。每个端点都有一个小的 HTML 页面，格式如下:

## 阅读更多

1.  [可达性](https://github.com/tonymillion/Reachability)
2.  [af networkreachable ymanager](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking/AFNetworkReachabilityManager.m)
3.  [如何同步检查互联网连接？](http://stackoverflow.com/questions/24535842/how-to-check-for-internet-connection-synchronously/25133851#25133851)
4.  [iOS:检查互联网连接是否可用](http://dipinkrishna.com/blog/2012/04/ios-check-internet-connection/)
5.  [检查 iOS 设备上是否存在活跃的互联网连接](http://ios-blog.co.uk/tutorials/check-if-active-internet-connection-exists-on-ios-device/)
6.  [技术问&一个 QA1693 在主线程上同步组网](https://developer.apple.com/library/ios/qa/qa1693/_index.html)
7.  [如何以非阻塞的方式在 iOS 上检查网络可达性？](http://stackoverflow.com/a/11314946/1418457)
8.  [了解套接字编程的 in addr _ ANY-c](http://stackoverflow.com/questions/16508685/understanding-inaddr-any-for-socket-programming-c)

原帖[https://github.com/onmyway133/blog/issues/209](https://github.com/onmyway133/blog/issues/209)