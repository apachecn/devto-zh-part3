# Java 多线程程序没有结束或退出

> 原文：<https://dev.to/vikramas/java-mqtt-concurrent-connections-is-taking-time-43e2>

我正在创建一个 mqtt 虚拟设备模拟器，其中将建立并发会话，一旦所有设备的发布完成，所有设备都将断开连接。

下面是代码的 github 链接:

[https://github . com/vik ramas/mqtttestclient . git](https://github.com/VIKRAMAS/MqttTestClient.git)

在这里，我在完成子线程后使用 CountDownLatch 继续主线程，代码代码正确执行并断开所有 mqtt 连接。但是代码没有退出，它处于等待状态。

发布者:

```
package com.test.publisher;

import java.util.concurrent.CountDownLatch;

import org.eclipse.paho.client.mqttv3.MqttClient;
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;
import org.eclipse.paho.client.mqttv3.MqttException;
import org.eclipse.paho.client.mqttv3.MqttMessage;
import org.eclipse.paho.client.mqttv3.MqttPersistenceException;
import org.eclipse.paho.client.mqttv3.MqttSecurityException;
import org.eclipse.paho.client.mqttv3.MqttTopic;

public class Publisher extends Thread{

    public static final String test_topic = "test";
    private MqttClient client;
    public static final String BROKER_URL = "tcp://192.168.1.129:1883";
    CountDownLatch latch;

    public Publisher(CountDownLatch latch) {
        super();
        this.latch = latch;
    }

    public void Publisher() {
        String clientid=Thread.currentThread().getName();
        //We have to generate a unique Client id.
       // System.out.println("---------------------------->");
        System.out.println("=========== "+clientid);
        MqttConnectOptions options = null;
        try {

             client = new MqttClient(BROKER_URL, clientid);
             options = new MqttConnectOptions();
            options.setCleanSession(true);
            options.setMaxInflight(50);
           /// options.setWill(client.getTopic("home/LWT"), "I'm gone :(".getBytes(), 0, false);
          // options.setUserName("superadmin");
           //options.setPassword("DeviceDi0MQOteBfoUrIrZKrfxSg==".toCharArray());
            client.connect(options);
        } catch (MqttException e) {
            try {
                client.connect(options);
            } catch (MqttSecurityException e1) {
                // TODO Auto-generated catch block
                e1.printStackTrace();
            } catch (MqttException e1) {
                // TODO Auto-generated catch block
                e1.printStackTrace();
            }
            e.printStackTrace();
            //System.exit(1);
        }
    }

    @Override
    public void run() {
        // TODO Auto-generated method stub
        Publisher();
        System.out.println(Thread.currentThread().getName());
        try {
            for(int i=0;i<50;i++)
            {
            //Thread.sleep(20);
            publishTemperature();
            }
            latch.countDown();  

        } catch (MqttPersistenceException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (MqttException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } /*catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }*/  
    }
    public void publishTemperature() throws MqttPersistenceException, MqttException {
        final MqttTopic test = client.getTopic(test_topic);
         final String temperature="{ \"test\": { \"type\": \"Lighting\", \"controller_id\": \"S00\", \"Luminar_Id\": \"L00\", \"TimeStamp\": \"2019-03-04T10:45:06.786596+02:00\", \"nvoLampFb\": false, \"power\": 0.6000000238418579, \"Energy\": 10.899999618530273, \"Runtime\": 963, \"supplyVoltage\": 236.3000030517578, \"supplyCurrent\": 0.03999999910593033, \"cycleCount\": 519, \"LevelFB\": 0, \"powerFactor\": 0.05454999953508377, \"temperature\": 23.93000030517578, \"Lcstate\": \"OLC_OFF\" } }";
         test.publish(new MqttMessage(temperature.getBytes()));

         //System.out.println("Published data. Topic: " + "test" + "  Message: " + temperature);

    }

    public MqttClient getClient() {
        return client;
    }

    public void setClient(MqttClient client) {
        this.client = client;
    }
} 
```

用于创建线程的测试类:

```
package com.test.publisher;

import java.util.concurrent.CountDownLatch;

import org.eclipse.paho.client.mqttv3.MqttException;
import org.eclipse.paho.client.mqttv3.MqttPersistenceException;

import kafka.producer.Producer;

public class test {
    static Publisher[] Publisher=null;

    public static void main(String[] args) throws MqttPersistenceException, MqttException, InterruptedException {
        final CountDownLatch latch = new CountDownLatch(50);
        Publisher = new Publisher[500];
        for(int i=0;i<50;i++)
        {
            /*Thread.sleep(25);*/
            Publisher[i]=new Publisher(latch);
            Publisher[i].start();

        }
        latch.await();
        System.out.println("****************************************************************************************");

        for(int i=0;i<50;i++)
        {
            //System.out.println("=======>"+i);
            //Thread.sleep(10);
            //Publisher[i].getClient().close();
            Publisher[i].getClient().disconnect();
            Publisher[i].getClient().close();

        }
        System.out.println("*****************completed*******************");
    }

} 
```

这里 main 方法将等待(`latch.await()`)所有的线程完成数据发布。

[![enter image description here](img/e311458b9e706f4f82b9086143693c27.png)](https://i.stack.imgur.com/HdSfr.png)