# 反应本地故障排除提示

> 原文：<https://dev.to/sandralundgren/react-native-tips-nek>

这个提示列表是一项正在进行的工作。

**提示# 1:地铁捆绑器错误**，如`ENOENT: no such file or directory, uv_cwd`
`jest-haste-map: watch error`

*jest-haste-map:watch Error:
Error:EIS dir:在 object . fs . read sync(fs . js:690:18)读取的目录上的非法操作...*

注意运行 Metro Bundler 的终端是一个好主意，因为那里会出现很多错误——特别是捆绑和语法错误。

要修复 jest-hast-map 或 uv_cwd 错误以及大多数其他与语法无关的问题，请运行以下命令

`$ killall node`
(该命令会杀死所有节点进程，所以不可否认有点过了，Metro Bundler 进程是您想要杀死的进程，默认情况下 Metro Bundler 运行在 8081 端口)
`$ rm -rf ios/build/ && react-native run-ios`
首先删除 iOS 构建，然后再次运行 iOS 模拟器。

`$ killall node`
T1】

**提示# 2 无法识别的字体系列“fontcustom”修复**

`$ react-native link`

您可能需要指定要链接的包名。

**提示# 3:修复落后的 iOS 模拟器**

React 本机 iOS 模拟器会变得非常慢，慢到即使打开开发工具菜单也有非常明显的滞后。如果你有这个问题，你应该检查“切换慢速动画”是否被激活。
在 iOS 模拟器中，点击“调试”——>“切换慢速动画”应该是未选中的。

**提示# 4:修复 XCode 的“应用安装失败-
已达到免费开发描述文件的最大应用数量。”**

从您的设备上删除一些使用相同描述文件安装的应用程序。
转到“窗口- >设备和模拟器”。
然后删除已安装应用部分的所有应用。
使用 SHIFT + cmd + K 清理 XCode 中的项目，并尝试再次将项目构建到 iPhone 中。

**提示# 5: XCode 在项目导航视图中显示“没有过滤结果”**

转到“导航>在项目导航器中显示”。

**提示 6:解决 99%的奇怪问题**

删除模拟器中的应用程序。关闭模拟器。
运行以下命令的全部或组合:

`$ kill $(lsof -t -i:8081)`

停止地铁捆绑。

`$ rm -rf ios/build/`
`$ watchman watch-del-all`
`$ rm -rf node_modules/`
`$ npm start -- --reset-cache`
`$ npm install`

* *提示# 7: ~~修复~~ ""blurTextInput" ~~已弃用~~":在 v0.58 中未弃用

`import { Keyboard } from 'react-native';`

`<TextInput
...
onBlur={Keyboard.dismiss()}
/>`

更正:似乎“blurTextInput”在 v0.58 中已被弃用，然后又被取消。在 React Native 的 repo 上阅读有关它的更多信息[。](https://github.com/facebook/react-native/commit/476a831ea09c25d0bbcb97a536c5bef76a1c8273#diff-b48972356bc8dca4a00747d002fc3dd5)

**提示# 8:iPad 模拟器上不需要的黑色边框

在 XCode 的项目设置中，前往“通用”标签。然后转到“构建设置”选项卡。
在搜索栏中，键入“目标设备系列”。
选择“1，2”。
重建。