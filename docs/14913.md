# 需要帮助整合反应条纹；获得 404

> 原文：<https://dev.to/christycakes/need-help-integrating-stripe-in-react-getting-404-1ng7>

我正在学习如何将 Stripe 集成到 React & Express 中。我得到一个 404 错误。另外，当我尝试 console . log req . body 时，什么也没有出现。

点击付款按钮，期待“购买完成”，得到“购买错误”(如下)

```
async submit(ev) {
ev.preventDefault();
let { token } = await this.props.stripe.createToken();
let response = await fetch("http://127.0.0.1:3000/api/charge", {
    method: "POST",
    headers: { "Content-Type": "text/plain" },
    body: token.id
});

if (response.ok) {
    console.log("Purchase Complete")
    this.setState({ complete: true })
} else {
    console.log("Purchase Error")
    console.log(response)
}
} 
```

路由器:

```
 router.post("/charge", async (req, res) => {

      console.log("req.body: ", req.body)

  try {
let { status } = await stripe.charges.create({
  amount: 14,
  currency: "usd",
  description: "Purchase 1,000 followers",
  source: req.body
});
res.json({ status });
 } catch (err) {
   res.status(500).end();
 }
}); 
```

期望在控制台中看到 req.body(来自上面的代码),但什么也没有得到。我尝试过在 server/src/index.js 中添加 bodyParser 的形式(如下)。

```
app.use(bodyParser.text());
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true })); 
```