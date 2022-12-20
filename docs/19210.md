# @冬眠中的一对一关系及其危险

> 原文：<https://dev.to/schmowser/onetomany-relations-in-hibernate-and-its-perils-1a41>

最近，我在用 Hibernate 持久化一个对象时遇到了麻烦。我想用记录卡拉 ok 酒吧麦克风的著名例子来描述我的旅程！这里，我们有两个麦克风(当然，一个黄色的到必须的黑色的！)为我们叫怪兽卡拉 ok 的酒吧。我们首先创建麦克风，然后创建条，最后将其保存到相应的 JPA 存储库中。

```
Microphone blackMicrophone = Microphone.builder()
                .microphoneId(UUID.randomUUID())
                .color("black")
                .build();

Microphone yellowMicrophone = Microphone.builder()
                .microphoneId(UUID.randomUUID())
                .color("yellow")
                .build();

List<Microphone> microphoneList = Arrays.asList(blackMicrophone, yellowMicrophone);

KaraokeBar karaokeBar = KaraokeBar.builder()
                .barId(UUID.randomUUID())
                .name("Monster Karaoke")
                .microphoneList(microphoneList)
                .build();

karaokeBarRepository.save(karaokeBar); 
```

Enter fullscreen mode Exit fullscreen mode

KaraokeBar 对象包含一个麦克风对象列表，用 Hibernate 的`@OneToMany`进行了注释。但是当执行代码时，会发生以下情况:

```
Request processing failed; 
nested exception is org.springframework.orm.jpa.JpaObjectRetrievalFailureException: 
Unable to find de.schmowser.radio.domain.Microphone with id fb133ab8-72ee-4bf4-ac5f-4701cb99e766; 
```

Enter fullscreen mode Exit fullscreen mode

找不到吗？我认为我们需要级联持久化，这样当 KaraokeBar 实例被。但是即使加上了

```
@OneToMany(cascade = CascadeType.PERSIST)
private List<Microphone> microphoneList; 
```

Enter fullscreen mode Exit fullscreen mode

a `javax.persistence.EntityNotFoundException`让我们相信麦克风仍然不能坚持使用。直到现在，我记得我们知道有人负责将任何实体持久化到数据库中。它是`@PersistenceContext`中的 EntityManager。在自动连接并使用 entityManager.perist 之后，会弹出:

```
No EntityManager with actual transaction available for current thread - 
cannot reliably process 'persist' call 
```

Enter fullscreen mode Exit fullscreen mode

是的，我们必须用`@Transactional`来注释这个方法，表示所有的事务都在后台自动处理！此外，我们必须首先定义所有者端对象(karaokeBar)，然后在我们的麦克风实例中设置它，然后在 KaraokeBar 中设置列表。否则，会发生此异常。

```
org.hibernate.TransientPropertyValueException: 
Not-null property references a transient value - 
transient instance must be saved before current operation : 
de.schmowser.radio.domain.Microphone.karaokeBar -> de.schmowser.radio.domain.KaraokeBar 
```

Enter fullscreen mode Exit fullscreen mode

感觉我们就要让顾客知道我们酒吧提供哪些话筒了。奇怪的是，如果没有 Repository.save 命令，下面的代码片段会编译...

```
List<Microphone> microphoneList = Arrays.asList(blackMicrophone, yellowMicrophone);

karaokeBar.setMicrophoneList(microphoneList);

entityManager.persist(karaokeBar);
entityManager.persist(blackMicrophone);
entityManager.persist(yellowMicrophone);

karaokeBarRepository.save(karaokeBar); 
```

Enter fullscreen mode Exit fullscreen mode

...而*与*储蓄，则不然。将最后一行包含在内会奖励我们一个`java.lang.UnsupportedOperationException`。这个实际上很难破译。不知何故，Hibernate 既不喜欢我们，也不喜欢不可修改的列表。因此，我们给他它想要的:一些要修改的东西。

```
List<Microphone> microphoneList = new ArrayList<>();
microphoneList.add(blackMicrophone);
microphoneList.add(yellowMicrophone); 
```

Enter fullscreen mode Exit fullscreen mode

冬眠是仁慈的，给我们所渴望的！

```
Hibernate: insert into karaoke_bar (name, karaokebar_id) values (?, ?)
Hibernate: insert into microphone (color, karaokebar_id, microphone_id) values (?, ?, ?)
Hibernate: insert into microphone (color, karaokebar_id, microphone_id) values (?, ?, ?) 
```

Enter fullscreen mode Exit fullscreen mode

这和你使用 Hibernate 的经历有什么关系吗？你如何处理这种异常风暴——一般来说，也许更有效？