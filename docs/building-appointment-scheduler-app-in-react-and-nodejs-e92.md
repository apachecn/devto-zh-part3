# 在 React 和 NodeJS 中构建约会调度程序应用程序

> 原文：<https://dev.to/kris/building-appointment-scheduler-app-in-react-and-nodejs-e92>

#### 我们将创建一个预约日程安排程序，使用户能够通过选择日期和时间并提供他们的用户信息(姓名、电子邮件、电话号码)来安排预约。通过文本向用户的电话发送确认以确认约会。

[![](img/8b04b072c4e3f2afe443163bab67d244.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hCwVkMWb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVAZxSSx7BYMDqxp6BumhPw.png)

[![](img/f0b17823feac6ff38f191ace5279f03b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVu18JFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A_2qTe_i7JioOl3SJ_YTZrQ.png)

#### 升级你的 JavaScript 技能。

只需几个下午就能学会 React.js。

Wes Bos 提供的一步一步的高级培训课程，帮助您构建真实世界的 React.js + Firebase 应用程序和网站组件。 [**现在开始学习**](https://reactforbeginners.com/#packages)

[![](img/f0b17823feac6ff38f191ace5279f03b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVu18JFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A_2qTe_i7JioOl3SJ_YTZrQ.png)

这是一个高级培训课程，旨在强化您的核心 JavaScript 技能

，并掌握 ES6 提供的所有内容。[现在就加入 22120 学习→](https://es6.io/#packages)

请参见下面的预览:

[![React-App](img/b5700dd5a878dd10f8c499c6e019f4a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lt4tPqhZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AcggUWwHEn_lOEqps.jpg)

我们将重点关注以下内容:

**后端(第一部分)**

1.  创建模板 Express Web 应用程序
2.  MongoDB 数据库配置
3.  创建 Nexmo 帐户
4.  为 Express web 应用程序创建路线、模型和控制器
5.  用 PostmanT1】测试 Express Web App https://medium . com/media/18214d 8333 adbb 5668 f 31791 a 3202 b 60/href

**前端(第二部分)**

1.  创建模板 React 应用程序
2.  为 React 创建视图
3.  深度应用功能。

源代码见[此处](https://github.com/krissnawat/node-react-appoinment)

#### 带节点的后端

对于后端，我们将使用以下框架:

Express 是一个快速、灵活的模块化 Node.js web 应用程序框架，为 web 和移动应用程序提供了一组强大的功能。

**Nexmo** 帮助成长中的初创公司和敏捷企业增强客户体验，并实现新的大规模业务成果。Nexmo 有一个短信服务，我们将使用它向用户发送消息。

MongoDB 是一个免费的面向文档的数据库程序。它被认为是一个 NoSQL 数据库程序，MongoDB 使用类似 JSON 的文档和模式。

**mongose**为 node.js 建模 MongoDB 对象，对于这个演示，它将使我们能够为我们的 express 应用程序建模 MongoDB 对象。

**NPM 和节点安装**

节点和 NPM 的安装是实现任何合理目标之前所必需的。安装节点也会自动安装 npm。可以从网站上的安装说明(基本上是下载并安装)安装节点。如果您已经安装了这两个软件包，请跳过这一步。)

下载后，检查是否通过命令行(CLI)安装了节点和 npm:

节点 v

npm -v

**创建模板 Express Web App**

创建一个名为“约会-日程安排-web-react-app”的文件夹

从您的命令行访问您的“约会-日程安排-web-react-app”文件夹，继续到 api 文件夹，如下所示:

cd 预约-日程安排-web-react-app

全局安装 express CLI(命令行界面)工具:

npm 安装快速发电机 g

要创建新的 express 项目:

express - view=ejs api

生成器创建一个名为 api 的新 express 应用程序。— view 标志告诉生成器对视图文件使用 ejs 模板引擎。

访问您的文件夹:

cd api

文件夹结构应该是这样的:

+ - bin

+ -公共

+ -路线

+ -观点

+-app . js

+-package . JSON

接下来，通过命令行(CLI)在新的 api 文件夹中安装以下依赖项:

npm 安装

npm 安装猫鼬

Mongoose 将用于创建 MongoDB 对象，并将它们连接到 MongoDB 数据库。

您的 app.js 文件应该如下所示:

[![apps](img/36ec7102f28d14ac0e74bebc6979a951.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pGqANz36--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/827/0%2AFUWe7wdVhyFjPimK.PNG)

**创建 Nexmo 账户**

我们将使用 Nexmo 在安排好预约后向用户的手机号码发送短信。

我们将创建一个 Nexmo 帐户，在此注册

如果你在 nexmo 上发现错误，我已经收到了 Nexmo 工作人员的评论

[![](img/90c86449504d1638553d50e006024752.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Epyi_M4w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/984/1%2AQvA8FQWN97pqfSrTeBDIRw.png)

通过 CLI 安装 Nexmo:

npm 安装 nexmo

帐户设置后，确保您从控制台仪表板获得以下信息

1.API 密钥

1.  API 秘密

将它们复制到安全的地方，我们稍后将需要它来在文件 controllers/appointments.js 中创建 nexmo 对象。

**创建 MongoDB 数据库**

MongoDB 是一个开源的跨平台面向文档的数据库程序。作为一个 NoSQL 数据库程序，MongoDB 使用类似 JSON 的文档和模式。

MongoDB 实例可以下载并安装在您的机器上，更多信息请参见链接，但是对于本教程，我们感兴趣的是云选项，我们可以通过 Mongoose 连接字符串将 express 应用程序连接到数据库。

现在让我们通过 Mongolab 创建一个 MongoDB 数据库，MongoLab 是免费的，易于设置。

首先:

创建一个 MongoLab 帐户

创建一个新的数据库，如下图所示:

[![sandox-option](img/4fc766411d9d191b19c4bfbd211a0de4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7v3WQ_sE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Azju6P6c6bnd4iMuA.PNG)

单击上面的沙盒选项创建一个免费层

接下来，对于下面的 AWS 地区，选择美国东部(弗吉尼亚州)(美国东部-1)选项:

[![sandbox2](img/b441e64dc29b4881063a050e4f4a628f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8phkj6d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AjH8O9WTF2PLmk0xf.PNG)

在下面，键入“约会”作为数据库名称

[![sandbox3](img/30851d60ff70ff27b1887f78004c249c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UsmD0xev--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AN1TjP6a-XWrT6e7a.PNG)

点击继续，你就完成了。恭喜你已经完成了数据库创建过程。下面是你的数据库的预览。您应该会在 MongoDB 部署中看到您的数据库:

[![sandbox6](img/eb8f739bd5dbf75cb77d87bb1806596d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E4QAxgLk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AvY6pisbnsJdPQupc.PNG)

**为 Express Web App**

创建路线、模型和控制器我们现在要构建 express app 特性。我们的 express 应用程序将使约会日程安排应用程序能够与我们之前创建的 MongoDB 进行交互。

快速应用程序需要以下内容:

**Models** :要使用 mongoose 访问 MongoDB 数据库，我们需要定义模式和模型。模式和模型向 mongoose 传达了由字段和数据类型组成的数据结构的简化表示。

**Routes** : Express 使用路由来处理来自客户端应用程序(在本例中是预约调度程序)和 MongoDB 的请求和响应。

**控制器**:控制器其实就是回调函数。继续，appointmentController.all、slotController.all 和 appointmentController.create 是回调函数，我们称之为控制器。

首先，我们必须编辑 **app.js** 文件来添加我们的 mongoose 连接:

```
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
const mongoose = require('mongoose');

var index = require('./routes/index');
const api = require('./routes/api/index');

var app = express();

mongoose.Promise = global.Promise;

//Adds connection to database using mongoose
//for \<dbuser\>:replace with your username, \<dbpassword\>: replace with your password.
//\<DATABASE\_URL\>: replace with database url, example:ds234562.mlab.com:17283
mongoose.connect('\<dbuser\>:\<dbpassword\>@\<DATABASE\_URL\>/appointments', {
 useMongoClient: true
});

//This enabled CORS, Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources (e.g. fonts) 
//on a web page to be requested from another domain outside the domain from which the first resource was served

app.all('/\*', function(req, res, next) {
 // CORS headers
 res.header("Access-Control-Allow-Origin", "\*"); // restrict it to the required domain
 res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');
 // Set custom headers for CORS
 res.header('Access-Control-Allow-Headers', 'Content-type,Accept,X-Access-Token,X-Key');
 if (req.method == 'OPTIONS') {
 res.status(200).end();
 } else {
 next();
 }
});
// view engine setup
app.set('views', path.join(\_\_dirname, 'views'));
app.set('view engine', 'ejs');

// uncomment after placing your favicon in /public
//app.use(favicon(path.join(\_\_dirname, 'public', 'favicon.ico')));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(\_\_dirname, 'public')));

app.use('/', index);
app.use('/api', api);
//app.use('/users', users);

// catch 404 and forward to error handler
app.use(function(req, res, next) {
 var err = new Error('Not Found');
 err.status = 404;
 next(err);
});

// error handler
app.use(function(err, req, res, next) {
 // set locals, only providing error in development
 res.locals.message = err.message;
 res.locals.error = req.app.get('env') === 'development' ? err : {};

// render the error page
 res.status(err.status || 500);
 res.render('error');
});

module.exports = app; 
```

在您的基本 api 文件夹中创建一个 models/index.js 文件:

```
const Schema = mongoose.Schema,
 model = mongoose.model.bind(mongoose),
 ObjectId = mongoose.Schema.Types.ObjectId;

const slotSchema = new Schema ({
 slot\_time: String,
 slot\_date: String,
 created\_at: Date
 });

const Slot = model('Slot', slotSchema);

const appointmentSchema = new Schema({
 id: ObjectId,
 name: String,
 email: String,
 phone: Number,
 slots:{type: ObjectId, ref: 'Slot'},
 created\_at: Date
});

const Appointment = model('Appointment', appointmentSchema); 
```

**快速应用控制器**

在基本 api 文件夹
中创建一个 controllers/appointments.js 文件

```
const { Appointment, Slot } = Model;
const Nexmo = require("nexmo");

const appointmentController = {
 all(req, res) {
 // Returns all appointments
 Appointment.find({}).exec((err, appointments) =\> res.json(appointments));
 },
 create(req, res) {
 var requestBody = req.body;`

var newslot = new Slot({
 slot\_time: requestBody.slot\_time,
 slot\_date: requestBody.slot\_date,
 created\_at: Date.now()
 });
 newslot.save();
 // Creates a new record from a submitted form
 var newappointment = new Appointment({
 name: requestBody.name,
 email: requestBody.email,
 phone: requestBody.phone,
 slots: newslot.\_id
 });

const nexmo = new Nexmo({
 apiKey: "YOUR\_API\_KEY",
 apiSecret: "YOUR\_API\_SECRET"
 });

let msg =
 requestBody.name +
 " " +
 "this message is to confirm your appointment at" +
 " " +
 requestBody.appointment;

// and saves the record to
 // the data base
 newappointment.save((err, saved) =\> {
 // Returns the saved appointment
 // after a successful save
 Appointment.find({ \_id: saved.\_id })
 .populate("slots")
 .exec((err, appointment) =\> res.json(appointment));

const from = VIRTUAL\_NUMBER;
 const to = RECIPIENT\_NUMBER;

nexmo.message.sendSms(from, to, msg, (err, responseData) =\> {
 if (err) {
 console.log(err);
 } else {
 console.dir(responseData);
 }
 });
 });
 }
};

module.exports = appointmentController; 
```

在基本 api 文件夹
中创建一个 controllers/slot.js 文件

```
const {Appointment, Slot} = Model;

const slotController = {
 all (req, res) {
 // Returns all Slots
 Slot.find({})
 .exec((err, slots) =\> res.json(slots))
 }
};

module.exports = slotController; 
```

快速应用程序的路由

编辑 routes/index.js 文件，如下所示:

```
var router = express.Router();

/\* GET home page. \*/
router.get('/', function(req, res, next) {
 res.render('index', { title: 'Express App running' });
});

module.exports = router; 
```

快速路由器负责应用程序内的导航，它从上面获取并显示位于 path views/index .ejs 中的快速应用程序默认页面。

让我们继续创建自己的路线。创建一个 routes/api/index.js

```
const router = express.Router();

const appointmentController = require('../../controllers/appointments')
const slotController = require('../../controllers/slot')

router.get('/appointments', appointmentController.all);
router.get('/retrieveSlots', slotController.all);
router.post('/appointmentCreate', appointmentController.create);

module.exports = router; 
```

express 和 react 应用程序都在开发模式下使用端口 3000。因此，我们将把本地主机端口值更改为 8083

在您的快递应用程序中进入 bin/ [www.js](http://www.js)

将端口从 3000 更改为 8083

使用 Ctrl C(对于 windows)、Ctrl Z(对于 mac)停止 express 应用程序，使用以下命令重新运行:

npm 开始

使用浏览器转到 localhost:8083:

[![express2](img/68ff11ae73f1c85bdeb3b965452f191b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ouWrDiRU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/538/0%2Asd_vsD92cHjbFttL.PNG)

现在我们已经完成了快速应用程序的创建。让我们看看是否可以使用我们的 express 应用程序发布和获取值。

[![](img/f0b17823feac6ff38f191ace5279f03b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVu18JFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A_2qTe_i7JioOl3SJ_YTZrQ.png)

#### 升级你的 JavaScript 技能。

只需几个下午就能学会 React.js。

Wes Bos 提供的一步一步的高级培训课程，帮助您构建真实世界的 React.js + Firebase 应用程序和网站组件。 [**现在开始学习**](https://reactforbeginners.com/#packages)

[![](img/f0b17823feac6ff38f191ace5279f03b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVu18JFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A_2qTe_i7JioOl3SJ_YTZrQ.png)

**与邮递员一起测试**

由于我们没有发送值的表单，Postman 是测试 API 的实用工具，是运行测试的理想工具，有了 Postman，我们可以使用前面创建的路径向数据库发送值或从数据库获取值，让我们继续:

你需要下载并安装 Postman

让我们开始本地测试，发布预约:

[![postman5](img/a46a4553bfa89d0950f027dadf9a3eec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--herUjp9y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/736/0%2AuZFVmDQLn6-3JDRh.PNG)

接下来，检索插槽:

[![postman6](img/a210e56c4b50defba2e8d34ed5365809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X2jWMu8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/635/0%2AgfFMc4obibqz5BXR.PNG)

#### 前端带 React App

现在，访问您的命令行界面(CLI ),使用以下命令通过 npm 全局创建 react 应用程序:

对于 npm 版本 5.1 或更低版本

NPM install-g create-react-app

要创建我们的约会调度程序应用程序，运行一个命令

NPM install create-react-app appointment-scheduler

对于 npm 版本 5.2 及更高版本

npx install-g create-react-app

要创建我们的约会调度程序应用程序，运行一个命令

npx install create-react-app appointment-scheduler

在当前目录中创建了一个名为 appointment-scheduler 的目录。在文件夹内，您应该看到以下过渡依赖项
【任命调度程序】

【readme . MD】

【node _ modules】

软件包。json

git ignore

【favicon .

【index . html】
【manifest . JSON】

通过 CLI

npm start 以开发模式启动项目

使用

cd 预约-计划程序访问您的应用程序目录

安装以下依赖项:

npm 安装材料-ui

npm 安装 axios

**为约会日程安排应用程序创建视图**

我们将首先创建一个组件文件夹:

在您的约会安排应用程序的“src”文件夹目录中创建一个名为“components”的文件夹。见下文:

约会-日程安排程序/src/组件

使用以下代码在 components 文件夹中创建 appointment app . js:

```
import React, {
 Component
} from "react";
import logo from "./logo.svg";
class AppointmentApp extends Component {
 render() {
 return ( 
 \<div\>
 \< p\> ApointmentApp \</p\> 
 \</div\>
 );
 }
}
export default AppointmentApp; 
```

更新你的 src/App.js

```
import logo from "./logo.svg";
import AppointmentApp from "./components/AppointmentApp.js";
import MuiThemeProvider from "material-ui/styles/MuiThemeProvider";
import "./App.css";
class App extends Component {
 render() {
 return (
 \<div\>
 \<MuiThemeProvider\>
 \<AppointmentApp /\>
 \</MuiThemeProvider\>
 \</div\>
 );
 }
}
export default App; 
```

注意，我们导入了 AppointmentApp 组件并包含了一个 MuiThemeProvider 组件。appointmentApp 组件包含在 MuiThemeProvider 中，用于将 material-ui 主题注入到应用程序上下文中。接下来，您可以使用 material-ui 文档中的任何组件。

**App 功能深度**

**App 状态描述**

App 通过 express app 从数据库加载数据

*   我们的预约安排过程将分三步进行:选择日期、选择时间段和填写个人信息。我们需要跟踪用户正在进行的步骤，他们选择的日期和时间，他们的联系方式，我们还需要验证他们的电子邮件地址和电话号码。
*   我们将加载预定的预约时段，当用户选择可用时段时，我们将从中受益。先前用户已经选择的时间段不能再次选择。
*   当我们的用户继续执行日程安排步骤时，在步骤 1 中选择日期后，应用程序将继续执行步骤 2，用户将选择上午或下午，这将显示可用的约会时间(时间段)，完成后，演示将继续执行步骤 3，用户将输入他们的详细信息并安排约会。
*   一个模态将显示用户确认的约会日期和时间。
*   最后，snackbar 将显示一条消息，指示约会成功或失败。

**appointment app 组件的方法描述**

因为 AppointmentApp 组件有四百多行代码。随着我们的进展，我们将对每个代码块进行划分和解释。点击这里查看完整版本的源代码。

各种组件从我们安装的各自的框架/库导入。使用 this.state.

设置名、姓等各种字段的状态。对于下面的 componentWillMount 生命周期方法，通过 express 应用程序从数据库中检索以前的预定预约时段。

```
import React, { Component } from "react";
import AppBar from "material-ui/AppBar";
import RaisedButton from "material-ui/RaisedButton";
import FlatButton from "material-ui/FlatButton";
import moment from "moment";
import DatePicker from "material-ui/DatePicker";
import Dialog from "material-ui/Dialog";
import SelectField from "material-ui/SelectField";
import MenuItem from "material-ui/MenuItem";
import TextField from "material-ui/TextField";
import SnackBar from "material-ui/Snackbar";
import Card from "material-ui/Card";
import {
 Step,
 Stepper,
 StepLabel,
 StepContent
} from "material-ui/Stepper";
import { RadioButton, RadioButtonGroup } from "material-ui/RadioButton";
import axios from "axios";

const API\_BASE = "[http://localhost:8083/](http://localhost:8083/)";

class AppointmentApp extends Component {
 constructor(props, context) {
 super(props, context);

this.state = {
 firstName: "",
 lastName: "",
 email: "",
 schedule: [],
 confirmationModalOpen: false,
 appointmentDateSelected: false,
 appointmentMeridiem: 0,
 validEmail: true,
 validPhone: true,
 finished: false,
 smallScreen: window.innerWidth \< 768,
 stepIndex: 0
 };
 }
 componentWillMount() {
 axios.get(API\_BASE + `api/retrieveSlots`).then(response =\> {
 console.log("response via db: ", response.data);
 this.handleDBReponse(response.data);
 });
 } 
```

handleNext 方法使用 stepIndex 字段将步进器移动到下一个位置。

handle prev 方法使用 stepIndex 字段将步进器移动到上一个位置。

```
handleNext = () =\> {
 const { stepIndex } = this.state;
 this.setState({
 stepIndex: stepIndex + 1,
 finished: stepIndex \>= 2
 });
 };

handlePrev = () =\> {
 const { stepIndex } = this.state;
 if (stepIndex \> 0) {
 this.setState({ stepIndex: stepIndex - 1 });
 }
 }; 
```

**validateEmail** 方法检查 Email 参数，如果 email 值有效则返回 true，否则返回 false。

方法检查电话参数，如果电话值有效则返回 true，否则返回 false。

```
validateEmail(email) {
 const regex = /^(([^\<\>()\[\]\.,;:\s@\"]+(\.[^\<\>()\[\]\.,;:\s@\"]+)\*)|(\".+\"))@(([^\<\>()[\]\.,;:\s@\"]+\.)+[^\<\>()[\]\.,;:\s@\"]{2,})$
 /i;
 return regex.test(email)
 ? this.setState({ email: email, validEmail: true })
 : this.setState({ validEmail: false });
 }
 validatePhone(phoneNumber) {
 const regex = /^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/;
 return regex.test(phoneNumber)
 ? this.setState({ phone: phoneNumber, validPhone: true })
 : this.setState({ validPhone: false });
 } 
```

renderStepActions 方法呈现下一步、完成和后退按钮。

```
renderStepActions(step) {
 const { stepIndex } = this.state;

return (
 \<div style={{ margin: "12px 0" }}\>
 \<RaisedButton
 label={stepIndex === 2 ? "Finish" : "Next"}
 disableTouchRipple={true}
 disableFocusRipple={true}
 primary={true}
 onClick={this.handleNext}
 backgroundColor="#00C853 !important"
 style={{ marginRight: 12, backgroundColor: "#00C853" }}
 /\>
 {step \> 0 && (
 \<FlatButton
 label="Back"
 disabled={stepIndex === 0}
 disableTouchRipple={true}
 disableFocusRipple={true}
 onClick={this.handlePrev}
 /\>
 )}
 \</div\>
 );
} 
```

handleSetAppointmentDate 方法用于设置 AppointmentDate 字段的状态。

handleSetAppointmentSlot 方法用于设置 AppointmentSlot 字段的状态。
T3【handleSetAppointmentMeridiem】方法用于设置 AppointmentMeridiem 字段的状态。

```
handleSetAppointmentDate(date) {
 this.setState({ appointmentDate: date, confirmationTextVisible: true });
 }
handleSetAppointmentSlot(slot) {
 this.setState({ appointmentSlot: slot });
 }
handleSetAppointmentMeridiem(meridiem) {
 this.setState({ appointmentMeridiem: meridiem });
 } 
```

checkDisableDate 方法将禁用的日期传递给日期选取器组件。

handleDBResponse 方法处理来自数据库的约会时间段数据。

```
checkDisableDate(day) {
 const dateString = moment(day).format("YYYY-DD-MM");
 return (
 this.state.schedule[dateString] === true ||
 moment(day)
 .startOf("day")
 .diff(moment().startOf("day")) \< 0
 );
 }

handleDBReponse(response) {
 const appointments = response;
 const today = moment().startOf("day"); //start of today 12 am
 const initialSchedule = {};
 initialSchedule[today.format("YYYY-DD-MM")] = true;
 const schedule = !appointments.length
 ? initialSchedule
 : appointments.reduce((currentSchedule, appointment) =\> {
 const { slot\_date, slot\_time } = appointment;
 const dateString = moment(slot\_date, "YYYY-DD-MM").format(
 "YYYY-DD-MM"
 );
 !currentSchedule[slot\_date]
 ? (currentSchedule[dateString] = Array(8).fill(false))
 : null;
 Array.isArray(currentSchedule[dateString])
 ? (currentSchedule[dateString][slot\_time] = true)
 : null;
 return currentSchedule;
 }, initialSchedule);

for (let day in schedule) {
 let slots = schedule[day];
 slots.length
 ? slots.every(slot =\> slot === true) ? (schedule[day] = true) : null
 : null;
 }

this.setState({
 schedule: schedule
 });
 } 
```

renderAppointmentTimes 方法向用户呈现可用的时隙，并禁用其余的时隙(如果有的话)。

```
renderAppointmentTimes() {
 if (!this.state.isLoading) {
 const slots = [...Array(8).keys()];
 return slots.map(slot =\> {
 const appointmentDateString = moment(this.state.appointmentDate).format(
 "YYYY-DD-MM"
 );
 const time1 = moment()
 .hour(9)
 .minute(0)
 .add(slot, "hours");
 const time2 = moment()
 .hour(9)
 .minute(0)
 .add(slot + 1, "hours");
 const scheduleDisabled = this.state.schedule[appointmentDateString]
 ? this.state.schedule[
 moment(this.state.appointmentDate).format("YYYY-DD-MM")
 ][slot]
 : false;
 const meridiemDisabled = this.state.appointmentMeridiem
 ? time1.format("a") === "am"
 : time1.format("a") === "pm";
 return (
 \<RadioButton
 label={time1.format("h:mm a") + " - " + time2.format("h:mm a")}
 key={slot}
 value={slot}
 style={{
 marginBottom: 15,
 display: meridiemDisabled ? "none" : "inherit"
 }}
 disabled={scheduleDisabled || meridiemDisabled}
 /\>
 );
 });
 } else {
 return null;
 }
 } 
```

renderAppointmentConfirmation 方法显示用户输入信息的模型，并要求用户在保存到数据库之前进行确认。

```
renderAppointmentConfirmation() {
 const spanStyle = { color: "#00C853" };
 return (
 \<section\>
 \<p\>
 Name:{" "}
 \<span style={spanStyle}\>
 {this.state.firstName} {this.state.lastName}
 \</span\>
 \</p\>
 \<p\>
 Number: \<span style={spanStyle}\>{this.state.phone}\</span\>
 \</p\>
 \<p\>
 Email: \<span style={spanStyle}\>{this.state.email}\</span\>
 \</p\>
 \<p\>
 Appointment:{" "}
 \<span style={spanStyle}\>
 {moment(this.state.appointmentDate).format(
 "dddd[,] MMMM Do[,] YYYY"
 )}
 \</span\>{" "}
 at{" "}
 \<span style={spanStyle}\>
 {moment()
 .hour(9)
 .minute(0)
 .add(this.state.appointmentSlot, "hours")
 .format("h:mm a")}
 \</span\>
 \</p\>
 \</section\>
 );
} 
```

handleSubmit 方法用于通过 express 应用程序将用户数据传递给数据库。如果数据成功保存在数据库中，或者如果数据保存失败，它将显示一条 snackbar 消息。

```
handleSubmit() {
 this.setState({ confirmationModalOpen: false });
 const newAppointment = {
 name: this.state.firstName + " " + this.state.lastName,
 email: this.state.email,
 phone: this.state.phone,
 slot\_date: moment(this.state.appointmentDate).format("YYYY-DD-MM"),
 slot\_time: this.state.appointmentSlot
 };
 axios
 .post(API\_BASE + "api/appointmentCreate", newAppointment)
 .then(response =\>
 this.setState({
 confirmationSnackbarMessage: "Appointment succesfully added!",
 confirmationSnackbarOpen: true,
 processed: true
 })
 )
 .catch(err =\> {
 console.log(err);
 return this.setState({
 confirmationSnackbarMessage: "Appointment failed to save.",
 confirmationSnackbarOpen: true
 });
 });
} 
```

render 方法将所有组件呈现给虚拟 DOM(文档对象模型)。

```
{
 const {
 finished,
 isLoading,
 smallScreen,
 stepIndex,
 confirmationModalOpen,
 confirmationSnackbarOpen,
 ...data
 } = this.state;
 const contactFormFilled =
 data.firstName &&
 data.lastName &&
 data.phone &&
 data.email &&
 data.validPhone &&
 data.validEmail;
 const DatePickerExampleSimple = () =\> (
 \<div\>
 \<DatePicker
 hintText="Select Date"
 mode={smallScreen ? "portrait" : "landscape"}
 onChange={(n, date) =\> this.handleSetAppointmentDate(date)}
 shouldDisableDate={day =\> this.checkDisableDate(day)}
 /\>
 \</div\>
 );
 const modalActions = [
 \<FlatButton
 label="Cancel"
 primary={false}
 onClick={() =\> this.setState({ confirmationModalOpen: false })}
 /\>,
 \<FlatButton
 label="Confirm"
 style={{ backgroundColor: "#00C853 !important" }}
 primary={true}
 onClick={() =\> this.handleSubmit()}
 /\>
 ];
 return (
 \<div\>
 \<AppBar
 title="Appointment Scheduler"
 iconClassNameRight="muidocs-icon-navigation-expand-more"
 /\>
 \<section
 style={{
 maxWidth: !smallScreen ? "80%" : "100%",
 margin: "auto",
 marginTop: !smallScreen ? 20 : 0
 }}
 \>
 \<Card
 style={{
 padding: "12px 12px 25px 12px",
 height: smallScreen ? "100vh" : null
 }}
 \>
 \<Stepper
 activeStep={stepIndex}
 orientation="vertical"
 linear={false}
 \>
 \<Step\>
 \<StepLabel\>
 Choose an available day for your appointment
 \</StepLabel\>
 \<StepContent\>
 {DatePickerExampleSimple()}
 {this.renderStepActions(0)}
 \</StepContent\>
 \</Step\>
 \<Step disabled={!data.appointmentDate}\>
 \<StepLabel\>
 Choose an available time for your appointment
 \</StepLabel\>
 \<StepContent\>
 \<SelectField
 floatingLabelText="AM/PM"
 value={data.appointmentMeridiem}
 onChange={(evt, key, payload) =\>
 this.handleSetAppointmentMeridiem(payload)
 }
 selectionRenderer={value =\> (value ? "PM" : "AM")}
 \>
 \<MenuItem value={0} primaryText="AM" /\>
 \<MenuItem value={1} primaryText="PM" /\>
 \</SelectField\>
 \<RadioButtonGroup
 style={{
 marginTop: 15,
 marginLeft: 15
 }}
 name="appointmentTimes"
 defaultSelected={data.appointmentSlot}
 onChange={(evt, val) =\> this.handleSetAppointmentSlot(val)}
 \>
 {this.renderAppointmentTimes()}
 \</RadioButtonGroup\>
 {this.renderStepActions(1)}
 \</StepContent\>
 \</Step\>
 \<Step\>
 \<StepLabel\>
 Share your contact information with us and we'll send you a
 reminder
 \</StepLabel\>
 \<StepContent\>
 \<p\>
 \<section\>
 \<TextField
 style={{ display: "block" }}
 name="first\_name"
 hintText="First Name"
 floatingLabelText="First Name"
 onChange={(evt, newValue) =\>
 this.setState({ firstName: newValue })
 }
 /\>
 \<TextField
 style={{ display: "block" }}
 name="last\_name"
 hintText="Last Name"
 floatingLabelText="Last Name"
 onChange={(evt, newValue) =\>
 this.setState({ lastName: newValue })
 }
 /\>
 \<TextField
 style={{ display: "block" }}
 name="email"
 hintText="youraddress@mail.com"
 floatingLabelText="Email"
 errorText={
 data.validEmail ? null : "Enter a valid email address"
 }
 onChange={(evt, newValue) =\>
 this.validateEmail(newValue)
 }
 /\>
 \<TextField
 style={{ display: "block" }}
 name="phone"
 hintText="+2348995989"
 floatingLabelText="Phone"
 errorText={
 data.validPhone ? null : "Enter a valid phone number"
 }
 onChange={(evt, newValue) =\>
 this.validatePhone(newValue)
 }
 /\>
 \<RaisedButton
 style={{ display: "block", backgroundColor: "#00C853" }}
 label={
 contactFormFilled
 ? "Schedule"
 : "Fill out your information to schedule"
 }
 labelPosition="before"
 primary={true}
 fullWidth={true}
 onClick={() =\>
 this.setState({
 confirmationModalOpen: !this.state
 .confirmationModalOpen
 })
 }
 disabled={!contactFormFilled || data.processed}
 style={{ marginTop: 20, maxWidth: 100 }}
 /\>
 \</section\>
 \</p\>
 {this.renderStepActions(2)}
 \</StepContent\>
 \</Step\>
 \</Stepper\>
 \</Card\>
 \<Dialog
 modal={true}
 open={confirmationModalOpen}
 actions={modalActions}
 title="Confirm your appointment"
 \>
 {this.renderAppointmentConfirmation()}
 \</Dialog\>
 \<SnackBar
 open={confirmationSnackbarOpen || isLoading}
 message={
 isLoading ? "Loading... " : data.confirmationSnackbarMessage || ""
 }
 autoHideDuration={10000}
 onRequestClose={() =\>
 this.setState({ confirmationSnackbarOpen: false })
 }
 /\>
 \</section\>
 \</div\>
 );
} 
```

使用源代码[更新您的 AppointmentApp.js，通过命令行运行您的应用程序，使用:](https://github.com/krissnawat/node-react-appoinment)

npm 开始

确保 express 和 appointment-scheduler 应用程序都在运行，测试是否工作正常。

#### 结论

现在我们完成了我们的应用程序，祝贺你！，我知道这很有挑战性，但是您已经创建了两个应用程序演示。干得好…

### 精选 React JS 课程

[![](img/f0b17823feac6ff38f191ace5279f03b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVu18JFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A_2qTe_i7JioOl3SJ_YTZrQ.png)

#### 升级你的 JavaScript 技能。

只需几个下午就能学会 React.js。

Wes Bos 提供的一步一步的高级培训课程，帮助您构建真实世界的 React.js + Firebase 应用程序和网站组件。 [**现在开始学习**](https://reactforbeginners.com/#packages)

[![](img/f0b17823feac6ff38f191ace5279f03b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVu18JFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A_2qTe_i7JioOl3SJ_YTZrQ.png)

**免责声明**

此帖子包含产品的附属链接。我们可能会收到通过这些链接购买的佣金。

* * *