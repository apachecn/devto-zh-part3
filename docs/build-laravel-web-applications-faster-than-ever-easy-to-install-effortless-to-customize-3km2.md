# ð£æå»º Laravel Web åºç¨ç¨åºçéåº¦æ¯ä»¥å¾ä»»ä½æ¶åé½å¿«ï¼æäºå®è£ï¼è½»æ¾å®å¶ðã

> åæï¼<https://dev.to/getspooky/build-laravel-web-applications-faster-than-ever-easy-to-install-effortless-to-customize-3km2>

Laravel æ¯ç± Taylor Otwell æå»ºçä¸ä¸ªä»¤äººé¾ä»¥ç½®ä¿¡çæ¡æ¶ï¼å®ç»åäºå¼ºå¤§ç web å¼ååè½ãä¸°å¯çææ¡£åæ´»è·çç¤¾åºãå¨æ¬æç¨ä¸­ï¼æä»¬å°ä½¿ç¨ laravelDash æå»ºä¸ä¸ªå¸¦æç®¡çé¢æ¿ç fullstack åºç¨ç¨åºã

## ä»ä¹æ¯ Laravel Dashboardï¼

å¼åèå¾æãä¸ï¼æä¸æ¯å¨å¼ç©ç¬ââä»ä»¬åªååå»ºç³»ç»ï¼å¸®å©ä»ä»¬å¨æªæ¥é¿åæ´å¤çå·¥ä½ãå°¤å¶æ¯éå¤æ§çå·¥ä½ãèä¸ç°å¨æç¸å½æç«¯çä¾å­ââæä»¬ä¸åéè¦åä»£ç äºï¼å®æ¯ä¸ºæä»¬èäº§ççãLaravel Dashboard æ LaravelDash ä¸º Laravel åºç¨ç¨åºç CRUD(åå»ºãè¯»åãæ´æ°ãå é¤)æä½æä¾äºå¼ºå¤§çç¨æ·çé¢ãå®æä¾äºé¢å¤çåè½ï¼åæ¬å¾è¡¨ï¼é¢æ¿ç®¡çï¼è®¾ç½®ï¼æ¯ä»ç³»ç»åè¶çº§ç®åçæè§å³æå¾â¦

## ç¬¬ 1 åé:åå»º Laravel åºç¨ç¨åº

æä»¬åè®¾æ¨å·²ç»è½å¤è®¾ç½®æ¨çå¼åç¯å¢ãå®è£ Laravel é¡¹ç®æä¸¤ç§æ¹æ³

*   éè¿ Laravel å®è£ç¨åº
*   éè¿ Composer åå»º-é¡¹ç®

æå°ä½¿ç¨ via composer åå»ºæ°é¡¹ç®

```
composer create-project --prefer-dist laravel/laravel blog 
```

Enter fullscreen mode Exit fullscreen mode

Laravel æä¾äºä¸ç§å¿«éçæ¹æ³ï¼ä½¿ç¨ä¸ä¸ªç®åçå½ä»¤:
æ¥æ­å»ºè®¤è¯æéçææè·¯ç±åè§å¾

```
php artisan make:auth 
```

Enter fullscreen mode Exit fullscreen mode

## ç¬¬äºåé:éç½®

é¦åï¼ç¡®ä¿åå»ºä¸ä¸ªæ°çæ°æ®åºï¼å¹¶å°æ¨çæ°æ®åºå­è¯æ·»å å°æ¨çãç¯å¢æä»¶:

```
APP_URL=http://localhost
DB_HOST=localhost
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret 
```

Enter fullscreen mode Exit fullscreen mode

## ç¬¬ 3 åé:å®è£å¹¶éç½® laravelDash

LaravelDash è¶çº§å®¹æå®è£ãå¨åå»ºäºæ°ç Laravel åºç¨ç¨åºä¹åï¼æ¨å¯ä»¥ä½¿ç¨ä¸é¢çå½ä»¤åå« laravelDash å:

```
composer require yal/laraveldash 
```

Enter fullscreen mode Exit fullscreen mode

å¦ææ¨ä½¿ç¨ Laravel >=5.5ï¼LaravelDash å°èªå¨æ³¨åå¶æå¡æä¾åãå¦ææ¨å° LaravelDash ä¸ Laravel 5.3 æ 5.4 ä¸èµ·ä½¿ç¨ï¼è¯·å¨åºç¨ç¨åºç config/app.php æä»¶ä¸­æ·»å  LaravelDash çæå¡æä¾è:

```
/*
 * Laravel dashboard Service Provider
 */
 \Yasser\LaravelDashboard\DashboardServiceProvider::class, 
```

Enter fullscreen mode Exit fullscreen mode

æ¥ä¸æ¥ï¼æ¨éè¦åå¸ laravelDash éç½®æä»¶:

```
php artisan vendor:publish --provider="Yasser\LaravelDashboard\DashboardServiceProvider" --tag="config" 
```

Enter fullscreen mode Exit fullscreen mode

## ç¬¬ 4 åé:è¿è¡è¿ç§»å¹¶å®ä¹å³ç³»

çæçè¿ç§»æ¯å¸¸è§ç Laravel è¿ç§»ï¼ä½¿ç¨:
è¿è¡è¿ç§»

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

æ°æ®åºè¡¨éå¸¸æ¯ç¸äºå³èçãä¾å¦ï¼ä¸ç¯åå®¢æç« å¯è½æè®¸å¤è¯è®ºï¼æèä¸ä¸ªè®¢åå¯è½ä¸ä¸è®¢åçç¨æ·æå³ãéè¾©ä½¿ç®¡çåå¤çè¿äºå³ç³»åå¾å®¹æãæä»¥å» App\User.php æ·»å  UserRelation

```
<?php

namespace App;

use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Yasser\LaravelDashboard\Traits\UserRelation;

class User extends Authenticatable
{
    use Notifiable,UserRelation;

} 
```

Enter fullscreen mode Exit fullscreen mode

## ç¬¬ 5 åé:è¶ï¼ä½ å¨ LaravelDash ä¸ï¼

è¦æ¥çå®ï¼æ¨éè¦å¨æ¨çå¼åæºå¨ä¸å¯å¨ä¸ä¸ª web æå¡å¨ãæ¨å¯ä»¥éè¿è¿è¡ä»¥ä¸å½ä»¤æ¥å®ç°è¿ä¸ç¹:php artisan serveï¼ç¶åè½¬å°`http://localhost:8000/Dashboard`

æ è®ºæ¨æ¯å¨å¸®å©æä»¬ä¿®å¤éè¯¯ãæ¹è¿ææ¡£è¿æ¯ä¼ æ­æ¶æ¯ï¼æä»¬é½å¸ææ¨æä¸º`LaravelDash`ç¤¾åºçä¸åï¼ðªðåè§ CONTRIBUTING.mdï¼äºè§£æä»¬å¨å¯»æ¾ä»ä¹ä»¥åå¦ä½å¼å§çæ´å¤ä¿¡æ¯ã

GitHub:[https://github.com/getspooky/laravelDash](https://github.com/getspooky/laravelDash)