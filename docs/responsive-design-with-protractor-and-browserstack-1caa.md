# 带有量角器和浏览器堆栈的响应式设计

> 原文：<https://dev.to/_maimart_/responsive-design-with-protractor-and-browserstack-1caa>

本文描述了我们如何使用量角器和 Browserstack 来创建一个良好的环境和开发过程，以便在开发过程中快速获得关于 web 应用程序响应性设计的反馈。

## 挑战

我们希望开发一个响应迅速的 web 应用程序。因此，它应该能够以用户友好的方式在不同的分辨率和不同的环境中使用。因此，我们希望确保应用程序能够在 Windows、OSX、IOS 和 Android 下的不同浏览器中正确显示和操作。

当在桌面上查看应用程序时，我们区分最大化窗口和缩小窗口，这可能需要不同的布局。在移动设备上，我们区分纵向和横向模式。

## 接近

在开发过程中，我们希望在不同的环境中尽可能快速有效地获得关于 web 应用程序表示的反馈。为此，我们希望创建一个环境，在这个环境中，我们可以随时从不同操作系统的不同浏览器中验证本地托管的应用程序的当前状态。

### 环境

#### 自有设备

我们现在可以设计我们的环境，例如在 Mac 上，并从虚拟或真实的 Windows 系统处理托管在那里的应用程序。然而，除此之外，我们还需要网络中的移动 Android 和 IOS 设备来测试我们的应用。

然而，这种解决方案需要我们在硬件上投入大量资金。这种硬件将永远伴随着我们，为了操作我们公司网络中的所有设备，我们可能不得不跨越很大的障碍。

如果我们与多个开发人员并行或远程工作，这种解决方案也是没有吸引力的。

#### 浏览器堆栈

