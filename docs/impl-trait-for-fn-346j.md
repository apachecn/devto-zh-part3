# Fn 的 impl 特征

> 原文：<https://dev.to/x1957/impl-trait-for-fn-346j>

When using ws-rs, watch demo:

```
connect(HUOBI, |out| {
        out.send(r#"{"event":"addChannel","channel":"ltcbtc_depth"}"#);

        move |msg| {
            println!("Client got message '{}'. ", msg);
            out.close(CloseCode::Normal)
        }
    }); 
```

After returning a closure, I thought it was some strange syntax at first. msg got what the server sent, which is amazing!

Look at the connect code:

```
pub fn connect<U, F, H>(url: U, factory: F) -> Result<()>
where
    U: Borrow<str>,
    F: FnMut(Sender) -> H,
    H: Handler,
{
    let mut ws = WebSocket::new(factory)?;
    let parsed = url::Url::parse(url.borrow()).map_err(|err| {
        Error::new(
            ErrorKind::Internal,
            format!("Unable to parse {} as url due to {:?}", url.borrow(), err),
        )
    })?;
    ws.connect(parsed)?;
    ws.run()?;
    Ok(())
} 
```

We should have entered a factory, and then created ws through the factory.
Let's look at the factory definition

```
/// A trait for creating new WebSocket handlers.
pub trait Factory {
    type Handler: Handler;

    fn connection_made(&mut self, _: Sender) -> Self::Handler;

    #[inline]
    fn on_shutdown(&mut self) {
        debug!("Factory received WebSocket shutdown request.");
    }

    #[inline]
    fn client_connected(&mut self, ws: Sender) -> Self::Handler {
        self.connection_made(ws)
    }

    #[inline]
    fn server_connected(&mut self, ws: Sender) -> Self::Handler {
        self.connection_made(ws)
    }

    #[inline]
    fn connection_lost(&mut self, _: Self::Handler) {}
} 
```

But what we typed is a closure? ? ?

Let's look at the limit of our input function where

```
F: FnMut(Sender) -> H
H: Handler 
```

The input is a Sender, and the return is a Handler, so the out in our demo is a Sender, and the returned

```
move |msg| {
            println!("Client got message '{}'. ", msg);
            out.close(CloseCode::Normal)
        } 
```

是个处理程序。

Continue to ask: My input is a function, how can it conform to Factory?

看看工厂特征里面

```
impl<F, H> Factory for F
where
    H: Handler,
    F: FnMut(Sender) -> H,
{
    type Handler = H;

    fn connection_made(&mut self, out: Sender) -> H {
        self(out)
    }
} 
```

He implemented Factory trait for FnMut(Sender) -> H, and connection_made is the only one in Factory trait that has no default implementation, so when connection is created, call our function, give him out, and return a Handler.

OK, there's only one problem here. What we returned is a closuzre, and the same is true. . . Handler trait is also implemented for this closuzre. Look at the code

```
impl<F> Handler for F
where
    F: Fn(Message) -> Result<()>,
{
    fn on_message(&mut self, msg: Message) -> Result<()> {
        self(msg)
    }
} 
```

Same as above, on_message is the only one in Handler that has no default implementation, so this one here is equal to

```
 move |msg| {
            println!("Client got message '{}'. ", msg);
            out.close(CloseCode::Normal)
        } 
```