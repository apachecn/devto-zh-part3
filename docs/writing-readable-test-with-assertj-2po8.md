# 用 AssertJ 编写可读测试

> 原文：<https://dev.to/gossie/writing-readable-test-with-assertj-2po8>

[AssertJ](http://joel-costigliola.github.io/assertj/) 是一个提供“java 的流畅断言”的库。断言的流畅接口允许开发人员编写可读性很强的测试。在本文中，我将尝试展示如何为您自己的域对象实现这一点。
让我们想象有一种返回 ValidationResult 的验证器。验证结果可能是这样的:

```
public enum ErrorCause {
    PARSE,
    RUNTIME
}

public class ValidationResult {

    private final boolean failed;
    private final ErrorCause errorCause;
    private final String message;

    public ValidationResult(boolean failed, ErrorCause errorCause, String message) {
        this.failed = failed;
        this.errorCause = errorCause;
        this.message = message;
    }

    public boolean hasFailed() {
        return failed;
    }

    public ErrorCause getErrorCause() {
        return errorCause;
    }

    public String getMessage() {
        return message;
    }
} 
```

当测试验证器时，将根据测试的预期来检查验证结果。用 [AssertJ](http://joel-costigliola.github.io/assertj/) 描述断言的一种方式如下。

```
ValidationResult result = validator.validateSomething(...);
assertThat(result.hasFailed()).isTrue();
assertThat(result.getErrorCause()).isEqualTo(ErrorCause.PARSE);
assertThat(result.getMessage()).isEqualTo("the expected message"); 
```

如果这样做，与其他断言库相比，您并没有真正获得什么。
可以自动生成断言。这将使测试代码看起来像这样。

```
ValidationResult result = validator.validateSomething(...);
assertThat(result).isFailed().hasErrorCause(ErrorCause.PARSE).hasMessage("the expected message"); 
```

这里的问题是，可读性仍然可以提高。AssertJ 让你为你的域对象编写你自己的断言类。这意味着所有的方法都可以被命名为产生真正可读的测试的方式。类 ValidationResultAssert 可能是这样的。

```
public class ValidationResultAssert extends AbstractAssert<ValidationResultAssert, ValidationResult> {

    private  ValidationResultAssert(ValidationResult actual) {
        super(actual, ValidationResultAssert.class);
    }

    public ValidationResultAssert hasFailed() {
        Assertions.assertThat(actual.hasFailed()).isTrue();
        return this;
    }

    public ValidationResultAssert dueToAParsingError() {
        Assertions.assertThat(actual.getErrorCause()).isEqualTo(ErrorCause.PARSE);
        return this;
    }

    public ValidationResultAssert dueToCauseARuntimeError() {
        Assertions.assertThat(actual.getErrorCause()).isEqualTo(ErrorCause.RUNTIME);
        return this;
    }

    public ValidationResultAssert withTheMessage(String message) {
        Assertions.assertThat(actual.getMessage()).isEqualTo(message);
        return this;
    }

    public static ValidationResultAssert assertThat(ValidationResult actual) {
        return new ValidationResultAssert(actual);
    }
} 
```

这导致了一个可以像句子一样阅读的断言，这使得理解为什么测试中的期望是这样的变得非常容易。除此之外，这样的测试还提供了对您的领域模型中的依赖关系的明确描述。

```
assertThat(result)
    .hasFailed()
    .dueToAParsingError()
    .withTheMessage("the expected message"); 
```

这是一个相当简单的例子，但是随着代码复杂性的增加，测试的可读性变得越来越重要。