[Browserstack](https://www.browserstack.com/) 提供了一种从各种浏览器、操作系统和设备访问网页的虚拟方式。这可用于手动或自动测试 web 应用程序。

对我们来说，关键特性是我们还可以从 Browserstack 实例访问我们的本地环境。

根据需要，许可费用并不便宜，但仍明显低于硬件采购和配置费用。它还允许我们高效灵活地开发。

### 发展过程

首先，我们用我们最喜欢的本地浏览器之一来开发和优化我们的 web 应用程序。我们考虑最大分辨率和小分辨率的表示。后者可能需要通过 CSS MediaQueries 等进行调整。

接下来，我们希望获得应用程序在各种环境下的反馈。例如，如果我们最初针对 Windows Chrome 进行开发，我们想知道该应用程序在 OSX Safari、IE11、Android Chrome 和 iPhone Safari 中是如何最大化和缩小显示的。

为此，我们通过量角器测试将应用程序置于状态中，我们希望验证其表现形式。随后，在测试中创建一个屏幕截图，并存储在本地。

这些半自动测试通过使用各自的量角器配置在 Browserstack 上运行。结果是最大化窗口和定义大小的较小窗口的每个桌面浏览器的屏幕截图。对于移动设备，屏幕截图以纵向和横向模式拍摄。

这给了我们关于这些非常不同的环境的自动和即时的反馈。然后，可以通过 Browserstack Live 手动分析和修复演示中的问题。像往常一样，在 Browserstack 实例中，我们得到了浏览器的独立开发工具的支持。

当应用程序在所有截图中正确显示时，我们最终必须手动验证剩余的环境。我们也可以总是制作所有变体的截图，但是这会相应地扩展截图的捕获和查看。

如果 app 在最多样化的环境下都能整齐的显示，我的经验是其余环境很少会有任何问题。

**注意截图的持续时间:**

根据许可证的不同，可以在不同的实例中并行测试不同的环境，也可以在单独的环境中并行执行测试。因此，屏幕截图的持续时间很大程度上取决于许可证包含的并行测试执行的数量。以我的经验，每个浏览器截图大概需要一分钟。

## 配置

### 浏览器堆栈

首先，我们在 [Browserstack](https://www.browserstack.com/) 页面上创建一个帐户，它拥有一个允许您运行自动化测试的许可证。

登录后，我们在我们的设置中找到我们的用户名和相关的密钥。

我们不想在我们的配置中将这些信息写成纯文本，所以我们将它们作为 **BS_USERNAME** 和 **BS_KEY** 放在操作系统的环境变量中。

为了让 Browserstack 访问我们的本地实例，我们需要 NPM 包“browserstack-local”。我们按照 [GitHub](https://github.com/browserstack/browserstack-local-nodejs) 页面上的描述安装它。

### 截图

为了捕捉和保存截图，我写了一个小的助手类。你可以从我的 [GitHub Gist](https://gist.github.com/maimArt/9dfaf1844dba43514b6ef1757e6549c4) 里面抄。

### 量角器配置

#### 依赖关系

在我们的量角器配置中，我们需要**浏览器堆栈本地**和**截屏。**

```
const { Screenshoter } = require('./screenshoter');
const browserstack = require('browserstack-local'); 
```

#### 能力

我所提到的环境在量角器和浏览器堆栈的上下文中被称为能力。每项功能都定义了一个环境，可以在 [Browserstack](https://www.browserstack.com/automate/capabilities) 网站的支持下创建。

有些属性的能力是相同的。我们对它们进行全局定义，然后将它们注入到单独的功能中。

```
const commonCapabilityProperties = {
  'browserstack.user': process.env.BS_USERNAME,
  'browserstack.key': process.env.BS_KEY,
  'browserstack.selenium_version': '3.13.0',
  'browserstack.local': 'true',
  'browserstack.video': 'true',
  'browserstack.debug': 'false',
  'browserstack.timezone': 'UTC',
}; 
```

**BS_USERNAME** 和 **BS_KEY** 是我们的浏览器栈用户名和密钥，我们之前已经定义为操作系统的环境变量。我们在运行时从流程中传递它们。

现在我们定义我们的能力。这可能是这样的，例如:

```
multiCapabilities: [
    {
      logName: 'OSX_Safari_1920',
      ...commonCapabilityProperties,
      os: 'OS X',
      os_version: 'High Sierra',
      browserName: 'Safari',
      browser_version: '11.1',
      resolution: '1920x1080',
      'browserstack.safari.driver': '2.48'
    },
    {
      logName: 'Win10_FF_720',
      ...commonCapabilityProperties,
      os: 'Windows',
      os_version: '10',
      browserName: 'Firefox',
      browser_version: '64.0',
      resolution: '1920x1080',
      customWindowSize: {
        width: 600,
        height: 800
      },
      'browserstack.geckodriver': '0.22.0',
    },
    {
      logName: 'GalaxyS9_Chrome_landscape',
      ...commonCapabilityProperties,
      os_version: '8.0',
      device: 'Samsung Galaxy S9',
      browserName: 'Chrome',
      browser_version: '71.0',
      real_mobile: 'true',
      deviceOrientation: 'landscape',
      'browserstack.appium_version': '1.9.1'
    },
    {
      logName: 'IPhoneX_Safari_portrait',
      ...commonCapabilityProperties ,
      device: 'iPhone X',
      browserName: 'Safari',
      real_mobile: 'true',
      'browserstack.appium_version': '1.9.1'
    }
} 
```

**" custom windowsize "**不是标准属性。如果我们不想在一个最大化的浏览器窗口中运行测试，而是在一个定义大小的窗口中运行，我们就使用它。更多关于这个的信息。

#### 设置

我们现在必须做一些更小的设置。

硒:

```
 seleniumAddress: 'http://hub-cloud.browserstack.com/wd/hub', 
```

使用的 Browserstack 实例的最大数量:

```
 maxSessions: 2, 
```

通常 Browserstack 实例可以通过 localhost 访问本地托管的实例。不幸的是，IOS 设备上的这种重定向存在问题。该问题已通过“bs-local.com”重定向到本地实例得到解决。

此外，可以使用的端口是有限的。信息可以在浏览栈[常见问题](https://www.browserstack.com/question/664)中找到。

在我们的例子中，应用程序托管在本地端口 5000 上。

```
 baseUrl: 'http://bs-local.com:5000/', 
```

#### 进入截屏

为了能够在测试中访问**屏幕截图**，我们通过一个函数将它作为量角器参数提供。

为此，我们创建一个全局地图并定义量角器参数:

```
const screenshoters = {};
exports.config = {
  //...
  params: {
    getScreenshoter: () => screenshoters[this.config.params.logName],
    logName: ''
  },
  //...
} 
```

#### 连接到浏览器堆栈

当启动量角器时，我们连接到浏览器堆栈并传播我们的会话。在出现错误的情况下，应该中止启动。

```
 beforeLaunch() {
    return new Promise((resolve, reject) => {
      console.log('Connecting local');
      exports.bs_local = new browserstack.Local();
      exports.bs_local.start({ key: commonCapabilityProperties['browserstack.key'] }, 
        function(error) {
        if (error) return reject(error);
        console.log('Connected to browserstack');
        resolve();
      });
    });
  }, 
```

在我们完成量角器之前，我们适当地关闭连接。

```
 afterLaunch() {
    return new Promise(resolve => exports.bs_local.stop(resolve));
  } 
```

#### 在环境入门之前…

对于每个单独的环境，应该实例化一个**屏幕截图**，并且应该最大化或调整窗口。

```
async onPrepare() {
   //...
   return browser.getProcessedConfig().then(config => {
      exports.config.params.logName = config.capabilities.logName;
      screenshoters[config.capabilities.logName] = new 
         Screenshoter(config.capabilities.logName);
      return resizeWindow(config);
    });
  },

const resizeWindow = async function(config) {
  const isDesktop = config.capabilities.os === 'Windows' || config.capabilities.os === 'OS X';
  const customWindowSize = config.capabilities.customWindowSize ? 
     config.capabilities.customWindowSize : null;
  if (isDesktop === true) {
    if (customWindowSize !== null) {
      await browser.driver
        .manage()
        .window()
        .setSize(customWindowSize.width, customWindowSize.height);
      console.log('Resized window to ' + customWindowSize.width + 'x' + 
         customWindowSize.height);
    } else {
      await browser.driver
        .manage()
        .window()
        .maximize();
      console.log('Maximized window');
    }
    browser.switchTo();
  }
}; 
```

#### 执行完一个环境的所有测试后…

执行完所有测试后，捕获的屏幕截图会保存在本地。

```
 async onComplete() {
    return new Promise(resolve => {
      const screenshotDir = `${__dirname}/screenshots/`;
      exports.config.params.getScreenshoter().saveScreenshots(screenshotDir);
      resolve();
    });
  }, 
```

### 半自动测试的规范

我们必须编写一个测试规范，将应用程序置于所需的状态，并最终捕获屏幕截图。

一个制作截图的规范可能是这样的。

```
describe('SomeResponsiveComponent', () => {
  const page = new Page();

  it('should display a responsive dialog', async () => {
    await page.open();
    await page.openResponsiveDialog();

    await browser.params.getScreenshoter().takeScreenshot('ResponsiveDialog');
  });
}); 
```

截图保存在项目的子文件夹“截图/响应对话框”中。png”。

## 结论

在开发多种浏览器和操作系统的过程中，浏览器堆栈可以极大地方便和节省成本。

通过创建截图，可以节省工作时间，并立即生成反馈，而无需在各种浏览器中手动、单调地一次又一次将应用程序更改为定义的状态。

带有量角器和浏览器堆栈的响应设计这个帖子最早出现在 [maimArt 制作的软件](http://maimart.de)上。