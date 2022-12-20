# 当 PHP 框架吸系列:业务逻辑自由控制器

> 原文：<https://dev.to/damnjan/when-php-framework-sucks-series-business-logic-free-controllers-46gj>

### 控制器中的业务逻辑

让我们从控制器和您的业务逻辑开始。规则一:无论如何不要把业务逻辑放在控制器中！控制器中的业务逻辑尤其成问题，因为控制器通常是从框架中派生出来的。还有，当他们扩展框架基本控制器类时，他们通常会知道太多(容器)。顾名思义，业务逻辑是您希望重用并独立于库和框架的代码。它应该被很好地分离和封装，因为你可能从不同的地方调用同一个方法。

这是 Symfony 控制器的例子[最佳实践页面](https://symfony.com/doc/current/best_practices/controllers.html) :

```
namespace App\Controller;

use App\Entity\Post;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;

class DefaultController extends AbstractController
{
    /**
     * @Route("/", name="homepage")
     */
    public function index()
    {
        $posts = $this->getDoctrine()
            ->getRepository(Post::class)
            ->findLatest();

        return $this->render('default/index.html.twig', [
            'posts' => $posts,
        ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，业务逻辑的位置不合适。相反，我们应该有一个返回最新帖子的类。为了简单起见，我们可以将该类称为 PostsFromDatabase。

```
namespace ExampleNamespace;

class PostsFromDatabase
{
    /** @var ManagerRegistry**/
    private $doctrine;

    public function getDoctrine()
    {
        return $this->doctrine;
    }

    public function __construct(ManagerRegistry $doctrine)
    {
        $this->doctrine = $doctrine;
    }

    public function getLatest()
    {
        $posts = $this->getDoctrine()
            ->getRepository(Post::class)
            ->findLatest();

        return $posts;

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，对这个类的测试很简单

```
namespace ExampleNamespace;

use Doctrine\Common\Persistence\ManagerRegistry;
use Doctrine\ORM\EntityRepository;

class PostsFromDatabaseTest extends \PHPUnit_Framework_TestCase
{
    /**
     * @covers \ExampleNamespace\PostsFromDatabase
     */
    public function testGetLatestShouldReturnSameCollectionAsFindLatestReturns()
    {
        $managerRegistryMock = $this->createMock(ManagerRegistry::class);
        $repository = $this->createMock(EntityManagerInterface::class);

        $managerRegistryMock
            ->expects($this->once())
            ->method('getRepository')
            ->with(Post::class)
            ->willReturn($repository);
        $repository
            ->expects($this->once())
            ->method('findLatest')
            ->willReturn(['post1', 'post2']);

        $postsFromDatabase = new PostsFromDatabase($managerRegistryMock);
        $latestPosts = $postsFromDatabase->getLatest();

        $this->assertEquals(['post1', 'post2'], $latestPosts);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的控制器看起来像这样

```
namespace App\Controller;

use ExampleNamespace\PostsFromDatabase
use App\Entity\Post;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;

class DefaultController extends AbstractController
{
    /** @var PostsFromDatabase **/
    private $postsFromDatabase;

    public function getPostsFromDatabase(): PostsFromDatabase
    {
        if (null === $this->postsFromDatabase) {
            $this->postsFromDatabase = new PostsFromDatabase($this->getDoctrine());
        }

        return $this->postsFromDatabase;
    }

    public function setPostsFromDatabase(PostsFromDatabase $postsFromDatabase)
    {
        $this->postsFromDatabase = $postsFromDatabase;
    }

    /**
     * @Route("/", name="homepage")
     */
    public function index()
    {
        $posts = $this->getPostsFromDatabase()->getLatest();

        return $this->render('default/index.html.twig', [
            'posts' => $posts,
        ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

控制器脱离了业务逻辑，现在只处理路由、渲染，并知道调用哪个服务。但是还有一个好处你可能还没有发现:

### 我们一半的代码，现在有一个单元测试🤩🤩🤩🤩🤩🤩

你不应该就此打住，现在控制器非常容易测试(这就是为什么我为 PostsFromDatabase 留下了“set”和“get”方法)，但是我将在本系列的下一章中讨论这一点。

在这里呆一会儿。你曾经在框架教程的例子中看到过，暗示你应该测试你的代码吗？大多数现代框架都有测试的“占位符”。但是我不记得我见过一个框架已经实现了单元测试度量、必需的测试或者在他们的教程例子中的测试。我把这个问题留在这里，留待以后的一些博文。