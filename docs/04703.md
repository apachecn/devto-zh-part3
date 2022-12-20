# 使命令变得复杂

> 原文：<https://dev.to/alexandlazaris/making-commands-complicated-4mfc>

有时候，做同样无聊的事情会让人失去兴趣，常常会让你的大脑变得僵死。冻结在这种不适中，无法尝试其他任何事情，你疲惫、沉闷的灵魂整晚都在输入重复的命令。

为什么不加点料呢？尝试将一个更常用的命令改为👩🏻‍💻,💪🏻和🥇。

##### 容易🚮

`git checkout virt-server-things`

#### 更好的❕

`git checkout ($(git branch | Select-String "virt") -replace " ", "")`

当然，如果有两个同名的分支，这一切都会失败...但是谁有一个以上的活动分支😅

你把哪些琐碎的命令转换成了硬模式？在你的代码库中见过任何过于复杂的代码吗？

#### 删除我在 Kubernetes 培训中使用的所有 docker 图片

`docker rmi $(docker images | grep "k8s" | awk '{ print $3 }')`