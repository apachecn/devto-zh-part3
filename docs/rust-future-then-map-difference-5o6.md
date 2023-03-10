# 信任未来，然后映射差异

> 原文：<https://dev.to/x1957/rust-future-then-map-difference-5o6>

I've always been curious about the difference between and_then, map. I feel like I input a fn and then return a Future.

Look at the document and you'll find it.

[然后 _ 然后](https://docs.rs/futures/0.1.8/futures/future/trait.Future.html#method.and_then)

```
fn and_then<F, B>(self, f: F) -> AndThen<Self, B, F> 
where F: FnOnce(Self::Item) -> B,
        B: IntoFuture<Error=Self::Error>,
        Self: Sized
[−]
Execute another future after this one has resolved successfully.

This function can be used to chain two futures together and ensure that the final future isn't resolved until both have finished. The closure provided is yielded the successful result of this future and returns another value which can be converted into a future.

Note that because Result implements the IntoFuture trait this method can also be useful for chaining fallible and serial computations onto the end of one future.

If this future is dropped, panics, or completes with an error then the provided closure f is never called.

Note that this function consumes the receiving future and returns a wrapped version of it.

Examples
use futures::future::*;

let future_of_1 = ok::<u32, u32>(1);
let future_of_4 = future_of_1.and_then(|x| {
    Ok(x + 3)
});

let future_of_err_1 = err::<u32, u32>(1);
future_of_err_1.and_then(|_| -> FutureResult<u32, u32> {
    panic!("should not be called in case of an error");
}); 
```

[地图](https://docs.rs/futures/0.1.8/futures/future/trait.Future.html#method.map)

```
fn map<F, U>(self, f: F) -> Map<Self, F> 
where F: FnOnce(Self::Item) -> U, Self: Sized
[−]
Map this future's result to a different type, returning a new future of the resulting type.

This function is similar to the Option::map or Iterator::map where it will change the type of the underlying future. This is useful to chain along a computation once a future has been resolved.

The closure provided will only be called if this future is resolved successfully. If this future returns an error, panics, or is dropped, then the closure provided will never be invoked.

Note that this function consumes the receiving future and returns a wrapped version of it, similar to the existing map methods in the standard library.

Examples
use futures::future::*;

let future_of_1 = ok::<u32, u32>(1);
let future_of_4 = future_of_1.map(|x| x + 3); 
```

You can see that the future returned by f of and_then should be an unresolved one, and the map returned a solved one.

Look at the code

```
#[derive(Debug)]
#[must_use = "futures do nothing unless polled"]
pub struct AndThen<A, B, F> where A: Future, B: IntoFuture {
    state: Chain<A, B::Future, F>,
}

pub fn new<A, B, F>(future: A, f: F) -> AndThen<A, B, F>
    where A: Future,
          B: IntoFuture,
{
    AndThen {
        state: Chain::new(future, f),
    }
}

impl<A, B, F> Future for AndThen<A, B, F>
    where A: Future,
          B: IntoFuture<Error=A::Error>,
          F: FnOnce(A::Item) -> B,
{
    type Item = B::Item;
    type Error = B::Error;

    fn poll(&mut self) -> Poll<B::Item, B::Error> {
        self.state.poll(|result, f| {
            result.map(|e| {
                Err(f(e).into_future())
            })
        })
    }
} 
```

```
#[derive(Debug)]
#[must_use = "futures do nothing unless polled"]
pub struct Map<A, F> where A: Future {
    future: A,
    f: Option<F>,
}

pub fn new<A, F>(future: A, f: F) -> Map<A, F>
    where A: Future,
{
    Map {
        future: future,
        f: Some(f),
    }
}

impl<U, A, F> Future for Map<A, F>
    where A: Future,
          F: FnOnce(A::Item) -> U,
{
    type Item = U;
    type Error = A::Error;

    fn poll(&mut self) -> Poll<U, A::Error> {
        let e = match self.future.poll() {
            Ok(Async::NotReady) => return Ok(Async::NotReady),
            Ok(Async::Ready(e)) => Ok(e),
            Err(e) => Err(e),
        };
        e.map(self.f.take().expect("cannot poll Map twice"))
         .map(Async::Ready)
    }
} 
```

The implementation of map is relatively simple. After future ready, the return value is processed with F, and the new Async::Ready wrapped Result is returned. However, and_then is a little more complicated, with an extra chain. Let's take a look at the chain code

```
#[derive(Debug)]
pub enum Chain<A, B, C> where A: Future {
    First(A, C),
    Second(B),
    Done,
}

impl<A, B, C> Chain<A, B, C>
    where A: Future,
          B: Future,
{
    pub fn new(a: A, c: C) -> Chain<A, B, C> {
        Chain::First(a, c)
    }

    pub fn poll<F>(&mut self, f: F) -> Poll<B::Item, B::Error>
        where F: FnOnce(Result<A::Item, A::Error>, C)
                        -> Result<Result<B::Item, B>, B::Error>,
    {
        let a_result = match *self {
            Chain::First(ref mut a, _) => {
                match a.poll() {
                    Ok(Async::NotReady) => return Ok(Async::NotReady),
                    Ok(Async::Ready(t)) => Ok(t),
                    Err(e) => Err(e),
                }
            }
            Chain::Second(ref mut b) => return b.poll(),
            Chain::Done => panic!("cannot poll a chained future twice"),
        };
        let data = match mem::replace(self, Chain::Done) {
            Chain::First(_, c) => c,
            _ => panic!(),
        };
        match f(a_result, data)? {
            Ok(e) => Ok(Async::Ready(e)),
            Err(mut b) => {
                let ret = b.poll();
                *self = Chain::Second(b);
                ret
            }
        }
    }
} 
```

It can be seen that after the execution of a.poll, get the previous fn, and then execute f(a_result, data), where F is the function
in the previous and_then.

```
|result, f| {
    result.map(|e| {
        Err(f(e).into_future())
}) 
```

So the match in chain must be an Err here. Here, B is the Future generated after f(e), and the state of Chain is changed here. Chain::Second(b). If b.poll () returns Ready here, it's over. If it's NotReady, then match *self in the next poll, which is B, so just poll B.