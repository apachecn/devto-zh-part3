# 迭代器问题的答案

> 原文：<https://dev.to/damnjan/answer-to-the-iterator-question-588a>

我得到了这个问题

[![godsgood33 profile image](img/fd33fa3d2a1fdd08b6094932fe446d11.png) ](/godsgood33) [ Ryan P ](/godsgood33) • [<time datetime="2018-12-03T22:02:04Z"> Dec 3 '18 </time> • Edited  on <time datetime="2018-12-03T22:02:45Z"> Dec 3</time>](https://dev.to/godsgood33/comment/7ajm) 

如果集合索引不是从 0 开始，您将如何实现一个解决方案？在我正在做的项目中，有几个地方遍历一个大数组需要很长时间，所以我用一个已知的数字指定索引。然后我可以做一个`isset($arr[$obj->id])`来查找、检索、删除或覆盖那个位置的对象。我试图通过修改您的代码来实现这一点，但是它给迭代器中的`$position`变量带来了一个问题。在 PHP 7.3 中，他们增加了一个`array_key_first()`方法来更容易地获得第一个密钥，但是我在 7.2 中操作。我尝试过 foreach 攻击，但是它在迭代器中创建了一个内部循环。

我发现它非常有趣，所以我决定给它一个完整的帖子来回答它，即使有 80%的代码库与原始帖子相似。我决定从集合的角度来解决这个问题，因为迭代器与搜索 Ryan 想要实现的 Id 函数无关。不过，迭代能力保持不变:)所以每个人都应该高兴。请特别注意我写的测试，它们和原始文章中的一样，只是增加了一些功能

原帖在此:

