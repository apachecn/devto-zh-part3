# Levensthein 算法带来更好的 faker 体验

> 原文：<https://dev.to/aexol/levensthein-algorithm-for-better-faker-experience-2ccf>

今天我写的是关于再次使用 [ts-api-faker](https://github.com/slothking-online/ts-api-faker) 伪造数据。通常我们把这种数据传给 faker

```
[  {  "name":  "name.firstName",  "surname":  "name.lastName",  "mail":  "internet.email",  "profilePhoto":  "internet.avatar",  "animalPhoto":  "image.cat"  },  {  "name":  "name.firstName",  "surname":  "name.lastName",  "mail":  "internet.email",  "profilePhoto":  "internet.avatar",  "animalPhoto":  "image.dog"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

然后我们收到伪造的数据

```
[  {  "name":  "Van",  "surname":  "Veum",  "mail":  "Corbin.Fritsch49@gmail.com",  "profilePhoto":  "https://s3.amazonaws.com/uifaces/faces/twitter/pixage/128.jpg",  "animalPhoto":  "https://source.unsplash.com/200x200/?cat"  },  {  "name":  "Serena",  "surname":  "Wilderman",  "mail":  "Mariam_Thiel@hotmail.com",  "profilePhoto":  "https://s3.amazonaws.com/uifaces/faces/twitter/imcoding/128.jpg",  "animalPhoto":  "https://source.unsplash.com/200x200/?dog"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

它运行完美，但是...通常一个模式看起来像这样。

```
[  {  "name":  "String",  "surname":  "String",  "mail":  "String",  "profilePhoto":  "String",  "animalPhoto":  "String"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

faker 的回答不合适然后

```
[  {  "name":  "atque",  "surname":  "eos",  "mail":  "voluptas",  "profilePhoto":  "similique",  "animalPhoto":  "earum"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

如果对值的键使用 levensthein 算法，并将值转换为最接近键名的选项，会怎么样？

我们再次通过

```
[  {  "name":  "String",  "surname":  "String",  "mail":  "String",  "profilePhoto":  "String",  "animalPhoto":  "String"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

瞧，我们得到了正确的 faker 结构。

```
[  {  "name":  "Edgar",  "surname":  "Alyce64",  "mail":  "Eusebio.Gottlieb@yahoo.com",  "profilePhoto":  "https://source.unsplash.com/200x200/?profile",  "animalPhoto":  "https://source.unsplash.com/200x200/?animal"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

[![Great Success](img/fe75346cc249cabd309000f2284bda52.png)](https://i.giphy.com/media/a0h7sAqON67nO/giphy.gif)

我刚刚将这个特性添加到我的 repo [ts-api-faker](https://github.com/slothking-online/ts-api-faker) 中

请随意贡献和扩展这个库的功能。如果你支持我和这个想法，请启动这个回购，以帮助我获得更多的贡献者和扩展 ts-api-faker 库。

在未来，我们可以添加机器学习，并为我们的 API 和数据库获得最终的智能 faker:)