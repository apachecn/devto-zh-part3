# 将 Firebase 与 React 挂钩一起使用

> 原文：<https://dev.to/bmcmahen/using-firebase-with-react-hooks-21ap>

本教程演示了如何在 react 应用程序中使用[钩子](https://reactjs.org/docs/hooks-reference.html)来更好地集成 firebase 认证和 firestore 数据获取。在开始之前，对[钩子](https://reactjs.org/docs/hooks-intro.html)、firebase [认证](https://firebase.google.com/docs/auth/)和 [firestore](https://firebase.google.com/docs/firestore/) 有一个基本的了解是很有帮助的。最后，我们将构建我们的示例应用程序 [Julienne.app](https://github.com/bmcmahen/julienne) 中的一些钩子。

### 监控认证

使用钩子和[上下文](https://reactjs.org/docs/context.html)的组合可以很容易地在 React 应用程序中的任何地方访问用户会话。我们可以将用户会话存储在上下文中，并将该上下文传递给我们的子组件。然后，这些组件可以利用钩子来访问会话对象。

首先，创造我们的环境。

```
const userContext = React.createContext({
  user: null,
}) 
```

我们为上下文提供一个包含空会话对象的默认值。当我们使用 firebase 来监控会话的变化时，这种情况将会改变。

接下来，我们将创建一个钩子来访问我们的上下文。

```
export const useSession = () => {
  const { user } = useContext(userContext)
  return user
} 
```

最后，让我们创建一个钩子来监视 firebase 认证状态。这个钩子将创建一个状态，该状态使用一个`useState`回调来确定一个用户会话是否已经存在。回调是一种很有用的方法，只在第一次安装组件时用值初始化状态。

接下来，我们使用一个`effect`来监控认证的变化。当您使用许多 firebase 登录方法中的一种触发登录时(或者您注销)，将使用当前身份验证状态调用`onChange`函数。

最后，我们返回我们的认证状态。

```
export const useAuth = () => {
  const [state, setState] = React.useState(() => { const user = firebase.auth().currentUser return { initializing: !user, user, } })
  function onChange(user) {
    setState({ initializing: false, user })
  }

  React.useEffect(() => {
    // listen for auth state changes
    const unsubscribe = firebase.auth().onAuthStateChanged(onChange)
    // unsubscribe to the listener when unmounting
    return () => unsubscribe()
  }, [])

  return state
} 
```

然后，我们可以在应用程序的顶层使用这个钩子，并使用我们的上下文提供者为子组件提供用户会话。

```
function App() {
  const { initializing, user } = useAuth()
  if (initializing) {
    return <div>Loading</div>
  }

  return (
    <userContext.Provider value={{ user }}> <UserProfile /> </userContext.Provider> )
} 
```

最后，在子组件中，我们可以使用`useSession`钩子来访问我们的用户会话。

```
function UserProfile() {
  const { user } = useSession() return <div>Hello, {user.displayName}</div>
} 
```

要真正登录或退出，你真的根本不需要使用钩子。只需在事件处理程序中调用`firebase.auth().signOut()`或各种[登录方法](https://firebase.google.com/docs/auth/)。

### 抓取文档

钩子对于使用 firestore 监控单个文档查询非常有用。在这个例子中，当提供了一个`id`时，我们想要获取一个食谱。我们希望为组件提供`error`、`loading`和`recipe`状态。

```
function useRecipe(id) {
  // initialize our default state
  const [error, setError] = React.useState(false) const [loading, setLoading] = React.useState(true) const [recipe, setRecipe] = React.useState(null)
  // when the id attribute changes (including mount)
  // subscribe to the recipe document and update
  // our state when it changes.
  useEffect(
    () => {
      const unsubscribe = firebase.firestore().collection('recipes') .doc(id).onSnapshot( doc => { setLoading(false) setRecipe(doc) }, err => { setError(err) } )
      // returning the unsubscribe function will ensure that
      // we unsubscribe from document changes when our id
      // changes to a different value.
      return () => unsubscribe()
    },
    [id]
  )

  return {
    error,
    loading,
    recipe,
  }
} 
```

### 提取收藏

获取集合与此非常相似，但是我们改为订阅文档集合。

```
function useIngredients(id) {
  const [error, setError] = React.useState(false)
  const [loading, setLoading] = React.useState(true)
  const [ingredients, setIngredients] = React.useState([])

  useEffect(
    () => {
      const unsubscribe = firebase
        .firestore()
        .collection('recipes')
        .doc(id)
        .collection('ingredients') .onSnapshot( snapshot => { const ingredients = [] snapshot.forEach(doc => { ingredients.push(doc) }) setLoading(false) setIngredients(ingredients) }, err => { setError(err) } )

      return () => unsubscribe()
    },
    [id]
  )

  return {
    error,
    loading,
    ingredients,
  }
} 
```

如果你计划在你的应用程序中使用带有 firebase 的钩子，我推荐你查看一下 [react-firebase-hooks](https://github.com/CSFrequency/react-firebase-hooks) 。它提供了一些有用的助手，允许我们重用上面写的一些逻辑。

关于用 Firebase、React 和 Typescript 构建的全功能应用程序的示例，请查看 [Julienne](https://github.com/bmcmahen/julienne) 。

(这是一篇发表在我 benmcmahen.com 博客上的文章。点击这里[可以在线阅读。)](https://www.benmcmahen.com/using-firebase-with-react-hooks/)