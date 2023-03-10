# 使用 passport.js 的节点认证——第 1 部分

> 原文：<https://dev.to/ganeshmani/node-authentication-using-passport-js-part-1-53k7>

web 应用程序的身份验证很难手动设置。为了方便起见，passport 开始行动了。

### 什么是护照？

passport 是一个用于节点应用的认证中间件。passport 使用用户名和密码、脸书和 Twitter 提供了不同的策略。

为了更好地理解 passport 身份验证概念，本文分为两个部分

我们可以使用不同的方式登录网络应用，如脸书、推特、谷歌和本地(用户名和密码)

在本文中，我们将对 web 应用程序和文件夹结构使用本地策略，如下所示:

#### 项目结构:

[![](img/a39eb774e9ba4de19cd31e66e8a307da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lKHd_be3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/04/Screen-Shot-2019-04-19-at-12.25.37-PM.png)

首先，我们将安装应用程序所需的所有依赖项。因此，我们安装应用程序所需的包

```
"dependencies": {
    "bcrypt-nodejs": "0.0.3",
    "body-parser": "^1.18.3",
    "connect-flash": "^0.1.1",
    "cookie-parser": "^1.4.4",
    "cors": "^2.8.5",
    "express": "^4.16.4",
    "express-handlebars": "^3.0.2",
    "express-session": "^1.16.1",
    "method-override": "^3.0.0",
    "mongoose": "^5.5.2",
    "morgan": "^1.9.1",
    "passport": "^0.4.0",
    "passport-local": "^1.0.0"
  },
```

最重要的是， **passport** 和 **passport-local** 是用于护照认证的包。

另一方面，我们需要创建**模型**，它对用户来说只是模式。创建名为 **user.schema.js** 的文件

```
const mongoose = require('mongoose');
const  bcrypt   = require('bcrypt-nodejs');
const Schema = mongoose.Schema;

let userschema = new Schema({
    email : String,
    password : String,
});

userschema.methods.generateHash = function(password) {
    return bcrypt.hashSync(password, bcrypt.genSaltSync(8), null);
};

// checking if password is valid
userschema.methods.validPassword = function(password) {
    return bcrypt.compareSync(password, this.password);
};

let User = mongoose.model('User',userschema);

module.exports = User;
```

我们需要创建一个名为 **passport** 的文件，在这里我们需要设置 passport 策略。也就是说，创建一个名为 **config/passport.js 的文件。**

首先，我们将了解如何使用 passport 身份验证来设置注册。因此，在 **config/passport.js** 中添加以下代码

```
const LocalStrategy = require('passport-local').Strategy;

let User = require('../models/user.schema');

module.exports = function(passport){

    // used to serialize the user for the session
    passport.serializeUser(function(user, done) {
        done(null, user.id);
    });

    // used to deserialize the user
    passport.deserializeUser(function(id, done) {
        User.findById(id, function(err, user) {
            done(err, user);
        });
    });

    passport.use('local-signup', new LocalStrategy({
        // by default, local strategy uses username and password, we will override with email
        usernameField : 'email',
        passwordField : 'password',
        passReqToCallback : true // allows us to pass back the entire request to the callback
    },
    function(req, email, password, done) {

        // asynchronous
        // User.findOne wont fire unless data is sent back
        process.nextTick(function() {

        // find a user whose email is the same as the forms email
        // we are checking to see if the user trying to login already exists
        User.findOne({ 'email' :  email }, function(err, user) {
            // if there are any errors, return the error
            if (err)
                return done(err);

            // check to see if theres already a user with that email
            if (user) {
                return done(null, false, req.flash('signupMessage', 'That email is already taken.'));
            } else {

                // if there is no user with that email
                // create the user
                var newUser            = new User();

                // set the user's local credentials

                newUser.email    = email;
                newUser.password = newUser.generateHash(password);

                // save the user
                newUser.save(function(err) {
                    if (err)
                        throw err;
                    return done(null, newUser);
                });
            }

        });    

        });

    }));
}
```

在上面的文件中，我们为注册设置了 passport 本地策略，并将用户插入数据库。

之后，我们需要创建一个 **route** 文件来处理注册 url。所以，创建一个名为 **routes/index.js** 的文件

```
 module.exports = function(app,passport){

    app.get('/',isLoggedIn,(req,res)=>{
        console.log("req user",req.user);
        res.render('home',{
            user : req.user
        });
    });

    app.get('/signup',(req,res) => {
        res.render('signup');
    })

    app.post('/signup', passport.authenticate('local-signup', {
        successRedirect : '/', // redirect to the secure profile section
        failureRedirect : '/signup', // redirect back to the signup page if there is an error
        failureFlash : true // allow flash messages
    }));

    app.get('/logout', function(req, res) {
        req.logout();
        res.redirect('/');
    });

        // route middleware to make sure a user is logged in
    function isLoggedIn(req, res, next) {

        // if user is authenticated in the session, carry on 
        if (req.isAuthenticated())
            return next();

        // if they aren't redirect them to the home page
        res.redirect('/login');
    }
}
```

到目前为止，我们添加了注册的路径，在成功注册后，我们将它们重定向到主页。因此，我们需要为注册和主页创建视图文件。

创建一个名为**视图**的文件夹，并添加以下文件

```
 - views
       - layouts
        ------ main.handlebars  <!-- show our home page with 
    ------ home.handlebars    <!-- show our home -->
    ------ login.handlebars   <!-- show our login form -->
```

也就是说，**汉王**应该长得像

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    Worker App
    <link rel="icon" hrefimg/favicon.png"/>
    <link  rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css"/>
</head>
<body>

    {body}

</body>
</html>
```

**把手**应该是什么样子

```
 {#if message }
        <div class="ui negative message transition hidden">
            <i class="close icon"></i>
            <div class="header">
                {message}
            </div>
        </div>
    {/if}
    <div class="ui middle aligned center aligned grid container">
        <div class="column">
            <h2 class="ui teal image header">
                <img srcimg/favicon.png" class="image"/>
                <div class="content">
                    Cloudnweb.dev
                </div>
            </h2>
            <form action="/signup" method="POST" class="ui large form">
                <div class="ui stacked segment">

                    <div class="field">
                        <div class="ui left icon input">
                            <i class="user icon"></i>
                            <input type="text" name="email" placeholder="Enter Email Address"/>
                        </div>
                    </div>
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="lock icon"></i>
                            <input type="password" name="password" placeholder="Enter Password"/>
                        </div>
                    </div>
                    <input type="submit" class="ui fluid large teal submit button" value="Sign Up">                </div>
            </form>

        </div>
    </div>
```

**车把**应该是什么样子

```
<div class="ui small menu">
  <a class="active item">
    Home
  </a>
  <div class="right menu">
    <div class="item">
      <h4>{user.email}</h4>
    </div>
    <div class="item">
        <a href="/logout" class="ui primary button">Log Out</a>
    </div>
  </div>
</div>
```

最后，在 **app.js** 中添加以下代码

```
const express = require('express');
const exphbs = require('express-handlebars');
const mongoose = require('mongoose');
const app = express();
const passport = require('passport');
const flash = require('connect-flash');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const bodyParser = require('body-parser');
const session = require('express-session');

require('dotenv').config();

app.engine('handlebars',exphbs({defaultLayout : 'main'}));
app.set('view engine','handlebars');

app.use(morgan('dev'));
app.use(cookieParser());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json())

const MONGODB_URI = process.env.MONGODB_URL;

mongoose.connect(MONGODB_URI,{ useNewUrlParser : true });

var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function() {
    console.log('connected');
});

app.use(session({secret : 'ilearnnodejs'}));
app.use(passport.initialize());
app.use(passport.session());
app.use(flash());

require('./config/passport')(passport);
require('./routes/index')(app,passport);

const PORT = process.env.PORT;

app.listen(PORT,() => {
    console.log(`app is listening to port ${PORT}`);
})
```

现在，我们可以在命令中以 **node app.js** 的形式运行应用程序

我们将在第 2 部分看到如何使用 passport 认证登录:[https://cloud nweb . dev/2019/04/node-authentic ation-using-passport-js-part-2/](https://cloudnweb.dev/2019/04/node-authentication-using-passport-js-part-2/)