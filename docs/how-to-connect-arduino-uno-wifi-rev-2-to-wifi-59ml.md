# 如何将 Arduino UNO Wifi Rev.2 连接到 Wifi

> 原文：<https://dev.to/mikulskibartosz/how-to-connect-arduino-uno-wifi-rev-2-to-wifi-59ml>

# 安装

当您第一次将主板连接到 USB 端口时，IDE 应该会检测到它并安装所需的驱动程序。

安装驱动程序后，请确保选择正确的 Arduino 类型。

您应该在工具窗口中选择以下设置。

[![](img/e5eb74c4be89fb3bc58da479dc7060ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jAMHAZ3w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6nemyvi2mfqcaw85d9pv.png)

现在，您应该转到工具->管理库窗口并安装 WiFiNINA 库:

[![](img/00ef57852b87856f5d6c7c928ab27367.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5AyueZyB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5vo52y7lb2khupb4zl1v.png)

# 连接 WiFi

现在，您已经准备好从[https://www.arduino.cc/en/Tutorial/WiFiNINAWiFiPing](https://www.arduino.cc/en/Tutorial/WiFiNINAWiFiPing)复制示例代码了

在运行之前，点击右上角的箭头按钮，选择“新标签”选项。

我们需要创建一个 arduino_secrets.h 文件，其中包含以下代码:

```
#define SECRET_SSID "YOUR_NETWORK_SSID"
#define SECRET_PASS "YOUR_NETWORK_PASSWORD" 
```

保存文件并返回到包含从示例中复制的代码的文件。

确保您的 Arduino 已连接到 USB 端口，然后点按“上传”按钮。

# 测试

单击工具菜单并打开“串行监视器”窗口。您应该会看到这样的内容:

[![](img/7beeba637aeadafd9c6e63613d8cf335.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UMOci_4e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4va23f7p8ybssdp5op97.png)

如果看到类似的结果，说明一切正常。现在，您可以开始删除示例中多余的代码，并添加您的代码。

请确保保留这部分设置和 arduino_secrets.h 文件:

```
#include "arduino_secrets.h" 
char ssid[] = SECRET_SSID;
char pass[] = SECRET_PASS;
int status = WL_IDLE_STATUS;

void setup() {
  while ( status != WL_CONNECTED) {
    status = WiFi.begin(ssid, pass);
    delay(5000);
  }

# your code here
} 
```