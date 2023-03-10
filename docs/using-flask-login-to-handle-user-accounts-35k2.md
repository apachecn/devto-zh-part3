# 使用 Flask-Login 处理用户帐户

> 原文：<https://dev.to/hackersandslackers/using-flask-login-to-handle-user-accounts-35k2>

[![Using Flask-Login to Handle User Accounts](img/92a5a221567672ed209aaf01db85a49e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uJ-eRu79--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers.com/conteimg/2019/04/flasklogin.jpg)

到目前为止，我们已经在这个系列中讨论了很多烧瓶的优点。我们完全了解如何构建一个合理的应用程序；我们可以提供 [**复杂的页面模板**](https://hackersandslackers.com/powerful-page-templates-in-flask-with-jinja/) ，并使用 Flask-SQLAlchemy 一头扎进 [**与数据库交互。对于我们的下一个挑战，我们将需要*到目前为止我们已经获得的所有*知识，以及更多更多的知识。欢迎来到烧瓶开发的超级碗。这个。是。烧瓶-登录。**](https://dev.to/hackersandslackers/manage-database-models-with-flask-sqlalchemy-22n8)

[**Flask-Login**](https://flask-login.readthedocs.io/en/latest/) 是一个 dope 库，处理用户管理的所有方面，包括你可能想不到的重要细节。一些值得注意的功能包括在登录墙后保护我们的应用程序的一部分，加密密码和处理会话。此外，它与我们已经熟悉的其他 Flask 库配合得很好: [**Flask-SQLAlchemy**](https://dev.to/hackersandslackers/manage-database-models-with-flask-sqlalchemy-22n8) 用于创建和获取帐户，以及 [**Flask-WTForms**](https://hackersandslackers.com/guide-to-building-forms-in-flask/) 用于处理智能注册&登录表单。本教程假设你对这些东西有一些工作知识。

在最初的学习曲线之后，Flask-Login 非常容易使用...但是这里面有一个问题。也许我不是唯一注意到这一点的人，但是大多数与 Flask 相关的文档都很糟糕。社区充斥着无助的过时信息；如果你曾经在教程中遇到过`flask.ext`，它对任何在 2019 年开发的人来说都是毫无价值的。更糟糕的是，官方的 Flask-Login 文档包含了一些完全错误的工件。该文档自相矛盾(我将向您展示我的意思)，并且几乎没有提供任何代码示例。我唯一的希望是，我可以拯救那些我自己经历过的无尽的头痛。

## **构建我们的应用**

让我们从安装依赖项开始。这应该会让你对自己的处境有所了解:

```
$ pip3 install flask flask-login flask-sqlalchemy psycopg2-binary python-dotenv 
```

太好了。让我们一步一步来，从我们的项目文件结构开始:

```
flasklogin-tutorial
├── /login_tutorial
│ ├── __init__.py
│ ├── auth.py
│ ├── forms.py
│ ├── models.py
│ ├── routes.py
│ ├── /static
│ │ ├── /dist
│ │ │ ├── /css
│ │ │ │ ├── account.css
│ │ │ │ └── dashboard.css
│ │ │ └── /js
│ │ │ └── main.min.js
│ │ └── /src
│ │ ├── /js
│ │ │ └── main.js
│ │ └── /less
│ │ ├── account.less
│ │ ├── dashboard.less
│ │ └── vars.less
│ └── /templates
│ ├── dashboard.html
│ ├── layout.html
│ ├── login.html
│ ├── meta.html
│ ├── scripts.html
│ └── signup.html
├── config.py
├── requirements.txt
├── setup.py
├── start.sh
└── wsgi.py 
```

当然，除非我也透露我的 **config.py** ，否则我就不是一个绅士:

```
from os import environ

class Config:
    """Set Flask configuration vars from .env file."""

    # General Config
    SECRET_KEY = environ.get('SECRET_KEY')
    FLASK_APP = environ.get('FLASK_APP')
    FLASK_ENV = environ.get('FLASK_ENV')
    FLASK_DEBUG = environ.get('FLASK_DEBUG')

    # Database
    SQLALCHEMY_DATABASE_URI = os.environ.get('SQLALCHEMY_DATABASE_URI')
    SQLALCHEMY_TRACK_MODIFICATIONS = os.environ.get('SQLALCHEMY_TRACK_MODIFICATIONS') 
```

配置值保存在一个`.env`文件中，这是我非常鼓励的做法。在这些配置变量中， **SECRET_KEY** 是我们应该关注的地方。SECRET_KEY 相当于用来保护我们 app 的密码；它应该尽可能的长，无意义，并且不可记忆。说真的:泄露你的密钥就相当于在午夜后喂小鬼。

## 初始化烧瓶-登录

使用一个标准的“应用工厂”应用程序，设置 Flask-Login 与其他 Flask 插件(或者现在叫它们什么)没有什么不同。这使得设置变得容易；我们所需要做的就是确保 **Flask-Login** 和我们的其他插件一起在`__init__.py`中初始化:

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager

db = SQLAlchemy()
login_manager = LoginManager()

def create_app():
    """Construct the core application."""
    app = Flask( __name__ , instance_relative_config=False)

    # Application Configuration
    app.config.from_object('config.Config')

    # Initialize Plugins
    db.init_app(app)
    login_manager.init_app(app)

    with app.app_context():
        # Import parts of our application
        from . import routes
        from . import login
        app.register_blueprint(routes.main_bp)
        app.register_blueprint(login.login_bp)

        # Initialize Global db
        db.create_all()

        return app 
```

在上面的例子中，我们使用最少数量的插件来使登录工作: **Flask-SQLAlchemy** 和 **Flask-Login** 。

为了保持理智，我们将把登录路径与主要的应用程序路径和逻辑分开。这就是为什么我们注册了一个名为 **auth_bp** 的蓝图，它是从名为`auth.py`的文件中导入的。我们的“主”应用程序(也就是没有登录的任何东西)将会驻留在`routes.py`中，在一个名为 **main_bp** 的蓝图中。我们一会儿将回到这些

## 创建用户模型

我们将在`models.py`中保存我们的**用户**模型。当创建与 Flask-Login 兼容的模型时，需要记住一些事情——最重要的是利用来自`flask_login`库的`UserMixin`。当我们从 **UserMixin、**继承我们的类时，我们的模型立即扩展到包含 Flask-Login 工作所需的所有方法。这是迄今为止创建用户模型最简单的方法。我就不赘述这些类是做什么的了，因为如果你只是简单地以`class User(UserMixin, db.Model):`开始你的课程，你真的不需要理解它:

```
from . import db
from flask_login import UserMixin
from werkzeug.security import generate_password_hash, check_password_hash

class User(UserMixin, db.Model):
    """Model for user accounts."""

    __tablename__ = 'flasklogin-users'

    id = db.Column(db.Integer,
                   primary_key=True)
    name = db.Column(db.String,
                     nullable=False,
                     unique=False)
    email = db.Column(db.String(40),
                      unique=True,
                      nullable=False)
    password = db.Column(db.String(200),
                         primary_key=False,
                         unique=False,
                         nullable=False)
    website = db.Column(db.String(60),
                        index=False,
                        unique=False,
                        nullable=True)
    created_on = db.Column(db.DateTime,
                           index=False,
                           unique=False,
                           nullable=True)
    last_login = db.Column(db.DateTime,
                           index=False,
                           unique=False,
                           nullable=True)

    def set_password(self, password):
        """Create hashed password."""
        self.password = generate_password_hash(password, method='sha256')

    def check_password(self, password):
        """Check hashed password."""
        return check_password_hash(self.password, password)

    def __repr__ (self):
        return '<User {}>'.format(self.username) 
```

`set_password`和`check_password`方法不一定需要存在于我们的用户模型中，但是最好将相关的逻辑捆绑在一起，不要放在我们的路径中。

您可能注意到我们的密码字段明确允许 200 个字符:这是因为我们的数据库将存储散列密码。因此，即使用户的密码是 8 个字符长，我们数据库中的字符串看起来也会有很大不同。

## 创建登录和注册表单

如果你精通 **WTForms** ，我们在 **forms.py** 中的表单逻辑可能会如你所料。当然，我们在这里设置的约束仅用于处理前端验证:

```
from wtforms import Form, StringField, PasswordField, validators, SubmitField
from wtforms.validators import ValidationError, DataRequired, Email, EqualTo, Length, Optional

class SignupForm(Form):
    """User Signup Form."""

    name = StringField('Name',
                        validators=[DataRequired(message=('Enter a fake name or something.'))])
    email = StringField('Email',
                        validators=[Length(min=6, message=('Please enter a valid email address.')),
                                    Email(message=('Please enter a valid email address.')),
                                    DataRequired(message=('Please enter a valid email address.'))])
    password = PasswordField('Password',
                             validators=[DataRequired(message='Please enter a password.'),
                                         Length(min=6, message=('Please select a stronger password.')),
                                         EqualTo('confirm', message='Passwords must match')])
    confirm = PasswordField('Confirm Your Password',)
    website = StringField('Website',
                          validators=[Optional()])
    submit = SubmitField('Register')

class LoginForm(Form):
    """User Login Form."""

    email = StringField('Email', validators=[DataRequired('Please enter a valid email address.'),
                                             Email('Please enter a valid email address.')])
    password = PasswordField('Password', validators=[DataRequired('Uhh, your password tho?')])
    submit = SubmitField('Log In') 
```

说完这些，让我们看看如何在 Jinja 端实现它们。

### signup.html

```
{% extends "layout.html" %}

{% block pagestyles %}
    <link href="{{ url_for('static', filename='dist/css/account.css') }}" rel="stylesheet" type="text/css">
{% endblock %}

{% block content %}
  <div class="formwrapper">
    <form method=post>
      <div class="logo">
        <img src="{{ url_for('static', filename='dist/img/logo.png') }}">
      </div>
      {% for message in get_flashed_messages() %}
        <div class="alert alert-warning">
            <button type="button" class="close" data-dismiss="alert">&times;</button>
            {{ message }}
        </div>
      {% endfor %}
      <h1>Sign Up</h1>
      <div class="name">
        {{ form.name.label }}
        {{ form.name(placeholder='John Smith') }}
        {% if form.name.errors %}
          <ul class="errors">
            {% for error in form.email.errors %}<li>{{ error }}</li>{% endfor %}
          </ul>
        {% endif %}
      </div>
      <div class="email">
        {{ form.email.label }}
        {{ form.email(placeholder='youremail@example.com') }}
        {% if form.email.errors %}
          <ul class="errors">
            {% for error in form.email.errors %}<li>{{ error }}</li>{% endfor %}
          </ul>
        {% endif %}
      </div>
      <div class="password">
        {{ form.password.label }}
        {{ form.password }}
        {% if form.password.errors %}
          <ul class="errors">
            {% for error in form.password.errors %}<li>{{ error }}</li>{% endfor %}
          </ul>
        {% endif %}
      </div>
      <div class="confirm">
        {{ form.confirm.label }}
        {{ form.confirm }}
        {% if form.confirm.errors %}
          <ul class="errors">
            {% for error in form.confirm.errors %}<li>{{ error }}</li>{% endfor %}
          </ul>
        {% endif %}
      </div>
      <div class="website">
        {{ form.website.label }}
        {{ form.website(placeholder='http://example.com') }}
      </div>
      <div class="submitbutton">
        <input id="submit" type="submit" value="Submit">
      </div>
    </form>
    <div class="loginsignup">
      <span>Already have an account? <a href="{{ url_for('auth_bp.login_page') }}">Log in.</a><span>
    </div>
  </div>
{% endblock %} 
```

### login.py

```
{% extends "layout.html" %}

{% block pagestyles %}
  <link href="{{ url_for('static', filename='dist/css/account.css') }}" rel="stylesheet" type="text/css">
{% endblock %}

{% block content %}
  <div class="formwrapper">
    <form method=post>
      <div class="logo">
        <img src="{{ url_for('static', filename='dist/img/logo.png') }}">
      </div>
      {% for message in get_flashed_messages() %}
        <div class="alert alert-warning">
            <button type="button" class="close" data-dismiss="alert">&times;</button>
            {{ message }}
        </div>
      {% endfor %}
      <h1>Log In</h1>
      <div class="email">
         {{ form.email.label }}
         {{ form.email(placeholder='youremail@example.com') }}
         {% if form.email.errors %}
           <ul class="errors">
             {% for error in form.email.errors %}<li>{{ error }}</li>{% endfor %}
           </ul>
         {% endif %}
      </div>
      <div class="password">
        {{ form.password.label }}
        {{ form.password }}
        {% if form.email.errors %}
          <ul class="errors">
            {% for error in form.password.errors %}<li>{{ error }}</li>{% endfor %}
          </ul>
        {% endif %}
      </div>
      <div class="submitbutton">
        <input id="submit" type="submit" value="Submit">
      </div>
      <div class="loginsignup">
        <span>Don't have an account? <a href="{{ url_for('auth_bp.signup_page') }}">Sign up.</a><span>
        </div>
    </form>
  </div>
{% endblock %} 
```

非常好:舞台已经准备好开始踢一些屁股。

## 创建我们的登录路线

让我们将注意力转向我们将在 **auth.py** 中写入的逻辑核心:

```
import os
from flask import redirect, render_template, flash, Blueprint, request, session, url_for
from flask_login import login_required, logout_user, current_user, login_user
from flask import current_app as app
from werkzeug.security import generate_password_hash, check_password_hash
from .forms import LoginForm, SignupForm
from .models import db, User
from . import login_manager

# Blueprint Configuration
auth_bp = Blueprint('auth_bp', __name__ ,
                    template_folder='templates',
                    static_folder='static')
assets = Environment(app)

@auth_bp.route('/login', methods=['GET', 'POST'])
def login_page():
    """User login page."""
    # Bypass Login screen if user is logged in
    if current_user.is_authenticated:
        return redirect(url_for('main_bp.dashboard'))
    login_form = LoginForm(request.form)
    # POST: Create user and redirect them to the app
    if request.method == 'POST':
        ...
    # GET: Serve Log-in page
    return render_template('login.html',
                           form=LoginForm(),
                           title='Log in | Flask-Login Tutorial.',
                           template='login-page',
                           body="Log in with your User account.")

@auth_bp.route('/signup', methods=['GET', 'POST'])
def signup_page():
    """User sign-up page."""
    signup_form = SignupForm(request.form)
    # POST: Sign user in
    if request.method == 'POST':
        ...
    # GET: Serve Sign-up page
    return render_template('/signup.html',
                           title='Create an Account | Flask-Login Tutorial.',
                           form=SignupForm(),
                           template='signup-page',
                           body="Sign up for a user account.") 
```

这里我们找到了两条独立的主干路径，分别是**注册**和**登录**。在没有添加认证逻辑的情况下，到目前为止，这些路由看起来几乎是相同的。

每当用户访问你的应用程序中的一个页面时，相应的路由就会被发送一个`request`对象。该对象包含用户请求的上下文信息，比如请求的类型(GET 或 POST)、提交的表单数据等。我们利用这一点来查看用户是第一次到达页面(GET 请求)，还是正在尝试登录(POST 请求)。这里相当聪明的一点是，我们的登录页面通过向用户自己发出 POST 请求来验证用户:这允许我们将所有与登录或注册相关的逻辑保留在一条路径中。

### 报名

我们能够通过导入`SignupForm`类并传递`request.form`作为有问题的表单来验证提交的表单。`if signup_form.validate()`根据表单的所有验证器检查用户提交的信息。如果不满足任何验证器，用户将被重定向回注册表单，并显示错误消息。

假设我们的用户不是无能的，我们可以继续我们的逻辑。首先，我们需要确保所提供电子邮件的用户不存在:

```
...

@auth_bp.route('/signup', methods=['GET', 'POST'])
def signup_page():
    """User sign-up page."""
    signup_form = SignupForm(request.form)
    # POST: Sign user in
    if request.method == 'POST':
        if signup_form.validate():
            # Get Form Fields
            name = request.form.get('name')
            email = request.form.get('email')
            password = request.form.get('password')
            website = request.form.get('website')
            existing_user = User.query.filter_by(email=email).first()
            if existing_user is None:
                user = User(name=name,
                            email=email,
                            password=generate_password_hash(password, method='sha256'),
                            website=website)
                db.session.add(user)
                db.session.commit()
                login_user(user)
                return redirect(url_for('main_bp.dashboard'))
            flash('A user already exists with that email address.')
            return redirect(url_for('auth_bp.signup_page'))
    # GET: Serve Sign-up page
    return render_template('/signup.html',
                           title='Create an Account | Flask-Login Tutorial.',
                           form=SignupForm(),
                           template='signup-page',
                           body="Sign up for a user account.") 
```

如果`existing_user is None`，我们就可以创建一个新的用户记录。我们通过`user = User(...)`创建用户模型的实例。然后我们通过标准的 SQLAlchemy 语法添加用户，最后使用导入的方法`login_user()`让用户登录。

如果一切顺利，用户最终将被重定向到主应用程序，这由`return redirect(url_for('main_bp.dashboard'))`处理:

[![Using Flask-Login to Handle User Accounts](img/1e7184097a22775a40836ec3ecfb0255.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zxk7cTpD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hackersandslackers.com/conteimg/2019/04/signup.gif) 

<figcaption>用户登录成功。</figcaption>

如果我们注销并尝试使用相同的信息注册，会发生以下情况:

[![Using Flask-Login to Handle User Accounts](img/e7895f5fcabaa43758928a68b96355ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NSQ-Ndiy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hackersandslackers.com/conteimg/2019/04/signupfailed.gif) 

<figcaption>试图用现有邮箱注册</figcaption>

### 登录

继续我们的登录路线:

```
@auth_bp.route('/login', methods=['GET', 'POST'])
def login_page():
    """User login page."""
    # Bypass Login screen if user is logged in
    if current_user.is_authenticated:
        return redirect(url_for('main_bp.dashboard'))
    login_form = LoginForm(request.form)
    # POST: Create user and redirect them to the app
    if request.method == 'POST':
        if login_form.validate():
            # Get Form Fields
            email = request.form.get('email')
            password = request.form.get('password')
            # Validate Login Attempt
            user = User.query.filter_by(email=email).first()
            if user:
                if user.check_password(password=password):
                    login_user(user)
                    next = request.args.get('next')
                    return redirect(next or url_for('main_bp.dashboard'))
        flash('Invalid username/password combination')
        return redirect(url_for('auth_bp.login_page'))
    # GET: Serve Log-in page
    return render_template('login.html',
                           form=LoginForm(),
                           title='Log in | Flask-Login Tutorial.',
                           template='login-page',
                           body="Log in with your User account.") 
```

这看起来应该差不多！我们的逻辑是相同的，直到我们检查用户是否存在。这一次，匹配的结果是成功，而不是失败。接下来，我们使用`user.check_password()`来检查我们之前用`user.generate_password_hash()`创建的散列密码。这两种方法都自行处理密码的加密和解密(基于我们之前创建的 SECRET_KEY ),以确保没有人(甚至包括我们)有权查看用户密码。

和上次一样，一次成功的登录以`login_user(user)`结束。这一次，我们的重定向逻辑稍微复杂一些:我们不是总是将用户发送回仪表板，而是检查`next`，这是存储在当前用户的查询字符串中的一个参数。如果用户在登录之前试图访问我们的应用，`next`将等于他们试图到达的页面:这允许我们将我们的应用与未经授权的用户隔离，然后将用户放在他们登录之前试图到达的页面:

[![Using Flask-Login to Handle User Accounts](img/087ce8b2cc3d1076d551cec9a77ba86b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kmreMNPy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hackersandslackers.com/conteimg/2019/04/login.gif) 

<figcaption>成功登录</figcaption>

### 重要提示:登录助手

在你的应用程序可以像上面那样工作之前，我们需要通过提供更多的路线来完成 **auth.py** :

```
@auth_bp.route("/logout")
@login_required
def logout_page():
    """User log-out logic."""
    logout_user()
    return redirect(url_for('auth_bp.login_page'))

@login_manager.user_loader
def load_user(user_id):
    """Check if user is logged-in on every page load."""
    if user_id is not None:
        return User.query.get(user_id)
    return None

@login_manager.unauthorized_handler
def unauthorized():
    """Redirect unauthorized users to Login page."""
    flash('You must be logged in to view that page.')
    return redirect(url_for('auth_bp.login_page')) 
```

我们的第一个路由`logout_page`，处理用户注销的逻辑。这将简单地结束用户的会话，并将他们重定向到登录屏幕。

`load_user`对于让我们的应用程序工作是至关重要的:在每个页面加载之前，我们的应用程序必须验证用户是否登录(或者*在时间过去后仍然*登录)。`user_loader`通过用户的唯一 ID 加载用户。如果返回一个用户，这表示一个注销的用户。否则，当返回`None`时，用户被注销。

最后，我们有`unauthorized`路线，它使用`unauthorized_handler`装饰器来处理未经授权的用户。任何时候，如果用户试图访问我们的应用程序，并且没有得到授权，这条路径就会被触发。

## 最后一块:routes.py

我们要讨论的最后一件事是如何保护我们的应用程序免受未授权用户的攻击。以下是我们在 **routes.py** 中看到的内容:

```
import os
from flask import Blueprint, render_template
from flask_assets import Environment, Bundle
from flask_login import current_user
from flask import current_app as app
from .models import User
from flask_login import login_required

# Blueprint Configuration
main_bp = Blueprint('main_bp', __name__ ,
                    template_folder='templates',
                    static_folder='static')
assets = Environment(app)

@main_bp.route('/', methods=['GET'])
@login_required
def dashboard():
    """Serve logged in Dashboard."""
    return render_template('dashboard.html',
                           title='Flask-Login Tutorial.',
                           template='dashboard-template',
                           current_user=current_user,
                           body="You are now logged in!") 
```

这里的魔力都包含在`@login_required`装饰器中。当这个装饰器出现在路线上时，会发生以下事情:

*   我们创建的`@login_manager.user_loader`路由决定了用户是否有权查看页面(已登录)。如果用户登录，他们将被允许查看该页面。
*   如果用户没有登录，将按照用`@login_manager.unauthorized_handler`修饰的路由中的逻辑重定向用户。
*   用户试图访问的路线名称将作为`?url=[name-of-route]`存储在 URL 中。这就是允许`next`工作的原因。

### 好了

如果你已经做到了这一步，我赞扬你的勇气。为了奖励你的成就，我在 Github 上发布了本教程的[源代码供你参考。一路顺风，勇敢的冒险家。](https://github.com/toddbirchard/flasklogin-tutorial)