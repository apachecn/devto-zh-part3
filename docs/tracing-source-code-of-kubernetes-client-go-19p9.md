# 追踪 Kubernetes 客户端的源代码-go

> 原文：<https://dev.to/dannypsnl/tracing-source-code-of-kubernetes-client-go-19p9>

整个事情就是从**入口**开始的 **Kubernetes** 的这个特点。但是今天我不打算谈论太多，基本上只是我必须让**入口控制器**将数据包发送到我们的**路由器**，这样我们就可以做我们想做的事情，如果你对我们的**路由器**感兴趣，你可以从[我们的博客](https://glasnostic.com/blog)获得更多信息，并通过登录来演示。

无论如何，我要做的事情是，我必须为真正的 kubernetes API 服务器创建一个代理，并根据我们的需要修改真正的数据。要做到这一点，我必须理解 [client-go](https://github.com/kubernetes/client-go) ( **入口**使用 client-go 来获取信息)如何发送请求，以及它期望什么。开始吧！

> 注意:我只是提到了部分代码，并没有解释整个大问题

```
 epEventHandler := cache.ResourceEventHandlerFuncs{
        AddFunc: func(obj interface{}) {
            updateCh.In() <- Event{
                Type: CreateEvent,
                Obj: obj,
            }
        },
        DeleteFunc: func(obj interface{}) {
            updateCh.In() <- Event{
                Type: DeleteEvent,
                Obj: obj,
            }
        },
        UpdateFunc: func(old, cur interface{}) {
            oep := old.(*corev1.Endpoints)
            cep := cur.(*corev1.Endpoints)
            if !reflect.DeepEqual(cep.Subsets, oep.Subsets) {
                updateCh.In() <- Event{
                    Type: UpdateEvent,
                    Obj: cur,
                }
            }
        },
    } 
```

这些代码在[入口-nginx](https://github.com/kubernetes/ingress-nginx) 标签`nginx-v0.20.0`(在下面的上下文中我们只使用这个标签)，文件`internal/ingress/controller/store/store.go`行`446`

目的是将这些回调发送到`SharedInformer`中，以获取 kubernetes 事件来更新存储中的数据，生成 nginx 配置来平衡这些 pods 的负载。

好，那么我们在哪里使用`epEventHandler`？我们会看到它在同一个函数中被传递给`store.informers.Endpoint`，行`519`

```
store.informers.Endpoint.AddEventHandler(epEventHandler) 
```

这里我们应该关心两件事

*   什么是`Endpoint`？
*   它如何使用发送到`AddEventHandler`中的函数？

让我们继续深入研究代码，我们会看到`AddEventHandler`是一个`interface` : `SharedInformer`的方法，是的，我们只是谈论它，现在我们看到了。`SharedInformer`是在`k8s.io/client-go/tools/cache/shared_informer.go`下定义的(记住，这里我追踪的是`ingress-nginx`厂商下的`client-go`，所以它可能会和最新的`client-go`一起过时)

`SharedInformer`的唯一实现者是`sharedIndexInformer`(仍然在同一个文件中)，它是一个结构，这里是`AddEventHandler`T3 的真实代码

```
func (s *sharedIndexInformer) AddEventHandler(handler ResourceEventHandler) {
    s.AddEventHandlerWithResyncPeriod(handler, s.defaultEventHandlerResyncPeriod)
}

func (s *sharedIndexInformer) AddEventHandlerWithResyncPeriod(handler ResourceEventHandler, resyncPeriod time.Duration) {
    // ignore, here would do some period syncing
    listener := newProcessListener(handler, resyncPeriod, determineResyncPeriod(resyncPeriod, s.resyncCheckPeriod), s.clock.Now(), initialBufferSize)
    // ignore, here would emit `listener` into `processer`
}

func newProcessListener(handler ResourceEventHandler, requestedResyncPeriod, resyncPeriod time.Duration, now time.Time, bufferSize int) *processorListener {
    ret := &processorListener{
        nextCh: make(chan interface{}),
        addCh: make(chan interface{}),
        handler: handler,
        pendingNotifications: *buffer.NewRingGrowing(bufferSize),
        requestedResyncPeriod: requestedResyncPeriod,
        resyncPeriod: resyncPeriod,
    }

    ret.determineNextResync(now)

    return ret
} 
```

到这里，我们应该停止这一部分，因为我们不能从这些中得到更多。所以我回到如何使用`sharedIndexInformer`

我发现类型`store.informers`有一个方法`Run`会被存储调用，这意味着它调用的是我们关心的点，那就是`store.informers.Endpoint`T3】

```
func (i *Informer) Run(stopCh chan struct{}) {
    // this is *sharedIndexInformer.Run
    go i.Endpoint.Run(stopCh)
    // ignore, all resource is working under the same way
}

func (s *sharedIndexInformer) Run(stopCh <-chan struct{}) {
    // this is last line, I ignore others codes
    s.controller.Run(stopCh)
} 
```

然后我来看看控制器是如何工作的

```
// Run begins processing items, and will continue until a value is sent down stopCh.
// It's an error to call Run more than once.
// Run blocks; call via go.
func (c *controller) Run(stopCh <-chan struct{}) {
    defer utilruntime.HandleCrash()
    go func() {
        <-stopCh
        c.config.Queue.Close()
    }()
    r := NewReflector(
        c.config.ListerWatcher,
        c.config.ObjectType,
        c.config.Queue,
        c.config.FullResyncPeriod,
    )
    r.ShouldResync = c.config.ShouldResync
    r.clock = c.clock

    c.reflectorMutex.Lock()
    c.reflector = r
    c.reflectorMutex.Unlock()

    var wg wait.Group
    defer wg.Wait()

    wg.StartWithChannel(stopCh, r.Run)

    wait.Until(c.processLoop, time.Second, stopCh)
} 
```

重点是`wg.StartWithChannel(stopCh, r.Run)`，在`reflector.Run`中，它叫`r.ListAndWatch(stopCh)`，而`ListAndWatch`是基于`listWatcher`T5

```
list, err := r.listerWatcher.List(options)
if err != nil {
    return fmt.Errorf("%s: Failed to list %v: %v", r.name, r.expectedType, err)
} 
```

我们稍后会回到这里，让我们看看什么是`listerWatcher`

我们通过这个`store.informers.Endpoint = infFactory.Core().V1().Endpoints().Informer()`在`internal/ingress/controller/store/store.go:L264`设置`store.informers.Endpoint`

然后我们看`infFactory`，行`257`

```
infFactory := informers.NewSharedInformerFactoryWithOptions(client, resyncPeriod,
    informers.WithNamespace(namespace),
    informers.WithTweakListOptions(func(*metav1.ListOptions) {})) 
```

`informer` :

```
func (f *endpointsInformer) Informer() cache.SharedIndexInformer {
    return f.factory.InformerFor(&corev1.Endpoints{}, f.defaultInformer)
}
// defaultInformer
func (f *endpointsInformer) defaultInformer(client kubernetes.Interface, resyncPeriod time.Duration) cache.SharedIndexInformer {
    return NewFilteredEndpointsInformer(client, f.namespace, resyncPeriod, cache.Indexers{cache.NamespaceIndex: cache.MetaNamespaceIndexFunc}, f.tweakListOptions)
}
// NewFilteredEndpointsInformer
func NewFilteredEndpointsInformer(client kubernetes.Interface, namespace string, resyncPeriod time.Duration, indexers cache.Indexers, tweakListOptions internalinterfaces.TweakListOptionsFunc) cache.SharedIndexInformer {
    return cache.NewSharedIndexInformer(
        &cache.ListWatch{
            ListFunc: func(options metav1.ListOptions) (runtime.Object, error) {
                if tweakListOptions != nil {
                    tweakListOptions(&options)
                }
                return client.CoreV1().Endpoints(namespace).List(options)
            },
            WatchFunc: func(options metav1.ListOptions) (watch.Interface, error) {
                if tweakListOptions != nil {
                    tweakListOptions(&options)
                }
                return client.CoreV1().Endpoints(namespace).Watch(options)
            },
        },
        &corev1.Endpoints{},
        resyncPeriod,
        indexers,
    )
} 
```

哈，我们现在得到了`ListWatch`，它将调用`*kubernetes.ClientSet`的一个实例来获得它想要的信息！

现在我们可以回到`ListAndWatch`，让我们来看看它的细节。

事实上，我更关注 watch API，因为它有点奇怪。我发现它的服务器一直在发送数据，直到客户端关闭连接。它是如何做到的？在`k8s.io/client-go/tools/cache/reflector.go:L226`

```
 for {
        // give the stopCh a chance to stop the loop, even in case of continue statements further down on errors
        select {
        case <-stopCh:
            return nil
        default:
        }

        timeoutSeconds := int64(minWatchTimeout.Seconds() * (rand.Float64() + 1.0))
        options = metav1.ListOptions{
            ResourceVersion: resourceVersion,
            // We want to avoid situations of hanging watchers. Stop any wachers that do not
            // receive any events within the timeout window.
            TimeoutSeconds: &timeoutSeconds,
        }

        r.metrics.numberOfWatches.Inc()
        w, err := r.listerWatcher.Watch(options)
        if err != nil {
            switch err {
            case io.EOF:
                // watch closed normally
            case io.ErrUnexpectedEOF:
                glog.V(1).Infof("%s: Watch for %v closed with unexpected EOF: %v", r.name, r.expectedType, err)
            default:
                utilruntime.HandleError(fmt.Errorf("%s: Failed to watch %v: %v", r.name, r.expectedType, err))
            }
            // If this is "connection refused" error, it means that most likely apiserver is not responsive.
            // It doesn't make sense to re-list all objects because most likely we will be able to restart
            // watch where we ended.
            // If that's the case wait and resend watch request.
            if urlError, ok := err.(*url.Error); ok {
                if opError, ok := urlError.Err.(*net.OpError); ok {
                    if errno, ok := opError.Err.(syscall.Errno); ok && errno == syscall.ECONNREFUSED {
                        time.Sleep(time.Second)
                        continue
                    }
                }
            }
            return nil
        }

        if err := r.watchHandler(w, &resourceVersion, resyncerrc, stopCh); err != nil {
            if err != errorStopRequested {
                glog.Warningf("%s: watch of %v ended with: %v", r.name, r.expectedType, err)
            }
            return nil
        }
    } 
```

当然是一个无止境的循环，会由航道停止或返回。

棘手的部分是它检查错误内容，如果这是一个可能的 EOF，它会继续获取数据，而不是停止连接。

好了，现在一切似乎都有意义了，但这还不够，我很困惑为什么它可以通过流的方式接收 JSON 数据，所以让我们回头看看`client.CoreV1().Endpoints(namespace).Watch(options)`

```
// Watch returns a watch.Interface that watches the requested endpoints.
func (c *endpoints) Watch(opts metav1.ListOptions) (watch.Interface, error) {
    opts.Watch = true
    return c.client.Get().
        Namespace(c.ns).
        Resource("endpoints").
        VersionedParams(&opts, scheme.ParameterCodec).
        Watch()
}
// Watch attempts to begin watching the requested location.
// Returns a watch.Interface, or an error.
func (r *Request) Watch() (watch.Interface, error) {
    return r.WatchWithSpecificDecoders(
        func(body io.ReadCloser) streaming.Decoder {
            framer := r.serializers.Framer.NewFrameReader(body)
            return streaming.NewDecoder(framer, r.serializers.StreamingSerializer)
        },
        r.serializers.Decoder,
    )
} 
```

我发现重点是`r.serializers`，糟糕的是它仍然是一个由外部代码发送的函数。

如果你追溯的话，你会发现它来自于`*RESTClient.serializers`，在`k8s.io/client-go/rest/client.go`，行`225`和`227`发送这个到`NewRequest`

你发现它是在同一个文件`serializers, err := createSerializers(config)`
的第`108`行创建的

```
func createSerializers(config ContentConfig) (*Serializers, error) {
    // ignore, we don't care them since we just use `StreamSerializer` of `Serializers`
    if info.StreamSerializer != nil {
        s.StreamingSerializer = info.StreamSerializer.Serializer
        s.Framer = info.StreamSerializer.Framer
    }

    return s, nil
} 
```

我们会看到`StreamSerializer`的类型是`runtime.Serializer`，它是一个接口，因为我们正在发送 JSON 数据，所以我们转到它的 JSON 一个实现者，看到它是`Decode`

```
import (
    jsoniter "github.com/json-iterator/go"
) 
```

看到那之后，我知道痕迹已经做了，因为我的问题已经被回答了，他们使用这个库

我想在完成我的 kube API 服务器代理后，我会谈谈如何创建一个修改数据的 kube API 代理。(真的很辛苦 XD)

我想今天我们学到的最有趣的事情是 Go `*http.Response`是一个`ReadCloser`！(Kubernetes 如何完成他们的手表魔术)

无论如何，感谢您的阅读，希望这些能帮助您更详细地理解 Kubernetes 客户端实现，并成为阅读更多相关内容的起点。