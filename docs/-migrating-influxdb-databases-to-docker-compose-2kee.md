# ð³å° InfluxDB æ°æ®åºè¿ç§»å° docker-compose

> åæï¼<https://dev.to/thibmaek/-migrating-influxdb-databases-to-docker-compose-2kee>

> **è¿ç¯åææååè¡¨å¨æç[åå®¢](https://thibmaek.com/post/migrating-influxdb-databases-to-docker-compose)T3 ä¸**

# é®é¢

ææä¸ä¸ªå¨æ°çè±ç¹å° NUCï¼ææç®ç¨å®ä½ä¸º Docker çæå¡å¨ãå å¨åï¼æå¨ Raspberry Pi Zero W ä¸è®¾ç½®äº InfluxDB æ¥æ¶éæçå®¶åº­å©çæ°æ®ï¼å¹¶ç¨ Telegraf çæ§æçæºå¨ã

æéè¯¯å°çæµ Chronograf ä¸­ä¼æä¸ä¸ªå¯¼å¥/å¯¼åºæ¨¡åï¼æç¨å®æ¥ç®¡çæç InfluxDB æ°æ®åºãä¸è¿ï¼è¿ä¸ªé®é¢ä¼¼ä¹æç¹å¤æãé®é¢æ¬èº«å¨äºè¿æ ·ä¸ä¸ªäºå®ï¼å³ influxd æå¡éè¦å¨æ°æ®åºæ¢å¤åçä¹ååæ­¢ï¼ä¸ºäºå¤å¶è¿ç§æåµï¼æ¨å¿é¡»å³é­ Docker å®¹å¨...ä¹ä½¿å¾éåè¡å¨åå¾æ¯«æ æä¹ã

è·éææ¾å°çæå¿«çè§£å³æ¹æ¡ï¼ä½¿ç¨ docker-compose åä¸ä¸ªä¸­é´å®¹å¨

## å¤ä»½

å¨è¿è¡ InfluxDB (Rpi Zero)çæ§ä¸»æºä¸ï¼åæ­¢æå¡å¹¶å¯¼åºæ°æ®åºãæè¿æ¨èçæ¹æ³æ¯éè¿å¯ç§»æ¤çæ¹æ³:

```
# Ignore this if the service is already running of course.
$ sudo systemctl start influxdb
$ influxd backup -portable -database telegraf ./influxdb-backup/telegraf
$ influxd backup -portable -database home_assistant ./influxdb-backup/home_assistant
$ sudo systemctl stop influxdb 
```

å¨å¤ä»½äºè¿ä¸¤ä¸ªæ°æ®åºä¹åï¼æç¨ scp å¤å¶äºè¿äºç®å½ï¼ç¶åå°å®ä»¬å¤å¶å NUCï¼å¨é£éæå°è¿è¡ Docker å®¹å¨

## æ¢å¤

ç°å¨ï¼æå·²ç»è¿è¡äº InfluxDB å®¹å¨æ¥æµè¯ä¸ Chronograf çè¿æ¥ãå ä¸ºæè®¤ä¸ºè¿ç§æ ¼å¼å¯è¯»æ§æ´å¥½ï¼æä»¥æä½¿ç¨ docker-compose æ¥æè½¬è¿äºå®¹å¨:

```
version: '2'

services:
  influxdb:
    image: influxdb:latest
    container_name: influxdb
    ports:
      - 8086:8086
    volumes:
      - /opt/appdata/influxdb:/var/lib/influxdb
    restart:
      always 
```

æ¢å¤çæ­¥éª¤åæ¬é¦åå³é­è¿ä¸ªå®¹å¨ï¼ç¶åå¯å¨ä¸ä¸ªåºå¼çå®¹å¨ï¼å°å¤ä»½ç®å½å¤å¶å°æ¬å°å®è£çå·/opt/appdataï¼è¯¥å·å°ä¿å­å¨ docker-compose åå»ºçå®¹å¨ä¸­ã

é¦åï¼ç¨`docker-compose down`(æ`docker stop influxdb`)åæ­¢æä»¬å½åçå®¹å¨

åå»ºæ å°æ°æ®å·åæä»¬çå¤ä»½ç®å½çä¸­é´å®¹å¨ï¼ç¶åè¿å¥å®¹å¨ç shell:

```
$ docker run --rm --detach -v /opt/appdata/influxdb:/var/lib/influxdb -v /home/thibmaek/influxdb-backup:/backups -p 8086 influxdb:latest
$ docker exec -it mystifying_kepler /bin/bash 
```

å¨ä¸­é´å®¹å¨ä¸­ï¼ä½¿ç¨æ å°çå¤ä»½æä»¶å¤¹æ¢å¤æä»¬çæ°æ®åºï¼ç¶åéåº:

```
$ influxd restore -portable -database telegraf /backups/telegraf
$ influxd restore -portable -database home_assistant /backups/home_assistant
$ exit 
```

ææ­»å¹¶ç§»é¤ä¸­é´å®¹å¨ãéå¯æä»¬ä¹åç docker-compose å®¹å¨ï¼dbs åºè¯¥è¢«æ¢å¤:

```
$ docker stop mystifying_kepler
$ docker-compose up -d 
```