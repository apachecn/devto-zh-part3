# Rust 编程语言中的错误处理

> 原文：<https://dev.to/saiumesh/error-handling-in-rust-programming-language-3g4>

[![Alt text of image](img/6057e7d1956092186b90d852be69c090.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r8TIqKK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2Ay1BM50iNmvX1bcBLq5d5DA.jpeg)

如果你在关注栈溢出开发者调查，你可能已经看到 Rust 连续两年成为最受欢迎的编程语言。Rust 可能是我见过最迷人编程语言，我完全同意这个调查。

说到这里，学习 Rust 并不容易，学习曲线非常高，如果你像我一样来自 JavaScript 等语言，这将是一个非常具有挑战性的旅程，但这条艰难的道路是值得的。

如果你是学习 Rust 的新手，我强烈建议从他们的[文档](https://doc.rust-lang.org/book/)开始，Rust 有最好的文档之一。

今天我们将学习 rust 中的[错误处理](https://doc.rust-lang.org/1.30.0/book/second-edition/ch09-00-error-handling.html)。

如果你来自像 JavaScript 这样的语言，JS 和 Rust 处理错误的方式是截然不同的。说得够多了，让我们从一些代码开始😋😋。

在编码任何东西之前，我们需要学习[选项](https://doc.rust-lang.org/std/option/index.html)和[导致](https://doc.rust-lang.org/std/result/)生锈。

**Rust 中的选项**代表可选值:每个选项要么是某个且包含值，要么是无，且不包含值。选项类型在 Rust 代码中非常常见，因为它们有多种用途。

Rust 中的**结果**是用于返回和传播错误的类型。它是一个带有变量的枚举，Ok(T)表示成功并包含一个值，Err(E)表示错误并包含一个错误值。

```
enum Result<T, E> {

 Ok(T),

 Err(E),

} 
```

现在让我们看一些例子。

首先我们将学习**选项**

```
fn main() {
    // first let's define vector/array of numbers
    let numbers = vec![1, 2, 4];

    // now let's try to read value from vector
    let index_one = numbers[1];
    println!("value at index {}", index_one);

    // now let's try to read 10th index which doesn't exists
    // since Rust doesn't have neither null nor try/catch to handle error
    // so we will take leverage of **Option**

    // un comment below two line to check error 😢😢

    // let out_of_index = numbers[10];
    // println!("this will throw error {}", out_of_index);

    // above code would result in following error
    // thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 10'

    // rust has very interesting way to handle these errors
    // we will take advantage of Option and get method from
    // vector to handle these kind of errors

    // get method of vector returns **Option** enum
    // Option will have two properties Some and None
    // let's see how to read/catch error
    match numbers.get(10) {
        Some(value) => println!("Hello {}", value),
        None => println!("Yo!! out of bound error")
    }

    // now let's see how to read value from it
    let result = match numbers.get(10) {
        Some(value) => value,
        None => &-1 // defaulting to -1
    };

    println!("result is {}", result);

    // as you can see above code works well but,
    // we can improve code by taking advantage of Result
} 
```

**现在，让我们稍微改进一下上面的代码**

```
fn read_from_vec(input: &Vec<i32>, index: usize) -> Result<i32, &'static str> {
    return match input.get(index) {
        Some(value) => Ok(*value),
        None => Err("Out of bound exception")
    }
}

fn main() {
    // now lets take same example and improve error handling
    let numbers = vec![1, 2, 4];
    let result = read_from_vec(&numbers, 2);

    // now since result is of type **Result** we can handle this in multiple ways
    if result.is_ok() {
        println!("got the value {}", result.unwrap());
    } else {
        println!("yo!! got the error {:?}", result.unwrap_err());
    }

    // above code works but there's an even better way to handle the same
    match result {
        Ok(data) => println!("indexed value {}", data),
        Err(err) => println!("error is  {}", err)
    } 
```

现在让我们看一个读取文件的一般用例。

```
use std::io;
use std::io::Read;
use std::fs::File;

fn read_from_file(file_path: &'static str) -> Result<String, io::Error> {
    let mut file_data = String::new();

    // now let's try to read file
    // here ? is for handling errors
    // if there's an error it will break program and returns error
        // let mut f = File::open(file_path)?;
        // f.read_to_string(&mut file_data)?;
        // return Ok(file_data);

    // we can simplify above code by writing
    File::open(file_path)?.read_to_string(&mut file_data)?;
    Ok(file_data)

}

fn main() {
    // now let's see more generic use cases
    // for error handling

    let file_path = "hello.txt";
    match read_from_file(file_path) {
        Ok(data) => println!("file data {}", data),
        Err(err) => println!("err {}", err)
    };
} 
```

我仍在学习 Rust，希望我对 Rust 语言中的错误处理有所了解。如果您有任何疑问或建议，可以在我的 twitter 上联系我。在那之前，我会在其他岗位上看到你。和平❤❤