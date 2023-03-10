# 使用 Wails 在 Go 中构建桌面应用程序

> 原文：<https://dev.to/plutov/building-desktop-app-in-go-using-wails-1poh>

[![wails+golang](img/c6cc5c7f35939a78ab7bb683022a2f08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CmVvLnde--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5p8l7ded7ljdya0p9hla.jpg)

这篇帖子是 [packagemain #6:使用 Wails](https://www.youtube.com/watch?v=Dg9rUXxNV-c) 视频在 Go 中构建桌面应用的文字版。

众所周知，Go 大多用于构建 API、web 后端、CLI 工具。但有趣的是，围棋可以用在我们意想不到的地方。

比如我们可以用 Go 和 Vue.js 用 [Wails](https://wails.app) 框架搭建一个桌面 App。

这个框架是新的，仍处于测试阶段，但我很惊讶用它开发、构建和打包一个应用程序是如此容易。

Wails 提供了将 Go 代码和 web 前端打包成一个二进制文件的能力。Wails CLI 通过处理项目创建、编译和捆绑，使这一点变得简单。

## App

我们将构建一个非常简单的应用程序来实时显示我的机器的 CPU 使用情况。如果你有时间并且喜欢哭，你可以想出一些更有创意和更复杂的东西。

## 安装

Wails CLI 可以用`go get`安装。安装后，您应该使用`wails setup`命令进行设置。

```
go get github.com/wailsapp/wails/cmd/wails
wails setup 
```

然后让我们用名称`cpustats` :
引导我们的项目

```
wails init
cd cpustats 
```

我们的项目由 Go 后端和 Vue.js 前端组成。`main.go`将是我们的入口点，我们可以在其中包含任何其他依赖项，还有一个`go.mod`文件来管理它们。`frontend`文件夹包含 Vue.js 组件、webpack 和 CSS。

## 概念

后端和前端之间共享数据有两个主要组件:绑定和事件。

绑定是一种允许你向前端公开(绑定)你的 Go 代码的方法。

此外，Wails 提供了一个统一的事件系统，类似于 Javascript 的本地事件系统。这意味着从 Go 或 Javascript 发出的任何事件都可以被任何一方获得。数据可以与任何事件一起传递。这允许你做一些简单的事情，比如让后台进程在 Go 中运行，并通知前台任何更新。

## 后端

让我们先开发一个后端部分，使用`bind`方法获取 CPU 使用率并将其发送到前端。

我们将创建一个新的包，并定义一个我将向前端公开(绑定)的类型。

pkg/sys/sys.go:

```
package sys

import (
    "math"
    "time"

    "github.com/shirou/gopsutil/cpu"
    "github.com/wailsapp/wails"
)

// Stats .
type Stats struct {
    log *wails.CustomLogger
}

// CPUUsage .
type CPUUsage struct {
    Average int `json:"avg"`
}

// WailsInit .
func (s *Stats) WailsInit(runtime *wails.Runtime) error {
    s.log = runtime.Log.New("Stats")
    return nil
}

// GetCPUUsage .
func (s *Stats) GetCPUUsage() *CPUUsage {
    percent, err := cpu.Percent(1*time.Second, false)
    if err != nil {
        s.log.Errorf("unable to get cpu stats: %s", err.Error())
        return nil
    }

    return &CPUUsage{
        Average: int(math.Round(percent[0])),
    }
} 
```

如果你的结构有一个`WailsInit`方法，Wails 会在启动时调用它。这允许您在主应用程序启动之前进行一些初始化。

在`main.go`中导入`sys`包，并将 Stats 实例绑定到前端:

```
package main

import (
    "github.com/leaanthony/mewn"
    "github.com/plutov/packagemain/cpustats/pkg/sys"
    "github.com/wailsapp/wails"
)

func main() {
    js := mewn.String("./frontend/dist/app.js")
    css := mewn.String("./frontend/dist/app.css")

    stats := &sys.Stats{}

    app := wails.CreateApp(&wails.AppConfig{
        Width:  512,
        Height: 512,
        Title:  "CPU Usage",
        JS:     js,
        CSS:    css,
        Colour: "#131313",
    })
    app.Bind(stats)
    app.Run()
} 
```

## 前端

我们从 Go 绑定了`stats`实例，可以通过 callind `window.backend.Stats`在前端使用。如果我们想调用一个函数`GetCPUUsage()`，它会返回给我们一个承诺。

```
window.backend.Stats.GetCPUUsage().then(cpu_usage => {
    console.log(cpu_usage);
}) 
```

要将整个项目构建成单一的二进制文件，我们应该运行`wails build`，可以添加`-d`标志来构建一个可调试的版本。它将创建一个名称与项目名称匹配的二进制文件。

让我们通过简单地在屏幕上显示 CPU 使用率值来测试它是否工作:

```
wails build -d
./cpustats 
```

## 事件

我们使用绑定将 CPU 使用率值发送到前端，现在让我们尝试不同的方法，让我们在后端创建一个计时器，它将使用事件方法在后台发送 CPU 使用率值。然后我们可以用 Javascript 订阅事件。

在围棋中我们可以在`WailsInit`函数中完成:

```
func (s *Stats) WailsInit(runtime *wails.Runtime) error {
    s.log = runtime.Log.New("Stats")

    go func() {
        for {
            runtime.Events.Emit("cpu_usage", s.GetCPUUsage())
            time.Sleep(1 * time.Second)
        }
    }()

    return nil
} 
```

在 Vue.js 中我们可以在组件挂载时(或者任何其他地方)订阅这个事件:

```
mounted: function() {
    wails.events.on("cpu_usage", cpu_usage => {
        if (cpu_usage) {
            console.log(cpu_usage.avg);
        }
    });
} 
```

## 标尺栏

用一个标尺栏来显示 CPU 的使用情况会很好，所以我们将包含一个第三方依赖项，只需使用`npm` :

```
npm install --save apexcharts
npm install --save vue-apexcharts 
```

然后导入到`main.js`文件:

```
import VueApexCharts from 'vue-apexcharts'

Vue.use(VueApexCharts)
Vue.component('apexchart', VueApexCharts) 
```

现在我们可以使用 apexcharts 显示我们的 CPU 使用情况，并通过从后端接收一个事件来更新组件的值:

```
<template>
  <apexchart type="radialBar" :options="options" :series="series"></apexchart>
</template>

<script>
export default {
  data() {
    return {
      series: [0],
      options: {
        labels: ['CPU Usage']
      }
    };
  },
  mounted: function() {
    wails.events.on("cpu_usage", cpu_usage => {
      if (cpu_usage) {
        this.series = [ cpu_usage.avg ];
      }
    });
  }
};
</script> 
```

要改变样式，我们可以直接修改`src/assets/css/main.css`或在组件中定义它们。

## 最终构建并运行

```
wails build -d
./cpustats 
```

[![desktop-app-wails](img/0d9d581bb05b2d19a694dae943d9a18f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xhgS-90w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xsnfdsvr9hn4u66v025q.png)

## 结论

我真的很喜欢使用`Wails`，事件概念使得控制应用程序的状态变得非常容易。

在 [wails.app](https://wails.app) 或 github.com/wailsapp/wails[Github](https://github.com/wailsapp/wails)查看

[GitHub 上这篇文章的完整代码](https://github.com/plutov/packagemain/tree/master/16-wails-desktop-app)