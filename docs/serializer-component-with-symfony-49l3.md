# 带 Symfony 的序列化程序组件

> 原文：<https://dev.to/_mertsimsek/serializer-component-with-symfony-49l3>

这篇文章导致在我们的项目中使用**序列化**。但是序列化是什么，谁需要它？我们将回答这些必要的问题。显然，他们正在帮助我们理解这一点。好吧，我们开始吧。

### 什么叫连载？

对于开发者来说；它把一个对象转换成一个格式化的字符串。我们会有文本，JSON，XML。另一方面，二进制格式。一般来说，我们一般开发 REST Apis。然而，不同的编程语言和不同的机器应该通过我们的 REST Apis 端点保持联系。在这一点上，序列化真正为我们工作。

### 谁需要序列化？

作为开发人员，我们可以这样参考。最初，我们在开发 REST Api 时发送或接收数据。它应该是管理所有通信的严格格式。最常见的用例是队列系统。我们用串行化来解决排队系统。其他事情可能是测试。我们能够察觉到一些变化。此外，如果我们想在数据库中存储数据，而不是有字段的列。所有数据只能有一个字段。如果我需要给出一些例子，他们可能会像 Redis，Elasticsearch。

### 它是如何工作的？

这是它的工作原理。

[![alt text](img/ba372217333d235f11ceca4a1811bcc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--of47r7CX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7jiy3jun1y7j659c2kqe.png)

- Normalizers -
首先有一个对象格式。规范化器显然是将对象转换成数组的类。数组结构很容易理解和操作。

-编码器-
这些类从数组转换成声明的格式。或者，声明格式化为数组。

好了，从现在开始我们可以开始写一些代码了。关于这一点，我将给出一些建议。为此，我安装了组件。

```
composer require symfony/serializer -vvv 
```

之后，我在`framework.yaml`文件中添加了我的配置行。

```
framework:
    serializer:
        enabled: true
        enable_annotations: true 
```

我已经调整了配置。现在，我写了我的第一个代码。

```
<?php

namespace App\Controller;

use App\Entity\User;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\Routing\Annotation\Route;
use Symfony\Component\Serializer\SerializerInterface;

class UserController extends AbstractController
{
    /**
     * @Route("/user", name="user")
     * @param SerializerInterface $serializer
     * @return JsonResponse
     */
    public function index(SerializerInterface $serializer)
    {

        $user = new User();
        $user->setFullname('Mert Simsek')
            ->setCity('İstanbul')
            ->setEmail('mertsmsk0@gmail.com')
            ->setPhone('+90-000-000-00-00')
            ->setCreatedAt(new \DateTime('2019-02-25 00:00:00'));

        $serializedUser = $serializer->serialize($user,'json');

        return JsonResponse::fromJsonString($serializedUser);

    }
} 
```

我实现了序列化程序接口，并转换了声明的格式。回应会是这样的。

[![alt text](img/791be5e944281810a8b990458f4cc57f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8wYnNyVJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/awsdd90l63du1ulyvei1.png)

此刻，我想强调一些特点。首先是`groups`。想象一下，你有所有这些柱子。全名、电话、城市等。但是你不想提供全部。在这一点上，“团体”帮助了我们。怎么会？像这样。你在实体的每个属性下面写上“组”。

```
<?php

namespace App\Entity;

use Doctrine\ORM\Mapping as ORM;
use Symfony\Component\Serializer\Annotation\Groups;

/**
 * @ORM\Entity(repositoryClass="App\Repository\UserRepository")
 */
class User
{
    /**
     * @ORM\Id()
     * @ORM\GeneratedValue()
     * @ORM\Column(type="integer")
     * @Groups({"phone","web"})
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     * @Groups({"phone","web"})
     */
    private $fullname;

    /**
     * @ORM\Column(type="string", length=255)
     * @Groups({"phone","web"})
     */
    private $city;

    /**
     * @ORM\Column(type="string", length=255)
     * @Groups({"phone"})
     */
    private $phone;

    /**
     * @ORM\Column(type="string", length=255)
     * @Groups({"phone","web"})
     */
    private $email;

    /**
     * @ORM\Column(type="datetime")
     * @Groups({"phone","web"})
     */
    private $createdAt;

    // at this point, there are getters and setters...

} 
```

现在，我们可以序列化这个“组”定义。如你所见，如果我用“web”组序列化，phone 属性就不会出现在我们面前。因为，电话属性只为“电话组”而出现。让我们看看。

```
$user = new User();
$user->setFullname('Mert Simsek')
    ->setCity('İstanbul')
    ->setEmail('mertsmsk0@gmail.com')
    ->setPhone('+90-000-000-00-00')
    ->setCreatedAt(new \DateTime('2019-02-25 00:00:00'));

$serializedUser = $serializer->serialize($user,'json', ['groups' => ['web']]);

return JsonResponse::fromJsonString($serializedUser); 
```

我的反应如何？给您

[![alt text](img/43129cb4f4fa86a3343c3c2438b11455.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mI_am0kv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mvece4exsnmtygcxe8fa.png)

如您所见，我们再也看不到电话属性了。现在让我们来看看其他格式。比如，xml？我把格式从 **json** 改成了 **xml** 。

```
$user = new User();
$user->setFullname('Mert Simsek')
    ->setCity('İstanbul')
    ->setEmail('mertsmsk0@gmail.com')
    ->setPhone('+90-000-000-00-00')
    ->setCreatedAt(new \DateTime('2019-02-25 00:00:00'));

$serializedUser = $serializer->serialize($user,'xml', ['groups' => ['web']]);

return JsonResponse::fromJsonString($serializedUser); 
```

立刻，我们确定结果。

[![alt text](img/095e47a43b0438667ed4b8816ec68974.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b8wWR6Gl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/799rn1szfa3u125b6923.png)

或 csv...当然，这一次我把 xml 转换成了 T2 的 csv。

[![alt text](img/17cf0fc45343e3a231e9f93eacaff65f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w66ueVqO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zn2g920kmobu64rehyf1.png)

我想提一下另一个特点。是**名转换器**。如果我们需要从 camelCase 更改为 snake_case，我们只需将这一行添加到配置中。

```
framework:
    serializer:
        enabled: true
        enable_annotations: true
        name_converter: serializer.name_converter.camel_case_to_snake_case 
```

从现在开始，`created at`属性将会是 snake_case 格式的。

[![alt text](img/138738a805df9c133240abb788993ad2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZPo0nG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kreo2ec6qjgr1brc7zi8.png)

### 总而言之

我想感谢你们所有人读到这里。序列化组件有很多特性。特别是，当你开发 REST Apis 的时候，它真的对你有帮助。