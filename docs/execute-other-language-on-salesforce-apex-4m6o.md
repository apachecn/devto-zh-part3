# 在 Salesforce Apex 上执行其他语言

> 原文：<https://dev.to/tzmfreedom/execute-other-language-on-salesforce-apex-4m6o>

我试图在 Salesforce 上执行不是 Apex 的服务器端语言**。**

*   [tzmfreedom/迷你解析器](https://github.com/tzmfreedom/mini-parser)

## 语言规范

规格如下。

```
// dynamic declaration
a = 123 

// function call
puts "hello" 

// for loop
for i = 0; i < 10; i = i + 1 {
  puts i
}

// while loop
i = 0
while i < 3 {
  puts i
  i = i + 1
}

// if statement
if a == 123 {
  puts "a"
} else {
  puts "b"
} 
```

## 如何实现

### 标记化(Lexer)

我是这样实现的。

```
public class Lexer {
  public String str;
  public Integer index;

  public List<String> reserved = new List<String>{
    'if',
    'else',
    'for',
    'while',
    'true',
    'false'
  };

  public Lexer(String str) {
    this.str = str;
    this.index = 0;
  }

  public List<Token> parse() {
    List<Token> tokens = new List<Token>();
    String current = this.current();
    while (true){
      Token token;
      switch on current {
        when '+', '-', '*', '/', '=', '\n', '(', ')', '{', '}', '!', ';', '>', '<' {
          if (current == '=' || current == '!' || current == '<' || current == '>') {
            if (this.peek() == '=') {
              String type = current + this.peek();
              token = new Token(type, type);
              this.next();
            }
          }
          if (token == null) {
            token = new Token(current, current);
          }
        }
        when else {
          if (Pattern.matches('[0-9]', current)) {
            token = this.parseInt();
          } else if (Pattern.matches('[a-zA-Z]', current)) {
            token = this.parseIdent();
          }
        }
      }
      if (token != null) {
        tokens.add(token);
      }
      if (this.index == this.str.length() - 1) {
        break;
      }
      current = this.next();
    }
    return tokens;
  }

  public String peek() {
    return this.str.substring(this.index+1, this.index+2);
  }

  public String current() {
    return this.str.substring(this.index, this.index+1);
  }

  public String next() {
    this.index++;
    return this.current();
  }
  // ... 
```

它逐个检查字符并生成令牌数组。

## 解析并生成 AST

解析器从 Lexer 创建的标记中生成 AST。
逐个检查令牌，生成语句节点数组。

```
public class Parser {
  public List<Token> tokens;
  public Integer index;

  public Parser(List<Token> tokens) {
    this.tokens = tokens;
    this.index = 0;
  }

  public List<Node> parse() {
    return this.statements();
  }

  public List<Node> statements() {
    List<Node> statements = new List<Node>();
    while (true){
      Node stmt = this.statement();
      if (stmt == null) {
        break;
      }
      statements.add(stmt);
      this.consume('\n');
    }
    return statements;
  }

  public Node statement() {
    if (this.current('if') != null) {
      return this.ifStatement();
    }
    if (this.current('while') != null) {
      return this.whileStatement();
    }
    if (this.current('for') != null) {
      return this.forStatement();
    }
    if (this.peek('=') != null) {
      return this.assign();
    }
    return this.call();
  }

  public Node assign() {
    Token ident = this.consume('Ident');
    if (ident == null) {
      return null;
    }
    Token op = this.consume('=');
    if (op == null) {
      return null;
    }
    Node exp = this.add();
    return new AssignNode(ident.value, exp);
  }

  public Node add() {
    Node exp = this.mul();
    Token op = this.consume('+');
    if (op != null) {
      return new BinaryOperatorNode('+', exp, this.add());
    }
    op = consume('-');
    if (op != null) {
      return new BinaryOperatorNode('-', exp, this.add());
    }
    return exp;
  }

  public Node mul() {
    Node exp = this.term();
    Token op = this.consume('*');
    if (op != null) {
      return new BinaryOperatorNode('*', exp, this.mul());
    }
    op = this.consume('/');
    if (op != null) {
      return new BinaryOperatorNode('/', exp, this.mul());
    }
    return exp;
  }

  public Node term() {
    Token exp = this.consume('Integer');
    if (exp != null) {
      return new IntegerNode(exp.value);
    }
    exp = this.consume('Ident');
    if (exp != null) {
      return new IdentifierNode(exp.value);
    }
    return null;
  }

  public Token consume(String type) {
    Token currentToken = this.current();
    if (currentToken == null) {
      return null;
    }
    if (currentToken.type == type) {
      this.index++;
      return currentToken;
    }
    return null;
  }

  public Token peek() {
    if (this.tokens.size() <= this.index+1) {
      return null;
    }
    return this.tokens[this.index+1];
  }

  public Token current() {
    if (this.tokens.size() == this.index) {
      return null;
    }
    return this.tokens[this.index];
  }
// ... 
```

## 按访客模式生成代码

每个节点实现 accept 方法。

```
public class BinaryOperatorNode implements Node {
  public String type;
  public Node left;
  public Node right;

  public BinaryOperatorNode(String type, Node left, Node right) {
    this.type = type;
    this.left = left;
    this.right = right;
  }

  public Value accept(Visitor v) {
    return v.visitBinaryOperator(this);
  }
} 
```

Visitor 实现 visit `{NodeName}`方法。

```
public class Visitor {
  public Map<String, Value> variables;
  private String buffer;

  public Visitor() {
    this.variables = new Map<String, Value>();
  }

  public void run(String str) {
    this.buffer = '';
    List<Token> tokens = new Lexer(str).parse();
    List<Node> nodes = new Parser(tokens).parse();
    for (Node node : nodes) {
      this.visit(node);
    }
  }

  public String getBuffer() {
    return this.buffer;
  }

  public void visit(Node node) {
    Value result = node.accept(this);
  }

  public Value visitInteger(IntegerNode node) {
    return new Value(node.value, node);
  }

  public Value visitString(StringNode node) {
    return new Value(node.value, node);
  }
// ... 
```

最后，它可以通过 Apex 执行迷你语言。

```
String code = '';
code += 'a = "hello"\n';
code += 'puts a + " apex!"';
Visitor v = new Visitor();
v.run(code);
System.debug(v.getBuffer()); 
```

使用`#getBuffer`方法，Apex 可以接收`puts`输出的数据。

## 考虑

如果您想用动态语言编写 Salesforce 应用程序，上述方法可能会有用。你可以存储程序代码到 SObject 记录并执行它。
所以你不需要通过迷你语言部署任何改变。

你可以设置 DSL 的迷你语言。
例如，自定义字段或其他格式化数据(JSON、YAML、XML...)没有逻辑只有价值。
如果给 Apex 写逻辑，需要部署。公式数据类型的表达能力不如 Apex。
在这一点上，像 DSL 这样的迷你语言比公式数据类型更有表现力，无需部署，易于编写。