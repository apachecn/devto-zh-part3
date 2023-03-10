# 在 Nuxt 中设置动态背景图像

> 原文：<https://dev.to/jaeyholic/setting-dynamic-background-images-in-nuxt-3j2>

几天前，我在 Nuxt 中设置动态背景图片时遇到了一些问题。我尝试了不同的方法把它拉出来，但是直到昨天我还是没能拉出来。顺便说一下，这是我第一次在项目中使用 Nuxt，我在我最近加入的新公司中建议这样做。既然是我建议的，我不妨成为我们网站上第一个使用它的人。是的，我开始使用这个框架，遇到了一些挑战，但在整个过程中，我找到了应对这些挑战的解决方案，是的，我说，这是一个很好的学习曲线，它也给了我利用这个框架探索更多的机会。回到我所面临的挑战，这个挑战是另一回事。我做了很多谷歌搜索，也在网上寻找解决方案，但都没有工作，直到我玩了一圈，找到了一个解决方案。我把我的解决方案分享给了一些可能有需要的人和社区，我发现这个社区很棒，我决定创建一个账户，并在这里分享。我不知道，但它也可能帮助某人，如果不是今天，可能是明天或改天。

[![Alt text of image](img/e26f950effb9943b6185ca7cc55bfe03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vPFkGEVg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vv6krdbdsftnfc9ab9pb.png)

因此，我首先创建一个 computed 属性，并在 computed 属性中创建 backgroundURL，然后返回循环的动态背景图像。为什么动态图像在元素或 div 上使用时有效的主要问题是，你应该*需要*图像，因为没有办法要求，这就是为什么我创建了一个计算属性，需要图像，然后在背景图像上调用属性。

[![Alt text of image](img/98c644477c6de6266c69a3eabb9ded29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OnCyffzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mtytcu8recakiklo4oxx.png)

我绑定了样式，因为它是一个动态的背景图像，在 URL 链接中，我放置了为背景图像创建的 computed 属性，并添加了一些样式来使图像看起来更好。

[![Alt text of image](img/90219a810103d3f28740a92482d94a1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FM4bjVq2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9sui83jyge22rm0dhzje.png)

这是 NUXT 中动态背景图片的最终结果

[![Alt text of image](img/c7f1a137048fb417fb7356033b229ab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LyVS59fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nrffvxa7c7zeggs3134i.png)

我使用了相同的过程，并用它为服务部分创建了一些好东西。

[![Alt text of image](img/f67220d428a77f48c6f72d85083d742f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAmeKs3D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5q828ol7xe5711w3k9tt.png)

我希望这能帮助到一些人，任何有问题或不明白的人都可以找我，随时在这里或 twitter 上联系我。