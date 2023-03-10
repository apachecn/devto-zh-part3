# 为一个类创建 HTML 表单——第 2 部分

> 原文：<https://dev.to/emanuelvintila/creating-an-html-form-for-a-class-part-2-5cih>

这篇文章改进了[上一篇文章](https://reflection.to/posts/php/2-Creating_an_HTML_form_for_a_class)中描述的概念，并将改进实现。

这篇文章中的大部分代码都是建立在前一篇文章中的代码之上的，但是它是独立的，所以你不必阅读前一篇文章，尽管我强烈建议你这样做。

使用**反射**，我们不仅可以检查一个类的属性或方法，还可以检查任何特定属性、方法或类的[文档注释](https://docs.phpdoc.org/guides/docblocks.html)。使用 DocComment，我们可以注释类的属性来帮助我们的表单构建方法。让我们将`Article`类扩展如下:

```
class Article
{
    /**
     * @var int
     * @Readonly
     */
    public $id = 1;

    /** @var string */
    public $title;

    /**
     * @var string
     * @Textarea
     */
    public $body;

    /** @var DateTime */
    public $date;

    public function __construct()
    {
        $this->date = $this->date ?
            new DateTime($this->date) :
            new DateTime();
    }
} 
```

我们在文章中添加了一个`id`属性，它将是只读的，而`body`属性应该显示为一个文本区域，正如它们各自的注释所指定的那样。注意我们的属性不再有初始化器，因为我们也将使用`@var`注释来推导它们的类型。在将文档注释解析成注释的`array`之后，我们可以用它来构建我们的表单。

```
function ParseDocCommentAnnotations(string $comment): array
{
    $matches = null;
    $annotations = [];
    $lines = explode("\n", $comment);
    foreach ($lines as $line) {
        $line = trim($line);
        // match all lines that start with an @ and capture the relevant parts
        if (1 === preg_match('/^\/?\*+\s*(@.*?)(?:\s*\*\/)?$/', $line,
                $matches)) {
            $annotation = $matches[1];
            // eventually split them on white-space, to handle @var annotations
            // or others that might also specify a value, such as @min, @max etc.
            list($key, $value) = preg_split('/\s+/', $annotation, 2);
            // if the value is falsy, use the key as the value
            // we could use literal true instead or whatever makes sense
            $value = $value ?? $key;
            $annotations[strtolower($key)] = $value;
        }
    }

    return $annotations;
} 
```

对于`id`属性，得到的数组将等同于`[var' => 'int', readonly' => '@readonly']`。让我们修改`MakeInput`函数，接受一个`ReflectionProperty $property`参数和一个`$object`参数，并利用上面的函数返回一个表单输入。

```
/**
 * This function creates a valid HTML input with an associated label.
 * @param ReflectionProperty $property
 * @param mixed $object An instance of the class that the $property parameter
 * was declared into
 * @return string
 */
function MakeInput(ReflectionProperty $property, $object): string
{
    $annotations = ParseDocCommentAnnotations($property->getDocComment());
    if (false === array_key_exists('@var', $annotations))
        // we could throw an exception instead
        // or assume that the property is a string
        return '';

    $class = $property->getDeclaringClass();
    $name = $property->getName();
    $value = $property->getValue($object);
    $input_name = "{$class->getName()}[{$name}]";
    $is_textarea = array_key_exists('@textarea', $annotations) &&
        $annotations['@var'] === 'string';
    // the for attribute targets an element with the specified id, not the name
    $label = sprintf('<label for="%s">%s</label>', $input_name, $name);

    $input_attributes = ['name' => $input_name, 'id' => $input_name];
    if (false === $is_textarea)
        $input_attributes['value'] = $value;
    if (array_key_exists('@readonly', $annotations))
        $input_attributes['readonly'] = 'readonly';

    switch ($type = $annotations['@var']) {
        case 'bool':
            $input_attributes['type'] = 'checkbox';
            break;
        case 'int':
        case 'double':
        case 'float':
            $input_attributes['type'] = 'number';
            // this is where the power of the annotations comes in
            // you can annotate your class's properties in any way you want
            // and then use those annotations to build the form
            if (array_key_exists('@min', $annotations))
                $input_attributes['min'] = $annotations['@min'];
            if (array_key_exists('@max', $annotations))
                $input_attributes['max'] = $annotations['@max'];
            if (array_key_exists('@step', $annotations))
                $input_attributes['step'] = $annotations['@step'];
            break;
        case 'string':
            if (false === $is_textarea)
                $input_attributes['type'] = 'text';
            break;
        default:
            // maybe the annotation specifies a class that we know how to
            // display as a form input
            $class = new ReflectionClass($type);
            if ($class->implementsInterface(DateTimeInterface::class)) {
                $input_attributes['type'] = 'date';
                break;
            }
            throw new InvalidArgumentException("The property {$name} with type {$type} could not be converted into an input.");
    }

    $attributes_string = '';
    foreach ($input_attributes as $k => $v)
        $attributes_string .= sprintf(' %s="%s"', $k, addslashes($v));

    if (false === $is_textarea)
        $input = "<input {$attributes_string} />";
    else
        $input = "<textarea {$attributes_string}>{$value}</textarea>";

    return $label . $input;
} 
```

我们还重构了`MakeInput`和`MakeForm`函数以接受一个对象实例作为参数，重构了`MakeObjectFromArray`函数以接受一个`ReflectionClass`参数。

```
function MakeInputs($object): array
{
    $inputs = [];
    $class = new ReflectionClass($object);
    $properties = $class->getProperties();
    foreach ($properties as $property)
        // we could use yield here and change the return type to Generator
        $inputs[] = MakeInput($property, $object);

    return $inputs;
}

/**
 * @param mixed $object An object instance of a class
 * that has its properties annotated
 * @return string
 */
function MakeForm($object): string
{
    $html = '<form method="POST">';
    foreach (MakeInputs($object) as $input)
        $html .= $input;
    $html .= '<input type="submit" />';
    $html .= '</form>';

    return $html;
}

function MakeObjectFromArray(ReflectionClass $class, array $values)
{
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

最后，让我们在我们的`index.php`
中写下以下内容

```
$request_method = strtoupper($_SERVER['REQUEST_METHOD']);
if ($request_method === 'GET') {
    echo MakeForm(new Article);
} elseif ($request_method === 'POST') {
    $class = new ReflectionClass(Article::class);
    $article = MakeObjectFromArray($class, $_POST[$class->getName()]);
    // validate and save article or re-show form and display errors
    // echo MakeForm($article);
    print_r($article);
} 
```

打开我们的浏览器，一切就绪，`id`属性的输入是 readonly，`body`属性的输入是 textarea，正如我们在注释中指定的。