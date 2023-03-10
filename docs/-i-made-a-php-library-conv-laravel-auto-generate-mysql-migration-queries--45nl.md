# ðæåäºä¸ä¸ª PHP åºâconv-æå¼å°â:èªå¨çæ MySQL è¿ç§»æ¥è¯¢ãð¤

> åæï¼<https://dev.to/howyi/-i-made-a-php-library-conv-laravel-auto-generate-mysql-migration-queries--45nl>

[![thumbnail](img/f1835743a60e0e9ab215bad2838f664f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RjgVuhNV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpm2d58qbtlmwmw14lgl.gif) 
[å¨å°ºå¯¸](https://thepracticaldev.s3.amazonaws.com/i/eh53otutrf9c0vtduxlx.gif)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [è±ªä¼](https://github.com/howyi)/[conv-æåç»´å°](https://github.com/howyi/conv-laravel)

### å¨ Laravel/Lumen ä¸èªå¨çæ MySQL è¿ç§»æ¥è¯¢

<article class="markdown-body entry-content container-lg" itemprop="text">

# conv-æåé¦å°

å¨ Laravel/Lumen ä¸èªå¨çæ MySQL è¿ç§»æ¥è¯¢

[![](img/e38ad1508862cbfe28572931c1aa1a43.png)](https://camo.githubusercontent.com/7a7bde35be28a4cea2a763ae0f45f63f0ad79734/68747470733a2f2f7265732e636c6f7564696e6172792e636f6d2f70726163746963616c6465762f696d6167652f66657463682f732d2d526a675675684e562d2d2f635f6c696d6974253243665f6175746f253243666c5f70726f6772657373697665253243715f3636253243775f3838302f68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f68706d326435387162746c6d776d7731346c676c2e676966)

## å®è£

1.  éè¦åº(Laravel/Lumen)

```
composer require "howyi/conv-laravel" --dev 
```

2.  åå¸éç½®(ä»é Laravel)

```
php artisan vendor:publish --provider="Howyi\ConvLaravel\ConvServiceProvider" 
```

3.  æ¨¡å¼åå§å(Laravel/Lumen)

```
php artisan conv:reflect 
```

å¨`database/schemas`çæåå»ºæ¥è¯¢

## ä½¿ç¨

1.  ç¼è¾åå»ºæ¥è¯¢(å¨æ¨¡å¼ç®å½ä¸­)
2.  `php artisan conv:generate`ä»å®éæ°æ®åºçæè¿ç§»-åå»ºæ¥è¯¢
3.  `php artisan migrate`è¦è¿ç§»

</article>

[View on GitHub](https://github.com/howyi/conv-laravel)

# ç¹å¾

ä»å®éçæ°æ®åº(\PDO)å DDLs(åå»ºæ¥è¯¢)çæ MySQL è¿ç§»æ¥è¯¢

# å¨æº

æåå¦äºç¼åè¿ç§»æ¥è¯¢...ð

## å®è£

1.  éè¦åº(Laravel/Lumen)

```
composer require "howyi/conv-laravel" --dev 
```

1.  åå¸éç½®(ä»é Laravel)

```
php artisan vendor:publish --provider="Howyi\ConvLaravel\ConvServiceProvider" 
```

1.  æ¨¡å¼åå§å(Laravel/Lumen)

```
php artisan conv:reflect 
```

å¨`database/schemas`çæåå»ºæ¥è¯¢

## ç¨æ³

1.  ç¼è¾åå»ºæ¥è¯¢(å¨æ¨¡å¼ç®å½ä¸­)
2.  `php artisan conv:generate`ä»å®éæ°æ®åºçæè¿ç§»-åå»ºæ¥è¯¢
3.  `php artisan migrate`è¦è¿ç§»

# ä¸¾ä¾

ç¤ºä¾å­å¨åºð

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [è±ªä¼](https://github.com/howyi)/[conv-æèç»´å°-ä¸¾ä¾](https://github.com/howyi/conv-laravel-example)

<article class="markdown-body entry-content container-lg" itemprop="text">

[conv-æåç»´å°](https://github.com/howyi/conv-laravel)çç¤ºä¾å­å¨åº

</article>

[View on GitHub](https://github.com/howyi/conv-laravel-example)