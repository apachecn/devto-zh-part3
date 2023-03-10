# 这个 Kubernetes 命令将改变你的生活

> 原文：<https://dev.to/moficodes/this-kubernetes-command-will-change-your-life-16pf>

好吧，也许不会改变你的生活。但是听我说完。如果你像我一样，经常处理 kubernetes yaml 文件，你就会知道 kubernetes 中每个原语有多少旋钮和按钮。这个命令让这个过程变得稍微容易一些。

[![](img/e08ba98d33a3a6cc9d9daf804a8d160f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uJIcTePB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8fel8i38ge7k6xsm3v2w.png)

Kubernetes 文档是开源行业中最好的文档之一。文档涵盖了运行 kubernetes 工作负载需要了解的所有主要内容，如果您想更深入地了解，您可以直接跳转到 [API 文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#-strong-api-overview-strong-)，它将为您提供这些元素必须提供的所有内容。但是文档有时会更难处理，因为大多数时候它包含的东西比你实际需要的要多。

我所说的`kubectl`让学习 kubernetes api 选项的过程好了 10 倍。

命令是`kubectl explain`。我最近才知道这件事，如果你不知道，现在你知道了。我有点生气，因为我以前不知道这件事。如果要做一个`kubectl` `--` `help`的话会在第二节出现

```
...
Basic Commands (Beginner):
  create         Create a resource from a file or from stdin.
  expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
  run            Run a particular image on the cluster
  set            Set specific features on objects

Basic Commands (Intermediate):
  explain        Documentation of resources
  get            Display one or many resources
  edit           Edit a resource on the server
  delete         Delete resources by filenames, stdin, resources and names, or by resources and label selector
... 
```

Enter fullscreen mode Exit fullscreen mode

实际上超过了我认为我用得最多的。我怎么错过解释了？

但是没有必要纠结于我在阅读 cli 文档时的失败。依赖于`explain`给你的东西。
这个命令非常简单，它返回特定类型的文档。所以类似于`kubectl explain jobs`的东西将会回归

```
KIND:     Job
VERSION:  batch/v1

DESCRIPTION:
     Job represents the configuration of a single job.

FIELDS:
   apiVersion        <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#resources

   kind        <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds

   metadata        <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

   spec        <Object>
     Specification of the desired behavior of a job. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status

   status        <Object>
     Current status of a job. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status 
```

Enter fullscreen mode Exit fullscreen mode

没有什么太疯狂的，但是解释将真正改变你的生活的地方是 JsonPath 标识符。
让我们再次使用之前的命令，并更深入地了解规范。
`kubectl explain jobs.spec`

```
KIND:     Job
VERSION:  batch/v1

RESOURCE: spec <Object>

DESCRIPTION:
     Specification of the desired behavior of a job. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-and-status

     JobSpec describes how the job execution will look like.

FIELDS:
   activeDeadlineSeconds        <integer>
     Specifies the duration in seconds relative to the startTime that the job
     may be active before the system tries to terminate it; value must be
     positive integer

   backoffLimit        <integer>
     Specifies the number of retries before marking this job failed. Defaults to
     6

   completions        <integer>
     Specifies the desired number of successfully finished pods the job should
     be run with. Setting to nil means that the success of any pod signals the
     success of all pods, and allows parallelism to have any positive value.
     Setting to 1 means that parallelism is limited to 1 and the success of that
     pod signals the success of the job. More info:
     https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/

   manualSelector        <boolean>
     manualSelector controls generation of pod labels and pod selectors. Leave
     `manualSelector` unset unless you are certain what you are doing. When
     false or unset, the system pick labels unique to this job and appends those
     labels to the pod template. When true, the user is responsible for picking
     unique labels and specifying the selector. Failure to pick a unique label
     may cause this and other jobs to not function correctly. However, You may
     see `manualSelector=true` in jobs that were created with the old
     `extensions/v1beta1` API. More info:
     https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/#specifying-your-own-pod-selector

...<output-omitted> 
```

Enter fullscreen mode Exit fullscreen mode

你可以继续挖，直到你到达单位元素。

想知道特定资源的所有可用选项是什么？
`--recursive`递归查找一个资源的所有字段。
`kubectl explain serviceaccounts --recursive`

```
KIND:     ServiceAccount
VERSION:  v1

DESCRIPTION:
     ServiceAccount binds together: * a name, understood by users, and perhaps
     by peripheral systems, for an identity * a principal that can be
     authenticated and authorized * a set of secrets

FIELDS:
   apiVersion        <string>
   automountServiceAccountToken        <boolean>
   imagePullSecrets        <[]Object>
      name        <string>
   kind        <string>
   metadata        <Object>
      annotations        <map[string]string>
      clusterName        <string>
      creationTimestamp        <string>
      deletionGracePeriodSeconds        <integer>
      deletionTimestamp        <string>
      finalizers        <[]string>
      generateName        <string>
      generation        <integer>
      initializers        <Object>
         pending        <[]Object>
            name        <string>
         result        <Object>
            apiVersion        <string>
            code        <integer>
            details        <Object>
               causes        <[]Object>
                  field        <string>
                  message        <string>
                  reason        <string>
               group        <string>
               kind        <string>
               name        <string>
               retryAfterSeconds        <integer>
               uid        <string>
            kind        <string>
            message        <string>
            metadata        <Object>
               continue        <string>
               resourceVersion        <string>
               selfLink        <string>
            reason        <string>
            status        <string>
      labels        <map[string]string>
      managedFields        <[]Object>
         apiVersion        <string>
         fields        <map[string]>
         manager        <string>
         operation        <string>
         time        <string>
      name        <string>
      namespace        <string>
      ownerReferences        <[]Object>
         apiVersion        <string>
         blockOwnerDeletion        <boolean>
         controller        <boolean>
         kind        <string>
         name        <string>
         uid        <string>
      resourceVersion        <string>
      selfLink        <string>
      uid        <string>
   secrets        <[]Object>
      apiVersion        <string>
      fieldPath        <string>
      kind        <string>
      name        <string>
      namespace        <string>
      resourceVersion        <string>
      uid        <string> 
```

Enter fullscreen mode Exit fullscreen mode

它也适用于嵌套元素。
`kubectl explain secrets.metadata--recursive`

```
KIND:     Secret
VERSION:  v1

RESOURCE: metadata <Object>

DESCRIPTION:
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#metadata

     ObjectMeta is metadata that all persisted resources must have, which
     includes all objects users must create.

FIELDS:
   annotations        <map[string]string>
   clusterName        <string>
   creationTimestamp        <string>
   deletionGracePeriodSeconds        <integer>
   deletionTimestamp        <string>
   finalizers        <[]string>
   generateName        <string>
   generation        <integer>
   initializers        <Object>
      pending        <[]Object>
         name        <string>
      result        <Object>
         apiVersion        <string>
         code        <integer>
         details        <Object>
            causes        <[]Object>
               field        <string>
               message        <string>
               reason        <string>
            group        <string>
            kind        <string>
            name        <string>
            retryAfterSeconds        <integer>
            uid        <string>
         kind        <string>
         message        <string>
         metadata        <Object>
            continue        <string>
            resourceVersion        <string>
            selfLink        <string>
         reason        <string>
         status        <string>
   labels        <map[string]string>
   managedFields        <[]Object>
      apiVersion        <string>
      fields        <map[string]>
      manager        <string>
      operation        <string>
      time        <string>
   name        <string>
   namespace        <string>
   ownerReferences        <[]Object>
      apiVersion        <string>
      blockOwnerDeletion        <boolean>
      controller        <boolean>
      kind        <string>
      name        <string>
      uid        <string>
   resourceVersion        <string>
   selfLink        <string>
   uid        <string> 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用任何资源的不同版本，您也可以使用`--api-version='<version>'`查看该文档。

我希望这有助于你的 kubernetes 之旅。

如果你喜欢这个帖子，请给我一个关注。或者在 twitter 上找我 [@moficodes](http://twitter.com/moficodes)