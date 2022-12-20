# 为 Rust 开发了一个测试框架(尝试使用它)

> 原文：<https://dev.to/nasa/developed-a-test-framework-for-rust-try-use-it-3kcj>

Hi. I'm nasa.

我用 Rust 做了一个测试框架 Ruspec。请使用它。

[https://github.com/k-nasa/ruspec](https://github.com/k-nasa/ruspec)

## 示例语法

这是功能宏。它扩展为具有测试属性代码。

```
use ruspec::ruspec;

ruspec! {
    describe "test module name" {
        before { let context = 5; }
        subject { context + 5 }

        it "test name" {
            assert_eq!(subject, 10);
        }
    }

    describe "test module 2" {
        before { let context = 5; }
        it "test name" {
            assert_eq!(context, 5);
        }

        context "context is 6" {
            before { let context = 6; }
            it "should equal 6" {
                assert_eq!(context, 6);
            }
        }
    }
} 
```

# 展开

```
mod test_module_name {
    #[test]
    fn test_name() {
        let context = 5;

        assert_eq(context + 5, 10)
    }
}

mod test_module_2 {
    #[test]
    fn test_name() {
        let context = 5;

        assert_eq(context, 10)
    }

    mod context_is_6 {
        #[test]
        fn should_equal_6() {
            let context = 6;
            assert_eq!(context, 6)
        }
    }
} 
```

## 如何使用

添加您的货物. toml

```
ruspec = "0.1.1" 
```

进口 ruspec！

```
use ruspec::ruspec; 
```