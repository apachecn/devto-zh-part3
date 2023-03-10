# ðä½ ä¸éè¦ passport . js-node . js è®¤è¯æåâï¸

> åæï¼<https://dev.to/santypk4/you-don-t-need-passport-js-guide-to-node-js-authentication-26ig>

**åè´´äº[softwareontheroad.com](https://softwareontheroad.com/nodejs-jwt-authentication-oauth)T3ã**

# ç®ä»

è½ç¶å Google FirebaseãAWS Cognito å Auth0 è¿æ ·çç¬¬ä¸æ¹è®¤è¯æå¡è¶æ¥è¶åæ¬¢è¿ï¼å¹¶ä¸å passport.js è¿æ ·çä¸ä½ååºè§£å³æ¹æ¡æ¯è¡ä¸æ åï¼ä½æä»¬ç»å¸¸çå°å¼åäººåä»æªçæ­£çè§£è®¤è¯æµç¨ä¸­æ¶åçææé¨åã

è¿ä¸ç³»åå³äº node.js èº«ä»½éªè¯çæç« æ¨å¨æ­å¼ JSON Web ä»¤ç(JWT)ãç¤¾äº¤ç»å½(OAuth2)ãç¨æ·æ¨¡æ(ç®¡çåå¯ä»¥ä½ä¸ºç¹å®ç¨æ·ç»å½ï¼æ éå¯ç )ãå¸¸è§å®å¨é·é±åæ»å»åªä»ç­æ¦å¿µçç¥ç§é¢çº±ã

æ­¤å¤ï¼è¿æä¸ä¸ª GitHub å­å¨åºï¼å¶ä¸­åå«å®æ´ç node.js è®¤è¯æµï¼æ¨å¯ä»¥å°å®ç¨ä½é¡¹ç®çåºç¡ã

# ç®å½

*   [è¦æ±âï¸](#requirements)
*   [å¦ä½æ³¨åð¥](#signup)
*   [å¦ä½è¿è¡ç­¾å°ð¥](#signin)
*   [JWT è§£ééð©âð«](#what-is-jwt)
*   [çæ jwtð­](#creating-jwt)
*   [å®å¨ç«¯ç¹âï¸](#secure-endpoints)
*   [ç¨æ·ååðµï¸](#user-impersonation)
*   [ç»è®ºð](#conclusion)
*   [èä¾ç¥è¯åºð¬](https://github.com/santiq/nodejs-auth)

# é¡¹ç®è¦æ±âï¸

è¯¥é¡¹ç®çè¦æ±æ¯:

*   ä¸ä¸ªæ°æ®åºï¼ç¨äºå­å¨ç¨æ·ççµå­é®ä»¶åå¯ç ï¼æè clientId å clientSecretï¼æèä»»æä¸å¯¹å¬é¥åç§é¥ã

*   ä¸ä¸ªå¼ºå¤§èææçå å¯ç®æ³æ¥å å¯å¯ç ã

å¨æ°åæ¬ææ¶ï¼æè®¤ä¸º Argon2 æ¯æå¥½çå å¯ç®æ³ï¼è¯·ä¸è¦ä½¿ç¨ç®åçå å¯ç®æ³ï¼å¦ SHA256ãSHA512 æ MD5ã

å³äº[éæ©å¯ç æ£åç®æ³](https://medium.com/@mpreziuso/password-hashing-pbkdf2-scrypt-bcrypt-and-argon2-e25aaf41598e)çæ´å¤ç»èï¼è¯·åèè¿ç¯ç²¾å½©çæç« 

## å¦ä½åå»ºæ³¨åð¥

åå»ºç¨æ·æ¶ï¼å¯ç å¿é¡»ç»è¿åå¸å¤çï¼å¹¶ä¸çµå­é®ä»¶åå¶ä»èªå®ä¹ä¿¡æ¯(ç¨æ·èµæãæ¶é´æ³ç­)ä¸èµ·å­å¨å¨æ°æ®åºä¸­

***æ³¨:éè¯»ä¸ä¸ç¯æç« [é²å¼¹ node.js é¡¹ç®æ¶æð¡ï¸](https://dev.to/ideal-nodejs-project-structure)***
å³äº node.js é¡¹ç®æ¶æ

```
import * as argon2 from 'argon2';

class AuthService {
  public async SignUp(email, password, name): Promise<any> {
    const salt = randomBytes(32);
    const passwordHashed = await argon2.hash(password, { salt });

    const userRecord = await UserModel.create({
      password: passwordHashed,
      email,
      salt: salt.toString('hex'), // notice the .toString('hex')
      name,
    });
    return {
      // MAKE SURE TO NEVER SEND BACK THE PASSWORD OR SALT!!!!
      user: {
        email: userRecord.email,
        name: userRecord.name,
      },
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

æ³¨æï¼æä»¬è¿ä¸ºå¯ç åå»ºäºä¸ä¸ª *salt* ãsalt æ¯ç¨ä½æ£åå½æ°çéå è¾å¥çéæºæ°æ®ï¼å¹¶ä¸ salt æ¯ä¸ºæ¯ä¸ªæ°ç¨æ·è®°å½éæºçæçã

ç¨æ·è®°å½å¦ä¸æç¤º:

[![User record - Database MongoDB](img/6f92fcd4b9d854efa2fc6b6a4aedac50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mfXY4sFy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tos2owsn2c79pmjnnvle.png)
*robo 3t for MongoDB*

## å¦ä½åå»ºç­¾å°ð¥

[![Sign-In Diagram](img/53ce053bb3bcb3169bb5775f4b279d88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KS-6my1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/motfoccrvvqixbirjww7.png)

å½ç¨æ·æ§è¡ç»å½æ¶ï¼ä¼åçä»¥ä¸æåµ:

*   å®¢æ·ç«¯åéä¸å¯¹*å¬é¥*åä¸ä¸ª*ç§é¥*ï¼éå¸¸æ¯ä¸å°çµå­é®ä»¶åä¸ä¸ªå¯ç 

*   æå¡å¨ä½¿ç¨çµå­é®ä»¶å¨æ°æ®åºä¸­æ¥æ¾ç¨æ·ã

*   å¦ææ°æ®åºä¸­å­å¨è¯¥ç¨æ·ï¼æå¡å¨ä¼å¯¹åéçå¯ç è¿è¡åå¸å¤çï¼å¹¶å°å¶ä¸å­å¨çåå¸å¯ç è¿è¡æ¯è¾

*   å¦æå¯ç ææï¼å®ä¼ååºä¸ä¸ª JSON Web ä»¤ç(æ JWT)

è¿æ¯ä¸´æ¶ç*å¯é¥*,å®¢æ·ç«¯å¿é¡»å¨æ¯ä¸ªè¯·æ±ä¸­å°å¶åéç»ç»è¿èº«ä»½éªè¯çç«¯ç¹

```
import * as argon2 from 'argon2';

class AuthService {
  public async Login(email, password): Promise<any> {
    const userRecord = await UserModel.findOne({ email });
    if (!userRecord) {
      throw new Error('User not found')
    } else {
      const correctPassword = await argon2.verify(userRecord.password, password);
      if (!correctPassword) {
        throw new Error('Incorrect password')
      }
    }

    return {
      user: {
        email: userRecord.email,
        name: userRecord.name,
      },
      token: this.generateJWT(userRecord),
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

ä½¿ç¨ argon2 åºæ§è¡å¯ç éªè¯ï¼ä»¥é²æ­¢âåºäºæ¶é´çæ»å»âï¼
ï¼è¿æå³çï¼å½æ»å»èè¯å¾æ ¹æ®[çå¯é ååæ´åç ´è§£å¯ç æ¶ï¼æå¡å¨éè¦å¤é¿æ¶é´æ¥ååº](https://en.wikipedia.org/wiki/Timing_attack)ã

å¨ä¸ä¸èä¸­ï¼æä»¬å°è®¨è®ºå¦ä½çæ JWT

# ä½æ¯ï¼JWT ç©¶ç«æ¯ä»ä¹ï¼ð©âð«

JSON Web ä»¤çæ JWT æ¯ä¸ä¸ªç¼ç ç JSON å¯¹è±¡ï¼ä»¥å­ç¬¦ä¸²æä»¤ççå½¢å¼å­å¨ã

ä½ å¯ä»¥æå®çä½æ¯é¥¼å¹²çæ¿ä»£åï¼å®æå ä¸ªä¼ç¹ã

ä»¤çç± 3 é¨åç»æï¼å¦ä¸æç¤º:

[![JSON Web Token example](img/0054565cca902c7491cba9ddeaaec2b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VKBSh6Sz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pe5cfpvjxeapwu1h3hks.png)

ä¸éè¦**ç§å¯**æ**ç­¾å**å°±å¯ä»¥å¨å®¢æ·ç«¯è§£ç  JWT çæ°æ®ã

è¿å¯¹äºä¼ è¾å¨ä»¤çåç¼ç çä¿¡æ¯æåæ°æ®å¾æç¨ï¼è¿äºä¿¡æ¯æåæ°æ®å°å¨åç«¯åºç¨ç¨åºä¸­ä½¿ç¨ï¼ä¾å¦ç¨æ·è§è²ãéç½®æä»¶ãä»¤çè¿æç­ã

[![JSON Web Token decoded example](img/0555701f22c63c5cf522fccaf7034ec3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_arMov3e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mtqu4byne393y9aw3l6z.png)

# å¦ä½å¨ node.js ä¸­çæ JWTð­

è®©æä»¬å®ç°å®æèº«ä»½éªè¯æå¡æéç generateToken å½æ°

éè¿ä½¿ç¨å¨ npmjs.com å¯ä»¥æ¾å°çåº`jsonwebtoken`ï¼æä»¬è½å¤çæä¸ä¸ª JWTã

```
import * as jwt from 'jsonwebtoken'
class AuthService {
  private generateToken(user) {

    const data =  {
      _id: user._id,
      name: user.name,
      email: user.email
    };
    const signature = 'MySuP3R_z3kr3t';
    const expiration = '6h';

    return jwt.sign({ data, }, signature, { expiresIn: expiration });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

è¿ééè¦çæ¯ç¼ç æ°æ®ï¼ä½ ä¸åºè¯¥åéå³äºç¨æ·çææä¿¡æ¯ã

ç­¾åæ¯ç¨äºçæ JWT çâç§å¯â,å¯¹äºä¿è¯ç­¾åçå®å¨éå¸¸éè¦ã

å¦æé­å°ç ´åï¼æ»å»èå¯ä»¥ä»£è¡¨ç¨æ·çæä»¤çï¼çªåä»ä»¬çä¼è¯ã

## ä¿æ¤ç«¯ç¹å¹¶éªè¯ JWT âï¸

ç°å¨éè¦åç«¯ä»£ç å°æ¯ä¸ªè¯·æ±ä¸­ç JWT åéå°å®å¨ç«¯ç¹ã

ä¸ä¸ªå¥½çåæ³æ¯å° JWT åå«å¨æ¥å¤´ä¸­ï¼éå¸¸æ¯æææ¥å¤´ã

[![Authorization Header](img/c58f9b513650e4b82353090d7006fbce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3IaDQVdb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/52ex1uzgjqkplez653bj.png)

ç°å¨å¨åç«¯ï¼å¿é¡»ä¸ºå¿«éè·¯çº¿åå»ºä¸ä¸ªä¸­é´ä»¶ã

*ä¸­é´ä»¶âis authâ*

```
import * as jwt from 'express-jwt';

// We are assuming that the JWT will come in the header Authorization but it could come in the req.body or in a query param, you have to decide what works best for you.
const getTokenFromHeader = (req) => {
  if (req.headers.authorization && req.headers.authorization.split('  ')[0] === 'Bearer') {
    return req.headers.authorization.split('  ')[1];
  }
}

export default jwt({
  secret: 'MySuP3R_z3kr3t', // Has to be the same that we used to sign the JWT

  userProperty: 'token', // this is where the next middleware can find the encoded data generated in services/auth:generateToken -> 'req.token'

  getToken: getTokenFromHeader, // A function to get the auth token from the request
}) 
```

Enter fullscreen mode Exit fullscreen mode

ä½¿ç¨ä¸­é´ä»¶ä»æ°æ®åºä¸­è·åå®æ´çå½åç¨æ·è®°å½å¹¶å°å¶éå å°è¯·æ±ä¸­æ¯éå¸¸æç¨çã

```
export default (req, res, next) => {
 const decodedTokenData = req.tokenData;
 const userRecord = await UserModel.findOne({ _id: decodedTokenData._id })

  req.currentUser = userRecord;

 if(!userRecord) {
   return res.status(401).end('User not found')
 } else {
   return next();
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

ç°å¨ï¼è·¯ç±å¯ä»¥è®¿é®æ­£å¨æ§è¡è¯·æ±çå½åç¨æ·ã

```
 import isAuth from '../middlewares/isAuth';
  import attachCurrentUser from '../middlewares/attachCurrentUser';
  import ItemsModel from '../models/items';

  export default (app) => {
    app.get('/inventory/personal-items', isAuth, attachCurrentUser, (req, res) => {
      const user = req.currentUser;

      const userItems = await ItemsModel.find({ owner: user._id });

      return res.json(userItems).status(200);
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

âåºå­/ä¸ªäººç©åâè·¯çº¿ç°å¨æ¯å®å¨çï¼æ¨éè¦ä¸ä¸ªææç JWT æ¥è®¿é®å®ï¼ä½æ¯å®ä¹å°ä½¿ç¨æ¥èª JWT çå½åç¨æ·å¨æ°æ®åºä¸­æ¥æ¾ç¸åºçç©åã

## ä¸ºä»ä¹ JWT æ¯å®å¨çï¼

è¯»å®è¿ç¯æç« åï¼ä½ å¯è½ä¼æä¸ä¸ªå¸¸è§çé®é¢:

***å¦æ JWT æ°æ®å¯ä»¥å¨å®¢æ·ç«¯è§£ç ï¼æ¯å¦å¯ä»¥éè¿æ¹åç¨æ· id æå¶ä»æ°æ®çæ¹å¼æçºµ JWTï¼*T3ã**

è½ç¶ä½ å¯ä»¥å¾å®¹æå°è§£ç  JWTï¼ä½å¦ææ²¡æ JWT ç­¾ç½²æ¶ä½¿ç¨çâç§å¯â,ä½ å°±ä¸è½ç¨æ°æ°æ®å¯¹å®è¿è¡ç¼ç ã

è¿æ¯æ°¸è¿ä¸è¦æ³é²ç§å¯çéè¦æ¹æ³ã

æä»¬çæå¡å¨æ­£å¨æ£æ¥ä¸­é´ä»¶`IsAuth`ä¸çç­¾åï¼åº`express-jwt`è´è´£è¿é¡¹å·¥ä½ã

ç°å¨æä»¬å·²ç»äºè§£äº JWT çå·¥ä½åçï¼è®©æä»¬ç»§ç»­äºè§£ä¸ä¸ªå¾é·çé«çº§åè½ã

## å¦ä½ååç¨æ·ðµï¸

ç¨æ·æ¨¡ææ¯ä¸ç§å¨ä¸ç¥éç¨æ·å¯ç çæåµä¸ä»¥ç¹å®ç¨æ·èº«ä»½ç»å½çææ¯ã

å¯¹äºè¶çº§ç®¡çåãå¼åäººåææ¯æäººåæ¥è¯´ï¼è¿æ¯ä¸ä¸ªéå¸¸æç¨çåè½ï¼å¯ä»¥è§£å³æè°è¯åªæå¨ä»çä¼è¯ä¸­æè½çå°çç¨æ·é®é¢ã

ä»£è¡¨ä»ä½¿ç¨åºç¨ç¨åºä¸éè¦ç¨æ·å¯ç ï¼åªéçæä¸ä¸ªå¸¦ææ­£ç¡®ç­¾ååæéç¨æ·åæ°æ®ç JWTã

è®©æä»¬åå»ºä¸ä¸ªç«¯ç¹ï¼è¯¥ç«¯ç¹å¯ä»¥çæä½ä¸ºç¹å®ç¨æ·ç»å½ç JWTï¼è¯¥ç«¯ç¹åªè½ç±è¶çº§ç®¡çåç¨æ·ä½¿ç¨

é¦åï¼æä»¬éè¦ä¸ºè¶çº§ç®¡çåç¨æ·å»ºç«ä¸ä¸ªæ´é«çè§è²ï¼æè®¸å¤æ¹æ³å¯ä»¥åå°è¿ä¸ç¹ï¼ä¸ä¸ªç®åçæ¹æ³å°±æ¯å¨æ°æ®åºä¸­çç¨æ·è®°å½ä¸æ·»å ä¸ä¸ªâè§è²âå±æ§ã

[![super admin role in user database record](img/4e5adf169efa150dc0c7a5f036ad192e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B-B9NpqU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ep22ozxpevfu9jbth8p.png)

å¶æ¬¡ï¼è®©æä»¬åå»ºä¸ä¸ªæ£æ¥ç¨æ·è§è²çæ°ä¸­é´ä»¶ã

```
export default (requiredRole) => {
  return (req, res, next) => {
    if(req.currentUser.role === requiredRole) {
      return next();
    } else {
      return res.status(401).send('Action not allowed');
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

è¯¥ä¸­é´ä»¶éè¦æ¾å¨`isAuth`å`attachCurrentUser`ä¸­é´ä»¶ä¹åã

ç¬¬ä¸ï¼ä¸ºç¨æ·çæè¦æ¨¡æç JWT çç«¯ç¹ã

```
 import isAuth from '../middlewares/isAuth';
  import attachCurrentUser from '../middlewares/attachCurrentUser';
  import roleRequired from '../middlwares/roleRequired';
  import UserModel from '../models/user';

  export default (app) => {
    app.post('/auth/signin-as-user', isAuth, attachCurrentUser, roleRequired('super-admin'), (req, res) => {
      const userEmail = req.body.email;

      const userRecord = await UserModel.findOne({ email });

      if(!userRecord) {
        return res.status(404).send('User not found');
      }

      return res.json({
        user: {
          email: userRecord.email,
          name: userRecord.name
        },
        jwt: this.generateToken(userRecord)
      })
      .status(200);
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

æä»¥ï¼è¿éæ²¡æä»ä¹é­æ³ï¼è¶çº§ç®¡çåç¥éæ³è¦ååçç¨æ·ççµå­é®ä»¶ï¼é»è¾ä¸ç»å½éå¸¸ç¸ä¼¼ï¼ä½æ¯æ²¡ææ£æ¥å¯ç çæ­£ç¡®æ§ã

è¿æ¯å ä¸ºä¸éè¦å¯ç ï¼ç«¯ç¹çå®å¨æ§æ¥èªè§è²æéçä¸­é´ä»¶ã

# ç»è®ºðï¸

è½ç¶ä¾é ç¬¬ä¸æ¹èº«ä»½éªè¯æå¡ååºå¯ä»¥èçå¼åæ¶é´ï¼ä½æ¯äºè§£èº«ä»½éªè¯èåçåºå±é»è¾ååçä¹æ¯å¿è¦çã

å¨æ¬æä¸­ï¼æä»¬æ¢è®¨äº JWT åè½ï¼ä¸ºä»ä¹éæ©ä¸ä¸ªå¥½çå å¯ç®æ³æ¥æ£åå¯ç å¾éè¦ï¼ä»¥åå¦ä½æ¨¡æç¨æ·ï¼å¦ææ¨ä½¿ç¨å passport.js è¿æ ·çåºï¼è¿å°±ä¸é£ä¹ç®åäº

å¨æ¬ç³»åçä¸ä¸é¨åä¸­ï¼æä»¬å°æ¢ç´¢ä¸åçéé¡¹ï¼éè¿ä½¿ç¨ OAuth2 åè®®åä¸ä¸ªæ´ç®åçæ¿ä»£æ¹æ¡ï¼å³å Firebase è¿æ ·çç¬¬ä¸æ¹èº«ä»½éªè¯æä¾èï¼ä¸ºæä»¬çå®¢æ·æä¾âç¤¾äº¤ç»å½âèº«ä»½éªè¯ã

### [åè§æ­¤å¤èä¾åºð¬](https://github.com/santiq/nodejs-auth)

### èµæº

*   [å­å¨å¯ç çæ¨èåå¸æ¯ä»ä¹:bcryptãscryptãArgon2ï¼](https://security.stackexchange.com/questions/193351/in-2018-what-is-the-recommended-hash-to-store-passwords-bcrypt-scrypt-argon2)

*   [å®æ¶æ»å»](https://en.wikipedia.org/wiki/Timing_attack)

# âå¿ï¼å¨ä½ èµ°ä¹åðâ

å¦æä½ åæ¬¢è¿ç¯æç« ï¼ææ¨èä½ è®¢éæçé®ä»¶åè¡¨ï¼è¿æ ·ä½ å°±ä¸ä¼éè¿å¦ä¸ç¯è¿æ ·çæç« äºãâ¬ï¸ â¬ï¸

[![Email List Form](img/2129e5bcf65cf65ee7005743e685578a.png)](https://softwareontheroad.us20.list-manage.com/subscribe/post?u=337d8675485234c707e63777d&id=14f1331817)

æä¸ä¼è¯å¾åç»ä½ ä»»ä½ä¸è¥¿ï¼æä¿è¯

è¿æä¸è¦éè¿æä¹åçå¸å­ï¼ç¸ä¿¡ä½ ä¼åæ¬¢ç:)

[![santypk4 image](img/3da502abc622980763f568f315d67060.png)](/santypk4) [## é²å¼¹ node.js é¡¹ç®æ¶æð¡ï¸

### Sam Apr 18 ' 1911 åéè¯»å

#node #javascript #webdev #tutorial](/santypk4/bulletproof-node-js-project-architecture-4epf)

éè¯»æå¯¹ä¸è½½éæå¤§çåç«¯æ¡æ¶çç ç©¶ï¼ç»æä¼è®©ä½ å¤§åä¸æï¼

[![santypk4 image](img/3da502abc622980763f568f315d67060.png)](/santypk4) [## ä¸è½½éæåååçåç«¯æ¡æ¶[2015-2019]

### Sam Jul 28 ' 195 åééè¯»

#javascript #angular #react #vue](/santypk4/top-10-front-end-frameworks-by-downloads-2015-2019-2427)