[![damnjan](img/e399ef90f1b8b03b984850e6ba9e43cb.png)](/damnjan) [## PHP 中的迭代器(实用指南)

### damn Jan Jovanovic 12 月 2 日 184 分钟阅读

#php #webdev #showdev #coding](/damnjan/iterator-in-php-a-practical-guide-2k59)

##### 注

如果你读了这个问题和这篇博文，并且你认为你有更好、更智能、更优化的 ar 清洁器解决方案，请随意贡献，我很乐意看到其他方法。

##### 用户类

用户类只是一个返回 Id 的类的例子，我们将在测试中一直模拟它，这样你就可以实现你自己的

```
// Example class with getId method
class User
{
    public function getId()
    {
        return rand(1, 1000000);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 用户迭代器

比我们没有改变的迭代器

```
class UserIterator implements \Iterator
{
    /** @var int */
    private $position = 0;

    /** @var UsersCollection */
    private $usersCollection;

    public function __construct(UsersCollection $userCollection)
    {
        $this->usersCollection = $userCollection;
    }

    public function current() : User
    {
        return $this->usersCollection->getUser($this->position);
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
        return !is_null($this->usersCollection->getUser($this->position));
    }

    public function rewind()
    {
        $this->position = 0;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 用户集合

最后，此处的集合包含新的公共方法 getUserById、removeById 和 updateUserById。另外，请注意，在将新用户添加到集合中的过程中，我们还会将新 id 添加到数组 userIds 中。该数组包含作为键的 userId 和作为值的迭代器位置。在[测试部分](#test_collection)
有更多关于事情如何运作的信息

```
class UsersCollection implements \IteratorAggregate
{
    /** @var array */
    private $users = [];

    private $userIds = [];

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

    public function getUserById($userId)
    {
        if (isset($this->userIds[$userId])) {
            return $this->getUser($this->userIds[$userId]);
        }

        return null;
    }

    public function count() : int
    {
        return count($this->users);
    }

    public function removeById($userId)
    {
        if (isset($this->userIds[$userId])) {
            unset($this->userIds[$userId]);
        }
    }

    public function updateUserById($userId, User $user)
    {
        if ($user->getId() !== $userId) {
            throw new Exception('User Id mismatch');
        }

        if (isset($this->userIds[$userId])) {
            $position = $this->userIds[$userId];
            $this->users[$position] = $user;
        }
    }

    public function addUser(User $user)
    {
        $this->users[] = $user;
        $this->setUserId($user);
    }

    private function setUserId(User $user)
    {
        $userId = $user->getId();
        $currentPosition = count($this->users) - 1;
        $this->userIds[$userId] = $currentPosition;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 用户迭代测试

迭代器的测试与原始文章中的测试非常相似，我只是对它进行了修改，实现了实际的用户模拟。

```
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

    /**
     * @return \PHPUnit\Framework\MockObject\MockObject | User
     */
    private function getUserMock()
    {
        $userMock = $this->getMockBuilder(User::class)->getMock();
        return $userMock;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 用户集合测试

和 tests for Collection now 测试两个新的“get”情况，通过现有 id 获取用户，如果用户 Id 不存在，则返回 null。此外，还有删除用户的测试和更新现有用户的两种情况。

```
class UsersCollectionTest extends MockClass
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

    /**
     * @covers UsersCollection
     */
    public function testSearchForUserByIdShouldReturnUserWithGivenId()
    {
        $user1 = $this->getUserMock();
        $user2 = $this->getUserMock();
        $user3 = $this->getUserMock();

        $user1->expects($this->once())
            ->method('getId')
            ->willReturn(123);
        $user2->expects($this->once())
            ->method('getId')
            ->willReturn(111);
        $user3->expects($this->once())
            ->method('getId')
            ->willReturn(345);

        $usersCollection = new UsersCollection();
        $usersCollection->addUser($user1);
        $usersCollection->addUser($user2);
        $usersCollection->addUser($user3);

        $this->assertEquals($user3, $usersCollection->getUserById(345));
        $this->assertEquals($user2, $usersCollection->getUserById(111));
        $this->assertEquals($user1, $usersCollection->getUserById(123));
    }

    /**
     * @covers UsersCollection
     */
    public function testSearchForUserByIdWhichNotExistShouldReturnNull()
    {
        $user1 = $this->getUserMock();
        $user2 = $this->getUserMock();

        $user1->expects($this->once())
            ->method('getId')
            ->willReturn(1);
        $user2->expects($this->once())
            ->method('getId')
            ->willReturn(2);

        $usersCollection = new UsersCollection();
        $usersCollection->addUser($user1);
        $usersCollection->addUser($user2);

        $this->assertEquals(null, $usersCollection->getUserById(4));
        $this->assertEquals(null, $usersCollection->getUserById(100));
    }

    /**
     * @covers UsersCollection
     */
    public funCtion testIfOneUserIsRemovedFromCollectionSearchOnUserIdShouldReturnNull()
    {
        $user1 = $this->getUserMock();
        $user2 = $this->getUserMock();
        $user3 = $this->getUserMock();

        $user1->expects($this->once())
            ->method('getId')
            ->willReturn(123);
        $user2->expects($this->once())
            ->method('getId')
            ->willReturn(111);
        $user3->expects($this->once())
            ->method('getId')
            ->willReturn(345);

        $usersCollection = new UsersCollection();
        $usersCollection->addUser($user1);
        $usersCollection->addUser($user2);
        $usersCollection->addUser($user3);

        $usersCollection->removeById(111);

        $this->assertEquals($user3, $usersCollection->getUserById(345));
        $this->assertEquals(null, $usersCollection->getUserById(111));
        $this->assertEquals($user1, $usersCollection->getUserById(123));
    }

    /**
     * @covers UsersCollection
     */
    public funCtion testUpdateUserByIdShouldReplaceUserObjectOnThisPosition()
    {
        $user1 = $this->getUserMock();
        $user2 = $this->getUserMock();
        $user3 = $this->getUserMock();
        $user4 = $this->getUserMock();
        // this property is set to ensure that object is different than $user1
        $user4->property = 4;

        $user1->expects($this->once())
            ->method('getId')
            ->willReturn(123);
        $user2->expects($this->once())
            ->method('getId')
            ->willReturn(111);
        $user3->expects($this->once())
            ->method('getId')
            ->willReturn(345);
        $user4->expects($this->once())
            ->method('getId')
            ->willReturn(123);

        $usersCollection = new UsersCollection();
        $usersCollection->addUser($user1);
        $usersCollection->addUser($user2);
        $usersCollection->addUser($user3);

        $usersCollection->updateUserById(123, $user4);

        $this->assertEquals($user4, $usersCollection->getUserById(123));
    }

    /**
     * @expectedExceptionMessage User Id mismatch
     * @expectedException \Exception
     * @covers UsersCollection
    */
    public funCtion testUpdateUserWhenUserIdAndGivenIdMismatchShouldThrowException()
    {
        $user1 = $this->getUserMock();
        $user2 = $this->getUserMock();

        $user1->expects($this->once())
            ->method('getId')
            ->willReturn(123);
        $user2->expects($this->once())
            ->method('getId')
            ->willReturn(444);

        $usersCollection = new UsersCollection();
        $usersCollection->addUser($user1);

        $usersCollection->updateUserById(123, $user2);
    }

    /**
     * @return \PHPUnit\Framework\MockObject\MockObject | User
     */
    private function getUserMock()
    {
        $userMock = $this->getMockBuilder(User::class)->getMock();
        return $userMock;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode