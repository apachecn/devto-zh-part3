# PHP 中的迭代器(实用指南)

> 原文：<https://dev.to/damnjan/iterator-in-php-a-practical-guide-2k59>

每次看到这个

```
$users = [new User(), new User()]; 
```

Enter fullscreen mode Exit fullscreen mode

我发现失去了使用迭代器的机会。

### 为什么是迭代器？

集合是一种组织以前没有命名的数组的好方法。有几个原因可以解释为什么你应该使用迭代器。其中一个原因是行为，你可以指定标准调用的确切行为，比如下一个、当前、有效等等。其他原因可能是您希望确保集合只包含特定类型的对象。

理解一个苦于使用一个数组的未知值类型。数组在 PHP 世界中非常常见，它被用来存储各种数据，以多种嵌套形式存储在多维空间中。数组给开发者带来了无限的灵活性，但正因为如此，它们变得非常邪恶。

### 举例:

*   您的函数(getUsers)返回一个用户对象数组。
*   另一个函数(setUsersToActiveState)使用 getUsers 输出数组并将所有用户的活动状态设置为 true。
*   setUsersToActiveState 循环遍历数组，并期望调用数组项上的特定方法。例如，方法名为 getActiveStatus。
*   如果给定的数组是具有可调用方法 getActiveStatus 的所需对象的数组，则一切正常。但是如果没有，将会抛出异常。
*   我们如何确保给定的数组总是一个特定类型的对象数组？

```
public function getUsers(): array
{
    /** 
    here happen something which gets users from database
    ....
    **/
    return $userArray;
}

public function setUsersToActiveState()
{
    $users = $this->getUsers();
     /** @var User $param */
    foreach ($users as $user) {
        if(!$user->getActiveStatus()) {
            $user->setActiveStatus(true);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

立即出现了两个问题。

1.  一个是类型的问题。我们的 ide 不知道$users 数组中有什么，因此 IDE 不能建议我们如何使用$user 元素。(我把这个注释块/** [@var](https://dev.to/var) User $param */放在前面，它适用于 phpStorm，我猜也适用于其他一些 ide)
2.  你的同事！如果没有任何提示，他们怎么可能知道数组里面是什么？
3.  额外的问题是，getUsers 可以返回任何数组，系统中不会有警告。

### 解

```
// Create a collection which accepts only Users 

class UsersCollection implements \IteratorAggregate
{
    /** @var array */
    private $users = [];

    public function getIterator() : UserIterator
    {
        return new UserIterator($this);
    }

    public function getUser($position)
    {
        if (isset($this->users[$position])) {
            return $this->users[$position];
        }

        return null;
    }

    public function count() : int
    {
        return count($this->users);
    }

    public function addUser(User $users)
    {
        $this->users[] = $users;
    }
}

// Create an Iterator for User
class UserIterator implements \Iterator
{
    /** @var int */
    private $position = 0;

    /** @var UsersCollection */
    private $userCollection;

    public function __construct(UsersCollection $userCollection)
    {
        $this->userCollection = $userCollection;
    }

    public function current() : User
    {
        return $this->userCollection->getUser($this->position);
    }

    public function next()
    {
        $this->position++;
    }

    public function key() : int
    {
        return $this->position;
    }

    public function valid() : bool
    {
        return !is_null($this->userCollection->getUser($this->position));
    }

