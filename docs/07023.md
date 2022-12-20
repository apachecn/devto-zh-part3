# ESP8266+WiFi manager+ArduinoJSON =多 WiFi！

> 原文：<https://dev.to/acido_binario/esp8266-wifimanager-arduinojson-multi-wifi-403n>

这是这个博客上的第一篇文章，所以我想分享一些我找不到的，必须自己弄清楚的东西

因此..我在做一个物联网项目，需要两个可能的 WiFi 连接，但只有一个可由用户配置，以确保与后端服务的更可靠的通信。

处理用户可配置的 WiFi 凭据的最快速、最简单的方法是对 ESP8266 使用 WiFiManager 库，对 HTTP 请求使用 ESP8266HTTPClient 库。

[【wifi manager 代码】](https://github.com/tzapu/WiFiManager/tree/master/examples/OnDemandConfigPortal)

```
#include Arduino.h
#include <ESP8266WiFi.h>          //https://github.com/esp8266/Arduino 
//needed for library
#include <ESP8266WebServer.h>
#include <DNSServer.h>
#include <WiFiManager.h> //https://github.com/tzapu/WiFiManager 
#define TRIGGER_PIN 0 
void setup() {
  Serial.begin(115200);
  Serial.println("\n Starting");

  pinMode(TRIGGER_PIN, INPUT_PULLUP);
}

void loop(){
    if(digitalRead(TRIGGER_PIN) == LOW){
        WiFiManager wifiManager;
        if (!wifiManager.startConfigPortal("OnDemandAP")) {
          Serial.println("failed to connect and hit timeout");
          delay(3000);
          //reset and try again, or maybe put it to deep sleep
          ESP.reset();
          delay(5000);
        }
        Serial.println("connected...yeey :)");
    }
    //do other stuff...
} 
```

Enter fullscreen mode Exit fullscreen mode

但是..有问题！

我们如何管理多个 WiFi 连接？

我们可以修改 WiFiManager 库来与 wifiMulti 库交互，但是我并不喜欢修改现有的和正在工作的库。所以有一件事要做...

我们必须编写自己的代码😃(或者我自己的代码然后分享到这里，idk)
但是...怎么会？

使用 [SPIFFS](https://arduino-esp8266.readthedocs.io/en/latest/filesystem.html) 和 [Arduino_JSON](https://github.com/arduino-libraries/Arduino_JSON) 存储用户的 WiFi 配置！

> 这种方法还可以保存对代码有用的其他配置变量。

这两个库都有很好的文档，我推荐你去 RTFM 看看例子，但是我会尽力解释代码。

## JSON 结构

我们将从配置文件开始，在 SPIFFS 中编写一个文件`config.json`，它将包含一个具有不同 WiFi 凭证的数组对象。

```
{  "WiFi":  [  {  "ssid":  "wifi1",  "psk":  "pass1"  },  {  "ssid":  "wifi2",  "psk":  "pass2"  },  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

WiFi 对象数组有一个“硬编码”(`WiFi[0]`)凭证和另一个可由用户配置的(`WiFI[1]`)凭证

## 使用 Arduino_JSON 库

我们如何构建这样的结构？简单，跟着[例子](https://github.com/arduino-libraries/Arduino_JSON/tree/master/examples)就行了！

```
void createStartingJsonStruct(File settings) {
    // Declare the JSON object to use
    JSONVar root;    // main json root object
    JSONVar wifiArr; // the WiFi array
    JSONVar wifiObj; // the object inside the WiFi array

    wifiObj["ssid"] = ssid;    // "ssid": ssid
    wifiObj["psk"] = password; // "psk": psk
    wifiArr[0] = wifiObj;      // "WiFi": [{wifiObj}] 
    root["WiFi"] = wifiArr;    // { "WiFi": wifiArr, }
    root.printTo(settings);    // print the main object to the settings file!
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数接收一个设置文件(在写模式下！)然后继续创建 JSON 结构并将其写入设置文件。

我们如何使用这个功能？

```
 File settings = SPIFFS.open("/settings.json", "w"); //open the file in write mode
 WriteStartingConfigFile(settings); // write the starting config json
 settings.close(); // close the file 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一个功能来保存/更改自定义 wifi 凭据

```
//TODO: complete this!
void SaveCustomWifi(String ssid, String psk)
{
  File settingsRead = SPIFFS.open("/settings.json", "r"); //open file in write mode
  String jsonstr;                                         //store its contents...
  while (settingsRead.available())
  {
    jsonstr += char(settingsRead.read());
  }
  settingsRead.close(); //close it

  //JSON parse!!
  JSONVar myObject = JSON.parse(jsonstr);
  File settingsWrite = SPIFFS.open("/settings.json", "w"); //open file in read mode

  myObject["WiFi"][1]["ssid"] = ssid; // write ssid to index 1
  myObject["WiFi"][1]["psk"] = psk;   // write psk to index 1

  myObject.printTo(settingsWrite);    // write to settings 
  settingsWrite.close(); //close it
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用`SaveCustomWifi(ssid, psk);` :D 拯救用户 wifi 网络

一切都很好，但我们如何检索数据？

```
String readSsidAtIndex(int ind) // read "ssid" from the array at index ind
{
  File settings = SPIFFS.open("/settings.json", "r"); // open the file
  String jsonstr; // var to store the string from the file
  while (settings.available()) // while there's content to be read
  {
    jsonstr += char(settings.read()); // append to jsonstr
  }
  JSONVar myObject = JSON.parse(jsonstr); // parse/unmarshall the json string to myObject
  settings.close(); // close the settings file

  // read form the "WiFi": [{"ssid":..},{"ssid":...}] at index ind
  String tmp = JSON.stringify(myObject["WiFi"][ind]["ssid"]);

  // tmp is something like "myWiFiNetwork" , so we have to get rid of the quotes ""
  tmp = tmp.substring(1, tmp.length() -1);  
  return tmp; // return the SSID! 
} 
```

Enter fullscreen mode Exit fullscreen mode

密码密钥
也是如此

```
String readPskAtIndex(int ind) // read "psk" from the array at index ind
{
  File settings = SPIFFS.open("/settings.json", "r"); // open the file
  String jsonstr; // var to store the string from the file
  while (settings.available()) // while there's content to be read
  {
    jsonstr += char(settings.read()); // append to jsonstr
  }
  JSONVar myObject = JSON.parse(jsonstr); // parse/unmarshall the json string 
  settings.close(); // close the settings file

  // read form the "WiFi": [{"psk":..},{"psk":...}] at index ind
  String tmp = JSON.stringify(myObject["WiFi"][ind]["psk"]);

  // tmp is something like "myWiFiPass" , so we have to get rid of the quotes ""
  tmp = tmp.substring(1, tmp.length() -1);
  return tmp;
} 
```

Enter fullscreen mode Exit fullscreen mode

*都在评论里...*

因此，现在如果我们使用`readSsidAtIndex(0)`和`readPskAtIndex(0)`，我们将获得 WiFi 的“硬编码”凭证。

如果我们使用`readSsidAtIndex(1)`和`readPskAtIndex(1)`，我们将获得用户 wifi 凭证

现在有了这些函数，我们可以开始做一些事情了。

我们可以用这个 if 语句
检查 settings.json 文件是否存在

```
if (!SPIFFS.exists("/settings.json")) 
```

Enter fullscreen mode Exit fullscreen mode

如果它不存在，那么我们创建它:

```
if (!SPIFFS.exists("/settings.json")) //check if settings exists
  {
    File settings = SPIFFS.open("/settings.json", "w"); // open the file in write mode (this will create it)
    WriteStartingConfigFile(settings); // save the starting configuration
    settings.close(); //close the file
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们检查了 settings.json 文件的存在之后，我们可以读取该文件并将它的内容解析到一个 json 对象

```
 File settingsRead = SPIFFS.open("/settings.json", "r"); //open file in read mode
  String jsonstr;                                         //store its contents...
  while (settingsRead.available())
  {
    jsonstr += char(settingsRead.read());
  }
  settingsRead.close(); //close it

  //JSON parse!!
  JSONVar myObject = JSON.parse(jsonstr); 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以根据 wifi 阵列的长度来决定是检查互联网质量(发送请求)还是只使用硬编码的 WiFi 凭据。

```
if (myObject["WiFi"].length() == 1) //if there's only one wifi cred..
  {
    if (!(WiFi.SSID() == readSsidAtIndex(0) && WiFi.psk() == readPskAtIndex(0))) // check if the current wifi connection isn't the WiFi[0] (modem)
    {
      SaveCustomWifi(WiFi.SSID(), WiFi.psk()); // if it is not, then save the new wifi credential
      checkInternetQuality = true; //set the checkInternetQuality flag to true
    }else{
      checkInternetQuality = false; //we are connected to the only wifi avalable, we do not need to check the internet quality
    }
  }
  else // if we're connected to the WiFi[0]
  {
    // I dont remember what(why) this does exactly, but something like check if the current wifi connection is not WiFi[0] and is not WiFi[1] and also that is not empty
    if((WiFi.SSID() != readSsidAtIndex(1) && WiFi.psk() != readPskAtIndex(1)) && (WiFi.SSID() != readSsidAtIndex(0) && WiFi.psk() != readPskAtIndex(0)) && WiFi.SSID() != "" && WiFi.psk() != ""){
      SaveCustomWifi(WiFi.SSID(), WiFi.psk()); // save this new wifi connection
    }
    checkInternetQuality = true;
  }

  if(checkInternetQuality){
    WiFi.begin(readSsidAtIndex(1), readPskAtIndex(1)); //connect to the WiFi[1] if we're going to do a internet quality check 
  }else{
     WiFi.begin(readSsidAtIndex(0), readPskAtIndex(0)); // stay on WiFi[0] if we don't have any other wifi network to test
  } 
```

Enter fullscreen mode Exit fullscreen mode

既然我们有了告诉我们是否要检查互联网质量并保存添加的新网络的逻辑，那么我们就可以开始为这两个 wifi 网络实现互联网质量检查器了！

为此，我们可以使用两个布尔值`firstWiFiOk`和`secondWiFiOk`来存储 wifi 网络的最终质量。

我将通过连接到网络并每 30 秒向我的服务器报告设备来测试每个 wifi 网络的互联网质量，对于每个 HTTP 错误，增加一个错误计数器(`httpErrorCount`)，
。2 分钟后，切换到另一个 wifi 网络并进行同样的操作，如果第一个 WiFi 网络(WiFi[1])的错误少于 2 个，我可以说它是一个好网络并连接到它，如果它的错误超过 2 个， 我将查看我连接到的第二个 wifi 网络(WiFi[0])的错误是否少于 2 个，但是如果它的错误多于 2 个，那么我将重置 esp。

看起来应该是这样的:

```
unsigned long currentTime = 0; // to store the current millis 
unsigned long internetQualityMillis = 0; // to store the millis from 
unsigned long previousRequestMillis = 0;
int httpErrorCount = 0;

// booleans to know when the time of the test has completed
bool firstWiFiPassed = false;
bool secondWiFiPassed = false; 

// booleans to store the final network quality
bool firstWiFiOk = false;
bool secondWiFiOk = false;

bool checkInternetQuality = false;

void loop() {
  // put your main code here, to run repeatedly:

    currentTime = millis(); //get the millis!
    if(digitalRead(TRIGGER_PIN) == LOW){
        WiFiManager wifiManager;
        if (!wifiManager.startConfigPortal("OnDemandAP")) {
            delay(3000);
            //reset and try again, or maybe put it to deep sleep
            ESP.reset();
            delay(5000);
        } else {
            ESP.reset();
        }
    }

    if(checkInternetQuality){
        // start the InternetQuality Testing!

        if((currentTime - internetQualityMillis) > (120000 * 2)){
            // if >four minutes has passed
            checkInternetQuality = false; // we do not need to keep checking all of this
            if(!secondWiFiPassed){// IDK if this is really necessary, but whatever
                secondWiFiPassed = true;
                if(httpErrorCount > 2){
                    secondWiFiOk = false;
                } else {
                    secondWiFiOk = true;
                }
            }

            if(firstWiFiOk){
                WiFi.begin(readSsidAtIndex(1), readPskAtIndex(1));
            } else {
                if (secondWiFiOk){
                    WiFi.begin(readSsidAtIndex(0), readPskAtIndex(0));
                } else {
                    ESP.reset();
                }
            }
        } else if ((currentTime - internetQualityMillis) > 120000){
            // if >two minutes has passed
            if (!firstWiFiPassed){
                firstWiFiPassed = true; //do this only once
                WiFi.begin(readSsidAtIndex(0), readPskAtIndex(0)); // connect to the second wifi at index 1
                if(httpErrorCount > 0){
                    firstWiFiOk = false;
                }else{
                    firstWiFiOk = true;
                }

            }
        }
    }

    if(currentTime - previousRequestMillis > 30000){
        // do a request and check if it was successful
        bool err = sendRequest();
        if(err){
            httpErrorCount++;
        } else {
            httpErrorCount = 0;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 就是这样！🎉

你可以在 [GitHub](https://github.com/acidobinario/espMultiWifiExample) 上查看完整源代码

如果您有疑问或建议，请在 twitter 上给我写信！ [@acido_binario](https://twitter.com/acido_binario)

你也可以看看我的博客(只有这篇文章 tho :c) [acidobinario.me](https://blog.acidobinario.me/)

* * *