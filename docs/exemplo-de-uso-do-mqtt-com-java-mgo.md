# Exemplo de uso do MQTT

> 原文：<https://dev.to/pcollares/exemplo-de-uso-do-mqtt-com-java-mgo>

MQTT(消息排队远程试验传输)是一种轻量级、易于使用的消息协议，已被广泛用于开发物联网(IoT)解决方案，但由于其中介功能非常强大，它还可以处理较重的应用程序。

MQTT 是一种标准*【发布/订阅】*，其中服务器(中介)接收消息并向感兴趣的人分发。这是通过主题实现的，其中每条已发布的消息都有，而且有兴趣的客户也可以注册。

<figure>[![](img/99dadb336859b79d989e27cf659a48d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zjFzF0Un--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.paulocollares.com.br/wp-content/uploads/2019/04/mqtt-1024x768.jpg) 

<figcaption>描述 MQTT</figcaption>

</figure>

运行情况的图

## 依赖关系

该项目只有一个依赖项，即 Eclipse 开发的 MQTT 的 Paho 客户端，可在此下载:https://www . eclipse . org/PAHO/clients/Java/。

## 类

下面是执行项目所需的三个类:Main(包含项目的主类)，它启动客户端和侦听器，并发布测试消息 client Qt(包含连接、订阅和发布方法)，以及 listener(订阅主题并在终端上显示消息)。

### Main.java

```
package javamqtt;

import java.text.SimpleDateFormat;

public class Main {

    public static void main(String[] args) throws InterruptedException {
        ClienteMQTT clienteMQTT = new ClienteMQTT("tcp://broker.mqttdashboard.com:1883", null, null);
        clienteMQTT.iniciar();

        new Ouvinte(clienteMQTT, "br/com/paulocollares/#", 0);

        while (true) {
            Thread.sleep(1000);
            String mensagem = "Mensagem enviada em " + new SimpleDateFormat("dd/MM/yyyy HH:mm:ss").format(System.currentTimeMillis()) + " - Teste de MQTT disponivel em www.paulocollares.com.br";

            clienteMQTT.publicar("br/com/paulocollares/teste", mensagem.getBytes(), 0);
        }
    }
} 
```

### ClienteMQTT.java

```
package javamqtt;

import java.util.Arrays;
import org.eclipse.paho.client.mqttv3.IMqttDeliveryToken;
import org.eclipse.paho.client.mqttv3.IMqttMessageListener;
import org.eclipse.paho.client.mqttv3.IMqttToken;
import org.eclipse.paho.client.mqttv3.MqttCallbackExtended;
import org.eclipse.paho.client.mqttv3.MqttClient;
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;
import org.eclipse.paho.client.mqttv3.MqttException;
import org.eclipse.paho.client.mqttv3.MqttMessage;
import org.eclipse.paho.client.mqttv3.persist.MqttDefaultFilePersistence;

public class ClienteMQTT implements MqttCallbackExtended {

    private final String serverURI;
    private MqttClient client;
    private final MqttConnectOptions mqttOptions;

    public ClienteMQTT(String serverURI, String usuario, String senha) {
        this.serverURI = serverURI;

        mqttOptions = new MqttConnectOptions();
        mqttOptions.setMaxInflight(200);
        mqttOptions.setConnectionTimeout(3);
        mqttOptions.setKeepAliveInterval(10);
        mqttOptions.setAutomaticReconnect(true);
        mqttOptions.setCleanSession(false);

        if (usuario != null && senha != null) {
            mqttOptions.setUserName(usuario);
            mqttOptions.setPassword(senha.toCharArray());
        }
    }

    public IMqttToken subscribe(int qos, IMqttMessageListener gestorMensagemMQTT, String... topicos) {
        if (client == null || topicos.length == 0) {
            return null;
        }
        int tamanho = topicos.length;
        int[] qoss = new int[tamanho];
        IMqttMessageListener[] listners = new IMqttMessageListener[tamanho];

        for (int i = 0; i < tamanho; i++) {
            qoss[i] = qos;
            listners[i] = gestorMensagemMQTT;
        }
        try {
            return client.subscribeWithResponse(topicos, qoss, listners);
        } catch (MqttException ex) {
            System.out.println(String.format("Erro ao se inscrever nos tópicos %s - %s", Arrays.asList(topicos), ex));
            return null;
        }
    }

    public void unsubscribe(String... topicos) {
        if (client == null || !client.isConnected() || topicos.length == 0) {
            return;
        }
        try {
            client.unsubscribe(topicos);
        } catch (MqttException ex) {
            System.out.println(String.format("Erro ao se desinscrever no tópico %s - %s", Arrays.asList(topicos), ex));
        }
    }

    public void iniciar() {
        try {
            System.out.println("Conectando no broker MQTT em " + serverURI);
            client = new MqttClient(serverURI, String.format("cliente_java_%d", System.currentTimeMillis()), new MqttDefaultFilePersistence(System.getProperty("java.io.tmpdir")));
            client.setCallback(this);
            client.connect(mqttOptions);
        } catch (MqttException ex) {
            System.out.println("Erro ao se conectar ao broker mqtt " + serverURI + " - " + ex);
        }
    }

    public void finalizar() {
        if (client == null || !client.isConnected()) {
            return;
        }
        try {
            client.disconnect();
            client.close();
        } catch (MqttException ex) {
            System.out.println("Erro ao desconectar do broker mqtt - " + ex);
        }
    }

    public void publicar(String topic, byte[] payload, int qos) {
        publicar(topic, payload, qos, false);
    }

    public synchronized void publicar(String topic, byte[] payload, int qos, boolean retained) {
        try {
            if (client.isConnected()) {
                client.publish(topic, payload, qos, retained);
                System.out.println(String.format("Tópico %s publicado. %dB", topic, payload.length));
            } else {
                System.out.println("Cliente desconectado, não foi possível publicar o tópico " + topic);
            }
        } catch (MqttException ex) {
            System.out.println("Erro ao publicar " + topic + " - " + ex);
        }
    }

    @Override
    public void connectionLost(Throwable thrwbl) {
        System.out.println("Conexão com o broker perdida -" + thrwbl);
    }

    @Override
    public void connectComplete(boolean reconnect, String serverURI) {
        System.out.println("Cliente MQTT " + (reconnect ? "reconectado" : "conectado") + " com o broker " + serverURI);
    }

    @Override
    public void deliveryComplete(IMqttDeliveryToken imdt) {
    }

    @Override
    public void messageArrived(String topic, MqttMessage mm) throws Exception {
    }

} 
```

### 倾听者。java

```
package javamqtt;

import org.eclipse.paho.client.mqttv3.IMqttMessageListener;
import org.eclipse.paho.client.mqttv3.MqttMessage;

public class Ouvinte implements IMqttMessageListener {

    public Ouvinte(ClienteMQTT clienteMQTT, String topico, int qos) {
        clienteMQTT.subscribe(qos, this, topico);
    }

    @Override
    public void messageArrived(String topico, MqttMessage mm) throws Exception {
        System.out.println("Mensagem recebida:");
        System.out.println("\tTópico: " + topico);
        System.out.println("\tMensagem: " + new String(mm.getPayload()));
        System.out.println("");
    }

} 
```

## 免费公开 MQTT 经纪人进行测试

在此示例中，我使用公共中介进行测试，但应仅用于此目的，因为发布的内容没有隐私。为了在你的项目中使用，我推荐[蚊子](https://projects.eclipse.org/projects/technology.mosquitto)，也是日食。

### 下载项目

将此示例项目下载到我的 github:https://github . com/pcollas/示例-blog/tree/master/javamqtt

[]的