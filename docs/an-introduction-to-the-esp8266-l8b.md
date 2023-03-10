# ESP8266 简介

> 原文：<https://dev.to/bc7/an-introduction-to-the-esp8266-l8b>

这本来是写在我的博客上的，但我认为它可以帮助节省其他人的故障诊断时间，所以我也在这里分享它！

免责声明:这是在基于 Debian 的 Linux 系统上编写和测试的。对于 Mac、Windows 和其他类似 Unix 的机器，命令可能会有所不同。

## 工具箱

[esptool](https://github.com/espressif/esptool) -需要上传初始固件

[探索者](https://esp8266.ru/esplorer/#download) -上传源代码需要

[固件](https://nodemcu-build.com/) -不言自明，链接指向推荐的云固件构建器以方便使用。

之所以选择这些，是因为它们大部分是跨平台工作的。但是，可以在这里找到其他工具/选项[。](https://frightanic.com/iot/tools-ides-nodemcu/)

## 固件上传

假设您已经下载了带有任何附加模块的固件，运行下面的代码将其上传到您的微控制器:

```
sudo esptool/esptool.py write_flash -fm qio 0x00000 <nodemcu-firmware>.bin 
```

视您的版本而定，Esptool 会列举所有可用的序列埠，直到找到连接的装置为止。因此，在以后的版本中，理论上应该没有必要指定串行端口。

**注意**:我必须用 *sudo* 运行命令，以便访问我的串口(老实说，我讨厌这么做)。如果你的没有 sudo 也能工作，那就不用管了。

**注意注意**:这也是假设您在同一个项目根目录中有 esptool.py 存储库和固件。你的道路可能不同。

## 测试 ESP8266

现在，通过以下命令运行 ESPlorer:

```
sudo java -jar ESPlorer/ESPlorer.jar 
```

**注意**:虽然没有 sudo 它也能正常运行，但我发现我还是无法建立连接，除非我用 sudo 启动它。

启动后，将打开连接按钮旁边的波特率设置为 115200(如果还没有设置的话)(在进行了大量的故障排除和一个模糊的未解决的 github 问题后，这是我打开连接的方法)。确认后，连接您的设备并打开连接。

最后，将以下示例 lua 代码作为一个新文件复制并粘贴到 ESPlorer 中。用你自己的网络凭证替换*我的用户名*和*我的密码*。

```
local SSID = "mySSID"
local PASSWORD = "myPassword"
function startup()
    if file.open("init.lua") == nil then
        print("init.lua deleted or renamed")
    else
        print("Running")
        file.close("init.lua")
    end
end

-- Define WiFi station event callbacks 
wifi_connect_event = function(T) 
  print("Connection to AP("..T.SSID..") established!")
  print("Waiting for IP address...")
  if disconnect_ct ~= nil then disconnect_ct = nil end  
end

wifi_got_ip_event = function(T) 
  -- Note: Having an IP address does not mean there is internet access!
  -- Internet connectivity can be determined with net.dns.resolve(). 
  print("Wifi connection is ready! IP address is: "..T.IP)
  -- a simple http server
    srv=net.createServer(net.TCP) 
    srv:listen(80,function(conn) 
        conn:on("receive",function(conn,payload) 
            local response = {
            "HTTP/1.0 200 OK\r\nServer: NodeMCU on ESP8266\r\nContent-Type: text/html\r\n\r\n",
            "<html><body>Hello world, come here often?</body></html"
            }
            print(payload)

              -- sends and removes the first element from the 'response' table
            local function send(localSocket)
                if #response > 0 then
                    localSocket:send(table.remove(response, 1))
                else
                    localSocket:close()
                    response = nil
                end
            end

            -- triggers the send() function again once the first chunk of data was sent
            conn:on("sent", send)
            send(conn)

            -- conn:send(response)
        end) 
    end)
--   print("Startup will resume momentarily, you have 3 seconds to abort.")
--   print("Waiting...") 
  tmr.create():alarm(3000, tmr.ALARM_SINGLE, startup)
end

wifi_disconnect_event = function(T)
  if T.reason == wifi.eventmon.reason.ASSOC_LEAVE then 
    --the station has disassociated from a previously connected AP
    return 
  end
  -- total_tries: how many times the station will attempt to connect to the AP. Should consider AP reboot duration.
  local total_tries = 75
  print("\nWiFi connection to AP("..T.SSID..") has failed!")

  --There are many possible disconnect reasons, the following iterates through 
  --the list and returns the string corresponding to the disconnect reason.
  for key,val in pairs(wifi.eventmon.reason) do
    if val == T.reason then
      print("Disconnect reason: "..val.."("..key..")")
      break
    end
  end

  if disconnect_ct == nil then 
    disconnect_ct = 1 
  else
    disconnect_ct = disconnect_ct + 1 
  end
  if disconnect_ct < total_tries then  
    print("Retrying connection...(attempt "..(disconnect_ct+1).." of "..total_tries..")")
  else
    wifi.sta.disconnect()
    print("Aborting connection to AP!")
    disconnect_ct = nil  
  end
end

-- Register WiFi Station event callbacks
wifi.eventmon.register(wifi.eventmon.STA_CONNECTED, wifi_connect_event)
wifi.eventmon.register(wifi.eventmon.STA_GOT_IP, wifi_got_ip_event)
wifi.eventmon.register(wifi.eventmon.STA_DISCONNECTED, wifi_disconnect_event)

print("Connecting to WiFi access point...")
wifi.setmode(wifi.STATION)
wifi.sta.config({ssid=SSID, pwd=PASSWORD}) 
```

如果你不熟悉 lua，不要担心，我也不熟悉——它的大部分仍在试错中。但是经验是最好的老师，所以不要让它阻止你！简而言之，上面的代码设置了一个 wifi 站，当设备连接到指定的网络时，当它接收到 ip 地址时，以及当它由于任何原因断开连接时，都有我们自己的自定义事件回调。

一旦接收到 ip 地址，它就建立一个 http 服务器，监听端口 80 上的任何入站连接，并返回一个简单的消息

当我们最终点击*另存为初始化*按钮，然后点击*保存到 ESP* 按钮时，ESPlorer 将上传文件并尝试运行它(如果按钮变灰，您可能需要点击实际设备上的小重置按钮，因为这意味着连接尚未完全建立)。现在，一旦它连接并获得 ip 地址，就可以从同一网络上的设备访问 *[http://ipaddress](http://ipaddress)* ，查看我们是否可以成功连接到该设备。

[![success](img/4a2b6d829997a2c50a00d536e0116a67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ecMKc13A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bcvii.com/static/a13093b04879d993a4e7d5d0c4a53b49/56de2/success.png)

**成功！:)**

## 结论

我们做到了团队！现在我们可以使用代码，并将其作为您下一个项目的基线。例如，我用它来与 GPIO 引脚进行交互，并通过 wifi 控制我的机器人，因为我在试图将它连接到步进电机时烧坏了我的第一个微控制器，这可能是下一个让其他人免受挫折的指南。无论如何，可用固件模块的可能性几乎是无限的。你用你的在建造什么？

如果有任何问题需要澄清，请随时提问！模块[文档](https://nodemcu.readthedocs.io/en/master/)也绝对是一个有用的资源。:)