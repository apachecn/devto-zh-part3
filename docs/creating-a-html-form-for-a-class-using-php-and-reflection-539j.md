# 使用 PHP 和反射为类创建 HTML 表单

> 原文：<https://dev.to/emanuelvintila/creating-a-html-form-for-a-class-using-php-and-reflection-539j>

这是一篇来自我自己博客的交叉文章:[为一个类创建 HTML 表单](https://reflection.to/posts/php/2-Creating_a_HTML_form_for_a_class)

你有没有想过有没有比简单地写出标记更简单的创建表单的方法？。假设你的表单代表了一个可以用类来表示的对象，那肯定是有的；如果没有，那就绝对应该有。让我们在博客上写一篇过于简化的文章。

```
class Article {
    /** @var string */
    public $title = '';
    /** @var string */
    public $body = '';
    /** @var DateTime */
    public $date;

    public function __construct() {
        // this will be explained when we get to re-creating the object
        // after a POST request
        $this->date = $this->date ?
            new DateTime($this->date) :
            new DateTime();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望标题和正文是纯文本输入，日期是日期输入。我们将通过 GET 请求处理表单的显示，通过 POST 请求处理表单的提交。让我们从枚举类的属性开始，为每个属性创建一个输入，并用一个提交输入将它们包装在一个表单标签中。让我们直接进入**反射**。我只注释了`MakeInput`函数，因为其他两个函数应该是不言自明的。

```
/**
  * This function creates a valid HTML input with an associated label.
  * @param string $name The input's name and the label's text.
  * @param mixed $value The input's initial value. This parameter also determines the input's type.
  * @return string
  */
public function MakeInput(string $name, $value): string
{
    $type = gettype($value);
    $label = sprintf('<label for="%1$s">%1$s</label>', $name);
    switch ($type) {
        case 'boolean':
            $input_type = 'checkbox';
            break;
        case 'integer':
        case 'double':
            $input_type = 'number';
            break;
        case 'string':
            $input_type = 'text';
            break;
        case 'object':
            // special handling for object types
            $class = new ReflectionClass($value);
            if ($class->implementsInterface(DateTimeInterface::class)) {
                $input_type = 'date';
                $value = $class
                    ->getMethod('format')
                    ->invoke($value, 'Y-m-d');
                break;
            }
            // if we do not know how to handle the object, fall-through and throw
        default:
            throw new InvalidArgumentException($value);
    }
    $input = sprintf('<input name="%1$s" id="%1$s" type="%2$s" value="%3$s" />',
        $name, $input_type, $value);

    return $label . $input;
}

public function MakeInputs(string $class_name): array
{
    $inputs = [];
    $instance = new $class_name();
    $class = new ReflectionClass($instance);
    $properties = $class->getProperties();
    foreach ($properties as $property) {
        // make it accessible so we can get its value
        $property->setAccessible(true);
        $name = $property->getName();
        $value = $property->getValue($instance);
        // this syntax means that after submission our $_POST superglobal
        // will contain an array named $class_name which will represent
        // our class
        $inputs[] = self::MakeInput("{$class_name}[{$name}]", $value);
    }

    return $inputs;
}

public static function MakeForm(string $class_name): string
{
    $html = '<form method="POST">';
    foreach (self::MakeInputs($class_name) as $input)
        $html .= $input;
    $html .= '<input type="submit" />';
    $html .= '</form>';

    return $html;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只需要在我们的`index.php`
中写一行

```
echo FormHelper::MakeForm(Article::class); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的形式。这不是最漂亮的形式，但标记是有效的，它的工作。我们可以添加一个回调函数作为参数来添加类或将每个输入包装在容器中，但这超出了本文的范围。现在让我们处理表单的提交部分，即 POST 请求和我们的`Article`对象的重新创建。

```
public function MakeClassFromArray(string $class_name, array $values)
{
    $class = new ReflectionClass($class_name);
    // we do not call the constructor yet
    $instance = $class->newInstanceWithoutConstructor();
    // first we set each property to their respective value
    foreach ($values as $name => $value) {
        $property = $class->getProperty($name);
        $property->setAccessible(true);
        $property->setValue($instance, $value);
    }
    // note that we have set primitive values to our object properties
    // we late-call the constructor, like PDO does when fetching objects
    // and it re-creates the object instances from the primitive values
    $class->getConstructor()->invoke($instance);

    return $instance;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`index.php`变成了

```
$request_method = strtoupper($_SERVER['REQUEST_METHOD']);
$class = Article::class;
if ($request_method === 'GET') {
    echo MakeForm($class);
} elseif ($request_method === 'POST') {
    $article = MakeClassFromArray($class, $_POST[$class]);
    print_r($article);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在提交表单后，从请求中重新创建了`$article`变量，并准备好进行验证和存储到数据库中。

在下一篇文章中，我将介绍创建表单的另一种方法，这种方法将解决这种方法存在的问题，例如属性必须初始化为默认值，以及 body 属性由普通的输入字段而不是文本区域表示。不用担心，这还涉及到**反射**。