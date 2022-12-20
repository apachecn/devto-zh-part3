# 推荐:匿名函数，编程灵药 1.6 —第 5 章

> 原文：<https://dev.to/chenge/recommend-anonymous-functions-programming-elixir-16chapter-5--2o0m>

今天我读了这个关于长生不老药系列的好帖子。分享一下。

```
 # FizzBuzz - Write a program that prints the numbers from 1 to 100\. But for multiples of 3, print "Fizz" instead of the number and for the multiples of 5, print "Buzz". For numbers which are multiples of both 3 and 5, print "FizzBuzz".

# First, the JavaScript implementation

const fizzBuzz = (num) => {
   if (num % 3 === 0 && num % 5 === 0) return "FizzBuzz";
   if (num % 3 === 0) return "Fizz";
   if (num % 5 === 0) return "Buzz";
   return num;
};

const range = (num) => [ ...Array(num).keys() ].map(num => num + 1); # This helper function simply creates an array of numbers - i.e range(100) == [1, 2, 3, ..., 100]

console.log(range(100).map(fizzBuzz));

# Next, the Elixir implementation

fizz_buzz_result = fn 
  (0, 0, _) -> "FizzBuzz"
  (0, _, _) -> "Fizz"
  (_, 0, _) -> "Buzz"
  (_, _, num) -> num
end

fizz_buzz = fn (num) -> fizz_buzz_result.(rem(num, 3), rem(num, 5), num) end

IO.puts(Enum.map(1..100, fizz_buzz)) 

# I think one of the most interesting aspects of the Elixir implementation is that there is NO conditional logic involved. 
# Instead of using `if / else` logic, Elixir takes advantage of pattern matching and multiple clauses to produce the same effect! 
```

[链接- >](https://medium.com/@fay_jai/programming-elixir-1-6-chapter-5-summary-6a7b5d88cf07)

```
Chapter 1 and 2 — OOP, Functional Programming, and Pattern Matching
Chapter 3 — Immutability
Chapter 4 — Basic Elixir Types
Chapter 5 — Anonymous Functions (this post)
Chapter 6 — Modules and Named Functions (coming soon)
Chapter 7 — Lists and Recursion (coming soon)
Chapter 8 and 9 — Maps, Structs, and Keyword Lists in Detail (coming soon)
Chapter 10 — Processing Collections (coming soon)
Chapter 11 — Strings and Binaries (coming soon)
Chapter 12 — Control Flow (coming soon) 
```