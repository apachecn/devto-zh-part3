# 如何在 react 中设置简单项目

> [https://dev . to/andregtz/as-configure-un-project-simple-en-react-2437](https://dev.to/andregtz/como-configuro-un-proyecto-simple-en-react-2437)

# Cookie Clicker App con React

## 设施

要创建应用程序，必须安装 create-react-app。

```
$ yarn global add create-react-app
$ yarn create react-app cookie-clicker
$ cd cookie-clicker 
```

### Instalar eslint

eslint 是 IDE 使用 javascript 开发的方法，用于检查语法错误并加强对已接受的流行样式的使用。

yar 搜索并运行已安装的项目模块`eslint`。不需要安装，因为 create-react-app 会自行安装。

```
$ yarn eslint --init
yarn run v1.15.2
$ /.../cookie-clicker/node_modules/.bin/eslint --init
? How would you like to use ESLint? (Use arrow keys)
  To check syntax only 
  To check syntax and find problems 
> To check syntax, find problems, and enforce code style 
```

选择图标`To check syntax, find problems, and enforce code style`

```
? What type of modules does your project use? (Use arrow keys)
> JavaScript modules (import/export) 
  CommonJS (require/exports) 
  None of these 
```

选择图标`JavaScript modules (import/export)`

```
? Which framework does your project use? (Use arrow keys)
> React 
  Vue.js 
  None of these 
```

选择图标`React`

```
? Where does your code run? (Press <space> to select, <a> to toggle all, <i> to invert selection)
>◉ Browser
 ◉ Node 
```

选择两者`<a> <enter>`

```
? How would you like to define a style for your project? (Use arrow keys)
> Use a popular style guide 
  Answer questions about your style 
  Inspect your JavaScript file(s) 
```

选择图标`Use a popular style guide`

```
? Which style guide do you want to follow? (Use arrow keys)
> Airbnb (https://github.com/airbnb/javascript) 
  Standard (https://github.com/standard/standard) 
  Google (https://github.com/google/eslint-config-google) 
```

选择图标`Airbnb`

> ？您希望您的配置文件采用什么格式？Java Script 语言

```
Checking peerDependencies of eslint-config-airbnb@latest
Local ESLint installation not found.
The config that you've selected requires the following dependencies:

eslint-plugin-react@^7.11.0 eslint-config-airbnb@latest eslint@^4.19.1 || ^5.3.0 eslint-plugin-import@^2.14.0 eslint-plugin-jsx-a11y@^6.1.1
? Would you like to install them now with npm? (Y/n) 
```

因为我们使用的是`npm`而不是`npm`，所以我们会告诉您没有，所以我们会使用`yarn`手动安装这些包。

```
$ yarn add eslint-plugin-react@^7.11.0 eslint-config-airbnb@latest eslint-plugin-import@^2.14.0 eslint-plugin-jsx-a11y@^6.1.1 --dev 
```

确保在末尾添加--dev，因为只有在项目开发期间才需要它。

此外还必须安装[

```
$ yarn add @babel/plugin-transform-runtime --dev 
```

并且可以自定义文件“`.eslintrc.js`”以适合每台计算机的风格。

在这种情况下，我们将添加:

```
{
    .
    .
    .
    parser: 'babel-eslint',
    rules: {
        'react/prop-types': [0,],
    },
} 
```

> 注:**根据所使用的 IDE，您必须启用读取“`.eslintrc.js`T2]”**

像`VS Code`这样的出版商已经把它综合起来了。

现在，如果打开文件“`src/App.js`”应标记一个错误，即使用“`jsx`”的文件应具有扩展名“`.jsx`”而不是“`.js`”。

## 创建应用程序布局

我们将使用[材料 ui](https://material-ui.com/) 作为各种组件、图标和样式的支持。

```
$ yarn add @material-ui/core 
```

修改者`App.js` por `App.jsx`。

删除`import App.css`，因为样式不会以这种方式使用。

创建 3 个容器。

1.  包含你有多少饼干信息的容器
2.  带有饼干图像的容器
3.  带有升级列表的容器

```
import React, { Component } from 'react';
import Typography from '@material-ui/core/Typography';
import Card from '@material-ui/core/Card';
import CardContent from '@material-ui/core/CardContent';
import logo from './logo.svg';

class App extends Component {
  state = {

  };

  render = () => (
    <div className="App">
      <div className="info">
        <Typography variant="subtitle1">
          Tienes X galletas.
        </Typography>
      </div>
      <div className="cookie">
        <img src={logo} alt="" />
      </div>
      <div className="upgrades">
        <Card className="card">
          <CardContent>
            <Typography className="" color="textSecondary" gutterBottom>
              +1 Cookie per click [30 cookies]
            </Typography>
          </CardContent>
        </Card>
      </div>
    </div>
  );
}

export default App; 
```

在那里我们使用材料成分——ui`Typography`、`Card`和`CardContent`。有关组件的详细信息，请访问材料-ui 页面。

如果使用运行应用程序

```
$ yarn start 
```

请注意，除了材质 ui 组件带来的少量样式外，它还没有其他样式。

要添加样式，我们需要使用-ui 材质包中包含的“`withStyles`”。

```
import React, { Component } from 'react';
import { withStyles } from '@material-ui/core/styles';
import Typography from '@material-ui/core/Typography';
import Card from '@material-ui/core/Card';
import CardContent from '@material-ui/core/CardContent';
import logo from './logo.svg';

const styles = {
  App: {
    height: '100%',
    width: '100%',
    display: 'flex',
    flexDirection: 'column',
    alignItems: 'center',
    justifyContent: 'flex-start',
  },
  info: {
    display: 'flex',
    justifyContent: 'center',
    alignItems: 'center',
  },
  cookie: {
    width: '100%',
    maxWidth: '500px',
  },
  upgrades: {
    width: '90%',
    display: 'flex',
    flexDirection: 'column',
    justifyContent: 'flex-start',
    alignItems: 'center',
  },
  card: {
    minWidth: '100%',
  },
};

class App extends Component {
  state = {

  };

  render = () => {
    const { classes } = this.props;
    return (
      <div className={classes.App}>
        <div className={classes.info}>
          <Typography variant="subtitle1">
            Tienes X galletas.
          </Typography>
        </div>
        <div className={classes.cookie}>
          <img src={logo} alt="" />
        </div>
        <div className={classes.upgrades}>
          <Card className={classes.card}>
            <CardContent>
              <Typography color="textSecondary" gutterBottom>
                +1 Cookie per click [30 cookies]
              </Typography>
            </CardContent>
          </Card>
        </div>
      </div>
    );
  };
}

export default withStyles(styles)(App); 
```

手动填写每一个‘t0’升级〖t1〗并不太方便，所以我们可以制作一个 js 文件来保存和获取‘T2’升级〖T3’。

我们创建了一个名为`upgrades.js`的文件

```
const upgrades = [
  {
    mejora: 1,
    costo: 30,
    actived: false,
  },
  {
    mejora: 2,
    costo: 100,
    actived: false,
  },
  {
    mejora: 3,
    costo: 200,
    actived: false,
  },
  {
    mejora: 4,
    costo: 300,
    actived: false,
  },
  {
    mejora: 5,
    costo: 600,
    actived: false,
  },
  {
    mejora: 6,
    costo: 800,
    actived: false,
  },
];

export default upgrades; 
```

并在`App.js`内使用

```
import React, { Component } from 'react';
import { withStyles } from '@material-ui/core/styles';
import Typography from '@material-ui/core/Typography';
import Card from '@material-ui/core/Card';
import CardContent from '@material-ui/core/CardContent';
import logo from './logo.svg';

// Importamos los upgrades
import UPGRADES from './upgrades';

const styles = {
  App: {
    height: '100%',
    width: '100%',
    display: 'flex',
    flexDirection: 'column',
    alignItems: 'center',
    justifyContent: 'flex-start',
  },
  info: {
    display: 'flex',
    justifyContent: 'center',
    alignItems: 'center',
  },
  cookie: {
    width: '100%',
    maxWidth: '500px',
  },
  upgrades: {
    width: '90%',
    display: 'flex',
    flexDirection: 'column',
    justifyContent: 'flex-start',
    alignItems: 'center',
  },
  card: {
    minWidth: '100%',
  },
  // Nuevo estilo para mostrar si ya se activó un upgrade
  activedBg: {
    backgroundColor: 'greenyellow',
  },
};

class App extends Component {
  // Agregamos el estado de los upgrades, el cual vamos a modificar para
  // actualizar si ya se activó o aún no. 
  state = {
    upgrades: [],
  };

  // Es importante utilizar componentDidMount para cargar todos los datos
  // que se van a utilizar al renderizar el componente.
  // Si se necesita cargar la información antes de renderizar, si utiliza
  // componentWillMount
  componentDidMount = () => {
    // Cargamos upgrades al estado
    this.setState({ upgrades: UPGRADES });
  };

  render = () => {
    // Es una buena práctica descomponer el estado y los props
    const { classes } = this.props;
    const { upgrades } = this.state;
    return (
      <div className={classes.App}>
        <div className={classes.info}>
          <Typography variant="subtitle1">
            Tienes X galletas.
          </Typography>
        </div>
        <div className={classes.cookie}>
          <img src={logo} alt="" />
        </div>
        <div className={classes.upgrades}>
          {/* Mapeamos los upgrades para ponerlos en su Card*/}
          {upgrades.map(upgrade => (
            <Card className={classes.card}>
              <CardContent>
                <Typography
                  className={upgrade.actived ? classes.activedBg : ''}
                  color="textSecondary"
                >
                  {`+${upgrade.mejora} Cookie per click [${upgrade.costo} cookies]`}
                </Typography>
              </CardContent>
            </Card>
          ))}
        </div>
      </div>
    );
  };
}

export default withStyles(styles)(App); 
```

## 实现状态

*   单击饼干时，按适当的量增加饼干总数。
*   当您按一下增强功能时，请增加每按一下饼干的数量
*   如果单击某项改进，且有足够数量的饼干，则从总数中减去饼干，并增加改进的成本。

根据这些需要，我们可以确定一个状态:

```
state = {
    upgrades: [],
    cookiesPerClick: 1,
    totalCookies: 0,
  }; 
```

饼干点击处理程序

```
cookieClick = (amount) => {
  const { totalCookies } = this.state;
  this.setState({ totalCookies: (amount + totalCookies) });
}; 
```

El 处理器 del 升级

```
clickMejora = (upgrade) => {
  const { totalCookies, cookiesPerClick, upgrades } = this.state;
  if (totalCookies >= upgrade.costo) {
    // findIndex es un método de los arreglos, si la condición es true, regresa el index
    const upgradeIndex = upgrades.findIndex(up => up.mejora === upgrade.mejora);
    const newCosto = Math.round(upgrade.costo * 1.15);
    // Probar que pasa si se hace: 
    // upgrades[upgradeIndex].costo = newCosto;
    upgrades[upgradeIndex] = {
      ...upgrades[upgradeIndex],
      costo: newCosto,
    };
    this.setState({
      totalCookies: (totalCookies - upgrade.costo),
      cookiesPerClick: (cookiesPerClick + upgrade.mejora),
      upgrades,
    });
  }
}; 
```

添加 onclick 事件

```
render = () => {
    const { classes } = this.props;
    const { upgrades, totalCookies, cookiesPerClick } = this.state;
    return (
      <div className={classes.App}>
        <div className={classes.info}>
          <Typography variant="subtitle1">
            {`Tienes ${totalCookies} galletas. Ratio: ${cookiesPerClick}`}
          </Typography>
        </div>
        <div
          className={classes.cookie}
          onClick={() => this.cookieClick(cookiesPerClick)}
          onKeyPress={() => {}}
          role="button"
          tabIndex="0"
        >
          <img src={logo} alt="" />
        </div>
        <div className={classes.upgrades}>
          {upgrades.map(upgrade => (
            <Card
              className={classes.card}
              key={upgrade.mejora}
              onClick={() => this.clickMejora(upgrade)}
            >
              <CardContent>
                <Typography
                  className={upgrade.actived ? classes.activedBg : ''}
                  color="textSecondary"
                >
                  {`+${upgrade.mejora} Cookie per click [${upgrade.costo} cookies]`}
                </Typography>
              </CardContent>
            </Card>
          ))}
        </div>
      </div>
    );
  }; 
```