# ðç¨ Next.js (React)ãGraphQLãStrapi å Stripe å¶ä½ä¸ä¸ª Deliveroo åéðèåæ¸å(ç¬¬ 3/7 é¨å)

> åæï¼<https://dev.to/ryanaz/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----dishes-list-part-37-5eh>

[![Strapi Next.js tutorial](img/6ae09f2771166462103198b0d9c7ca44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bD-CV-5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iudm6at0xp8v1o7f1f86.png)

æ¬æç¨æ¯ç¨ Next.js (React)ãGraphQLãStrapi å Stripe å¶ä½ Deliveroo åéæç¨ç³»åçä¸é¨åã

**ç®å½**

*   [è®¾ç½®](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----setup-part-17-ked)(ç¬¬ 1 é¨å)
*   ð [é¤é¦](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----restaurants-list-part-27-10ce)(ç¬¬äºé¨å)
*   ð[èè´](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----dishes-list-part-37-5eh/)(ç¬¬ä¸é¨å)**-å½å**
*   ð[è®¤è¯](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----authentication-part-47-eho)(ç¬¬åé¨å)
*   ð [è´­ç©è½¦](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----shopping-cart-part-57-2h1e)(ç¬¬äºé¨å)
*   ðµ[è®¢è´­åç»è´¦](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----order-and-checkout-part-67-fph)(ç¬¬å­é¨å)
*   ð[å¥å±:é¨ç½²](https://dev.to/ryanrez/-cooking-a-deliveroo-clone-with-nextjs-react-graphql-strapi-and-stripe----bonus-deploy---part-77-1i8e)(ç¬¬ä¸é¨å)

*æ³¨:* *æºä»£ç * *å¨ GitHub ä¸æ**:[https://GitHub . com/strapi/strapi-examples/tree/master/nextjs-react-strapi-deliver oo-clone-tutorial *](https://github.com/strapi/strapi-examples/tree/master/nextjs-react-strapi-deliveroo-clone-tutorial*)ã**

## ðèè´æ¸å

æ­åæ¨ï¼æ¨æåæ¾ç¤ºäºé¤ååè¡¨ï¼è¿æ¯éè¦çç¬¬ä¸æ­¥ã

### å®ä¹åå®¹ç±»å

æ¯ä¸ªé¤é¦åºå®çèè´ä¹å¿é¡»å­å¨å¨æ°æ®åºä¸­ãæä»¥ï¼æä»¬ç°å¨éè¦ä¸ä¸ªæ°çåå®¹ç±»åï¼æ¾ç¶å½åä¸º`dish`ãæ¢ç¶æ¨å·²ç»ç¥éå¦ä½åå»ºå®ï¼æå°åªç»åºå®çå±æ§:

*   `name`åç±»å`String`ã
*   `description`åç±»å`Text`ã
*   `image`åç±»å`Media`ã
*   `price`åç±»å`Number`(æ´æ°)ã
*   `restaurant`å¸¦ç±»å`Relation`:è¿ä¸ªæ´å·ä½ä¸ç¹ãæä»¬å¨è¿éçç®çæ¯åè¯ Strapiï¼æ¯ä¸éèé½å¯ä»¥ä¸ä¸å®¶é¤é¦ç¸å³èãä¸ºæ­¤ï¼åå»ºä¸ä¸ªä¸å¯¹å¤å³ç³»ï¼å¦ä¸æç¤ºã

[![Strapi relation](img/1de74c9004d1401287e08243aa2de416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UvOrkH8g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/um51cjveo5xweophtze9.png)

ä»¥ä¸æ¯æç»ç»æ:

[![Dishes fields](img/3d9744ac7e75389510a5f8e1c31f1ac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T48_-kXp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ozxel8tagpyq9xfjc0a0.png)

### æ·»å ä¸äºæ¡ç®

ç¶åï¼ä»åå®¹ç®¡çå¨æ·»å ä¸äºè:[http://localhost:1337/admin/plugins/Content-Manager/dish](http://localhost:1337/admin/plugins/content-manager/dish)ãç¡®ä¿ä»ä»¬é½æä¸ä¸ªå½¢è±¡ï¼å¹¶é¾æ¥å°ä¸å®¶é¤é¦ã

### å±ç¤ºèè´

[![Dishes list](img/d8d2aeee6f68db00d54bda9526e180fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---EhRQbOG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u4hzy41jvhczwkmi5bte.gif)

æä»¬å°ä¸º`/restaurants`ä½¿ç¨ä¸ä¸ªæ°çè·¯ç±ï¼å®å°æ¥æ¶é¤é¦ç ID å¹¶è¿åè¯¥é¤é¦çèè´åè¡¨:

```
cd ..
cd ..
cd pages
touch restaurants.js 
```

Enter fullscreen mode Exit fullscreen mode

è·¯å¾:`/frontend/pages/restaurants.js`