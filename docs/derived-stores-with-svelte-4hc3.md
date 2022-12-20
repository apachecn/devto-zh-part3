# 苗条的衍生商店

> 原文：<https://dev.to/teej/derived-stores-with-svelte-4hc3>

这最初是在我的网站上发布的。

最近，苗条的身材得到了很多关注，这是理所当然的。如果你过去使用过 Vue 或 React 之类的产品，那么 Svelte 将不会遥不可及。如果你感兴趣，我推荐跟随[他们的互动教程](https://svelte.dev/tutorial/basics)。

来自 Vue 背景，我喜欢的一个特性是计算属性。如果您想存储某种依赖于反应状态的值，但又不想在状态改变时手动更新它，这就是它们的用武之地。例如，`greeting`属性可以返回类似于`"Hello, ${this.name}!"`的内容。Vue 知道每当`name`改变时就更新`greeting`。

Svelte 在其所谓的衍生商店中提供了类似的解决方案。为了展示这是如何工作的，我创建了一个小应用程序，它接收 JSON 提要并让我们对其进行过滤。该提要包含一个工作列表，我希望能够通过职位搜索，并通过复选框只显示远程工作。

初始储备如下:

```
import { writable } from 'svelte/store'

export const jobs = writable([])
export const searchTerm = writable('')
export const remoteOnly = writable(false) 
```

Enter fullscreen mode Exit fullscreen mode

在某个阶段，当应用程序准备就绪时，`jobs`存储中会填充来自提要的一系列作业。当我输入搜索输入时,`searchTerm`商店被更新，当我切换仅远程复选框时,`remoteOnly`商店被切换。

理想情况下，我想做的是避免编辑`jobs`商店。我想保持原来的列表不变，这样我就可以回到原来的状态。

这是我可以使用派生存储的地方。首先，我需要通过更新顶级导入语句来导入它:

```
import { writable, derived } from 'svelte/store' 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以声明我的派生存储了。

```
export const filteredJobs = derived(
  [jobs, remoteOnly, searchTerm],
  ([$jobs, $remoteOnly, $searchTerm]) => {
    return $jobs
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数是我希望从中派生的一个或多个商店。如果希望使用多个数组，只需传递一个数组。这里的第二个参数是回调，它传递给我们请求的存储。目前，我们原封不动地退回原来的商店。让我们创建一个函数来只显示远程作业:

```
function filterByRemote(jobs, remoteOnly) {
  if (!remoteOnly) return jobs

  return jobs.filter(job => !job.location)
}

export const filteredJobs = derived(
  [jobs, remoteOnly, searchTerm],
  ([$jobs, $remoteOnly, $searchTerm]) => {
    return filterByRemote($jobs, $remoteOnly)
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

这里的远程作业是指没有设置位置的任何作业。如果`remoteOnly`被设置为`false`，我们将提前返回作业数组。

对搜索词采取类似的方法。这不是最强大的搜索引擎，但它能完成任务:

```
function filterByRemote(jobs, remoteOnly) {
  if (!remoteOnly) return jobs

  return jobs.filter(job => !job.location)
}

function filterBySearchTerm(jobs, searchTerm) {
  if (!searchTerm) return jobs

  return jobs.filter(job => {
    const title = job.title.toLowerCase().replace('-', '  ')
    const formattedTerm = searchTerm.toLowerCase().trim()

    return title.includes(formattedTerm)
  })
}

export const filteredJobs = derived(
  [jobs, remoteOnly, searchTerm],
  ([$jobs, $remoteOnly, $searchTerm]) => {
    return filterBySearchTerm(filterByRemote($jobs, $remoteOnly), $searchTerm)
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们从内向外工作，我们会看到`filterByRemote`返回一个作业数组，然后这个数组成为调用`filterBySearchTerm`的第一个参数。

那是我第一次体验苗条。希望不是最后一次，因为这是对我之前所做的事情的一次全新尝试。

[查看演示](https://codepen.io/tjFogarty/project/details/XqqnVO)
[查看来源](https://github.com/tjFogarty/codepen-job-board-svelte)