    public function rewind()
    {
        $this->position = 0;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 测试

当然，还有一些测试来确保我们的集合和迭代器像魔法一样工作。对于这个例子，我使用 PHPUnit 框架的语法。

```
class UsersCollectionTest extends TestCase
{
    /**
     * @covers UsersCollection
     */
    public function testUsersCollectionShouldReturnNullForNotExistingUserPosition()
    {
        $usersCollection = new UsersCollection();

        $this->assertEquals(null, $usersCollection->getUser(1));
    }

    /**
     * @covers UsersCollection
     */
    public function testEmptyUsersCollection()
    {
        $usersCollection = new UsersCollection();

        $this->assertEquals(new UserIterator($usersCollection), $usersCollection->getIterator());

        $this->assertEquals(0, $usersCollection->count());
    }

    /**
     * @covers UsersCollection
     */
    public function testUsersCollectionWithUserElements()
    {
        $usersCollection = new UsersCollection();
        $usersCollection->addUser($this->getUserMock());
        $usersCollection->addUser($this->getUserMock());

        $this->assertEquals(new UserIterator($usersCollection), $usersCollection->getIterator());
        $this->assertEquals($this->getUserMock(), $usersCollection->getUser(1));
        $this->assertEquals(2, $usersCollection->count());
    }

    private function getUserMock()
    {
        // returns the mock of User class
    }
}

class UserIteratorTest extends MockClass
{
    /**
     * @covers UserIterator
     */
    public function testCurrent()
    {
        $iterator = $this->getIterator();
        $current = $iterator->current();

        $this->assertEquals($this->getUserMock(), $current);
    }

    /**
     * @covers UserIterator
     */
    public function testNext()
    {
        $iterator = $this->getIterator();
        $iterator->next();

        $this->assertEquals(1, $iterator->key());
    }

    /**
     * @covers UserIterator
     */
    public function testKey()
    {
        $iterator = $this->getIterator();

        $iterator->next();
        $iterator->next();

        $this->assertEquals(2, $iterator->key());
    }

    /**
     * @covers UserIterator
     */
    public function testValidIfItemInvalid()
    {
        $iterator = $this->getIterator();

        $iterator->next();
        $iterator->next();
        $iterator->next();

        $this->assertEquals(false, $iterator->valid());
    }

    /**
     * @covers UserIterator
     */
    public function testValidIfItemIsValid()
    {
        $iterator = $this->getIterator();

        $iterator->next();

        $this->assertEquals(true, $iterator->valid());
    }

    /**
     * @covers UserIterator
     */
    public function testRewind()
    {
        $iterator = $this->getIterator();

        $iterator->rewind();

        $this->assertEquals(0, $iterator->key());
    }

    private function getIterator() : UserIterator
    {
        return new UserIterator($this->getCollection());
    }

    private function getCollection() : UsersCollection
    {
        $userItems[] = $this->getUserMock();
        $userItems[] = $this->getUserMock();

        $usersCollection = new UsersCollection();

        foreach ($userItems as $user) {
            $usersCollection->addUser($user);
        }

        return $usersCollection;
    }

    private function getUserMock()
    {
        // returns the mock of User class
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
public function getUsers(): UsersCollection
{
    $userCollection = new UsersCollection();
    /** 
    here happen something which gets users from database
    ....
    **/
    foreach ($whatIGetFromDatabase as $user) {
        $userCollection->addUser($user);
    }
    return $userCollection;
}

public fucntion setUsersToActiveState()
{
    $users = $this->getUsers();

    foreach ($users as $user) {
        if(!$user->getActiveStatus()) {
            $user->setActiveStatus(true);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，setUsersToActiveState 保持不变，我们只是不需要为 IDE 或 collagues 指定$users 变量的类型。

### 扩展功能

信不信由你，你可以重用这两个对象，只需改变变量的名字来满足大多数需求。但是，如果您想要任何更复杂的功能，请随意将其添加到迭代器或集合中。

#### 例 1

例如，假设 userCollection 只接受年龄超过 18 岁的用户。实现将在方法 addUser 中的 UsersCollection 类中进行。

```
 public function addUser(User $users)
    {
        if ($user->getAge() > 18) {
            $this->users[] = $users;
        }    
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 例 2

您需要添加批量用户。然后，您可以使用额外的方法 addUsers 来扩展您的 userCollection，可能如下所示。

```
 public function addUsers(array $users)
    {
        foreach($users as $user) {
            $this->addUser(User $users);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 注

我发现了这篇很棒的文章，它回答了为什么返回一个数组通常是个坏主意，在这个话题上我非常同意 [@aleksikauppila](https://dev.to/aleksikauppila)

[![aleksikauppila](img/30dcda52685d9f3050dbf26154c3df62.png)](/aleksikauppila) [## 不要返回关联数组！

### 阿列克西·亚历山德罗维奇 8 月 15 日 18 日 3 分钟前读完

#php #oop #beginners](/aleksikauppila/dont-return-associative-arrays-28il)