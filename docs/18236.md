# 如何在 Nuxt.js+Firestore 中安全地进行 SSR

> 原文：<https://dev.to/dala00/nuxtjsfirestoressr-231g>

在服务器上运行 Nuxt.js，使用 Firestore，而且想进行服务器端渲染时，可以考虑很多。 试着总结一下担心的地方和看起来应对比较好的地方。

## 与不进行 SSR 时的不同

在客户端上从 Firestore 获取数据而不进行 SSR 的情况下，无论是匿名认证，还是 SNS 帐户的认证，都可以根据是否已认证来通过安全规则保护数据，盗用他人的 Firebase 项目设置进行任意操作

但是，如果是在 asyncData 内进行的 SSR，则无法利用其客户端的凭据。 这样的话，不仅不能保护数据，甚至连设定了安全规则的数据都无法访问。 我会考虑这种时候该怎么办。

## 如何解决

### 云 Functionsの利用

这次考虑了利用 Cloud Functions 的方法。 具体怎么办呢，在 asyncData 内只从 functions 中提取数据。 例如以下这样的感觉。

```
 async asyncData({ params }) {
    const response = await axios.get(functionUrl)
    return response.data
  } 
```

Enter fullscreen mode Exit fullscreen mode

不管呼叫方法如何，都会发生通信，所以如上所述，请求设为 1 次比较好吧。 在 functions 端制作该页面专用的动作并返回数据。

```
export const showUser = functions.https.onRequest((req, res) => {
  return cors(req, res, async () => {
    const user = await User.getUserByUsername(req.query.id)
    res.json({
      user,
      posts: await Post.getPosts(user.uid, 5)
    })
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 数据的获取方法

但是，这样的话，和刚才的 asyncData 一样，由于安全规则的关系不能获取数据吧？ 可能会被认为是。 但是，Firebase 有一个用于服务器的 Firebase Admin SDK。 利用它，因为它可以与安全规则无关地处理数据。 具体形式如下。

```
import * as admin from 'firebase-admin'

const firestore = admin.firestore()
const usersCollection = firestore.collection('users')

export async function getUser(uid: string) {
  const querySnapshot = await usersCollection
    .where('uid', '==', uid)
    .get()
    .catch(error => {
      console.error(error)
      return null
    })

  if (!querySnapshot || querySnapshot.size == 0) {
    return null
  }

  const user = querySnapshot.docs[0].data()
  user.id = querySnapshot.docs[0].id
  return user
} 
```

Enter fullscreen mode Exit fullscreen mode

只是 collection 的取法与客户端有微妙的不同，基本上是一样的。 这样，可以进行用于服务器侧渲染的数据获取处理。

## 可以无视安全规则吗？

关于这次例子中 functions 端的数据获取处理，可以无视规则。

那也就是说，原本 SSR 的目的，应该是为了让履带认识的 SEO 对策。 也就是说，不需要采取任何需要用户认证的数据。 说到底只是获取谁都可以浏览的公共数据。 因此，基本上在这里没有必要考虑关于认证的安全规则。 只是，cors 还是好好夹着吧。

## 总结

我试着做了一下，这和使用普通服务器的开发是一样的吧！ ？ 虽然感觉已经失去了简便性，但即便如此，不需要管理服务器也是一大优点，这一点没有改变吧。

虽然可能还有其他各种各样的方法，但总之不管是什么样的方法，如何保护数据都需要好好考虑并构筑。

因为以前也有关于认证和安全规则的考察，所以如果你愿意的话也请一定要看看。

[Firebase 的匿名认证是为了什么-安全篇](https://crieit.net/posts/Firebase)