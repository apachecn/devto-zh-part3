# 让我们做一个可重复使用的 React 钩子！

> 原文：<https://dev.to/evertbouw/let-s-make-a-reusable-react-hook-e63>

我假设你已经阅读了官方的钩子文档。如果没有请做！

让我们从一个简单的组件开始:

```
const Foo = () => {
    return <div>Hello world</div>; }; 
```

我希望这个组件根据媒体查询做一些事情。这可以通过 [window.matchMedia](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) 来完成。对于本教程，我将使用媒体查询`(prefers-color-scheme: dark)`。让我们将它添加到组件:

```
const Foo = () => {
    const mediaQueryList = window.matchMedia("(prefers-color-scheme: dark)");
    // btw this query currently only works in cool browsers,
    // but dark mode is for cool people only so that's fine

    return <div>Hello {mediaQueryList.matches ? "dark" : "light"} world</div>; }; 
```

太酷了，这很有效！但是我们真的不需要在每次组件渲染时都运行查询。让我们添加第一个钩子，`useMemo`。

```
const Foo = () => {
    const mediaQueryList = useMemo(
        () => window.matchMedia("(prefers-color-scheme: dark)"),
        [],
    );

    return <div>Hello {mediaQueryList.matches ? "dark" : "light"} world</div>; }; 
```

太好了。但是媒体的询问是可以改变的。你可以旋转手机或缩小浏览器屏幕，或者在我们的情况下，你可以在操作系统的亮暗模式之间切换。

因此，让我们添加一个事件侦听器。该侦听器需要设置一些状态，以便组件使用新值重新呈现。

```
const Foo = () => {
    const mediaQueryList = useMemo(
        () => window.matchMedia("(prefers-color-scheme: dark)"),
        [],
    );

    const [matches, setMatches] = useState(mediaQueryList.matches);

    useEffect(() => {
        const listener = event => {
            setMatches(event.matches);
        };

        mediaQueryList.addEventListener("change", listener);

        return () => {
            mediaQueryList.removeEventListener("change", listener);
        };
    }, []);

    return <div>Hello {matches ? "dark" : "light"} world</div>; };

// Ryan Florence might call this 90% cleaner code,
// but this tutorial is only half way done 
```

就是这样！我们不需要添加任何其他内容。但是我们能重复使用任何东西吗？从事件和 mediaQueryList 中获取`matches`属性感觉像是重复，让我们为此创建一个函数。然后，让我们将所有连接转移到一个自定义钩子上，该钩子将查询作为一个参数。

```
const getMatches = mediaQueryList => mediaQueryList.matches;

const useMediaQuery = query => {
    const mediaQueryList = useMemo(
        () => window.matchMedia(query),
        // Let's recreate the media query list when the query changes.
        // Might be useful
        [query],
    );

    const [matches, setMatches] = useState(getMatches(mediaQueryList));

    useEffect(
        () => {
            const listener = event => {
                setMatches(getMatches(event));
            };

            mediaQueryList.addEventListener("change", listener);

            return () => {
                mediaQueryList.removeEventListener("change", listener);
            };
        },
        // if the mediaQueryList can change we'll also need to resubscribe
        // to get the correct updates
        [mediaQueryList],
    );

    // the component only cares about `matches`, so let's return it
    return matches;
};

const Foo = () => {
    const matches = useMediaQuery("(prefers-color-scheme: dark)");

    return <div>Hello {matches ? "dark" : "light"} world</div>; }; 
```

很好，我们已经创建了一个可重用的媒体查询钩子。但是我们可以更深入。如果我们将`listener`移出`useEffect`，我们可以将`useEffect`移向它自己的钩子。该钩子接受一个对象、事件名和回调函数作为参数。

```
// I can never remember the order of so many properties,
// so I put them in an object
const useEventListener = ({ eventName, listener, element }) => {
    useEffect(
        () => {
            element.addEventListener(eventName, listener);

            return () => {
                element.removeEventListener(eventName, listener);
            };
        },
        // We'll rerun the effect when any of the arguments change
        [eventName, listener, element],
    );
};

const getMatches = mediaQueryList => mediaQueryList.matches;

const useMediaQuery = query => {
    const mediaQueryList = useMemo(() => window.matchMedia(query), [query]);

    const [matches, setMatches] = useState(getMatches(mediaQueryList));

    const listener = useCallback(event => {
        // This listener is now created outside of the useEffect hook.
        // Since we are resubscribing every time this function changes
        // we'll need to useCallback
        setMatches(getMatches(event));
    }, []);

    useEventListener({
        eventName: "change",
        element: mediaQueryList,
        listener,
    });

    return matches;
}; 
```

那个钩子看起来已经很有用了。但是我可以看到我自己将`window`传递给它的次数最多，所以我将把它作为默认设置。另外，我可能不需要让监听器一直处于活动状态，但是你不能把钩子放在一个条件中。所以让我们在钩子内部添加一个条件。

```
const useEventListener = ({
    eventName,
    listener,
    element = window,
    active = true,
}) => {
    useEffect(() => {
        if (active) {
            // sneaky fix for Edge that doesn't seem to support addEventListener in mediaQueryList
            if ("addListener" in element) {
                element.addListener(listener);

                return () => {
                    element.removeListener(listener);
                };
            }

            element.addEventListener(eventName, listener);

            return () => {
                element.removeEventListener(eventName, listener);
            };
        }
    }, [eventName, listener, element, active]);
}; 
```

我们现在有了一个完全通用的`useEventListener`钩子，你再也不用写`addEventListener`了。不相信我？让我们现在就重新使用它。我喜欢惹人讨厌，所以让我们使用这个钩子来防止人们离开我的应用程序。

```
const listener = event => {
    event.preventDefault();
    event.returnValue = "";
};

const usePrompt = active =>
    useEventListener({
        eventName: "beforeunload",
        listener,
        active,
    });

// I regret nothing. 
```

我想我们可以回到我们的媒体查询挂钩，再一次把它分开。捕获事件值也是我想在媒体查询之外使用的东西。

```
const useEvent = ({
    eventName,
    getValue,
    initialState,
    element = window,
    active = true,
}) => {
    const [value, setValue] = useState(initialState);

    const listener = useCallback(
        event => {
            setValue(getValue(event));
        },
        [getValue],
    );

    useEventListener({
        eventName,
        listener,
        element,
        active,
    });

    return value;
};

const useMediaQuery = query => {
    const mediaQueryList = useMemo(() => window.matchMedia(query), [query]);

    return useEvent({
        eventName: "change",
        element: mediaQueryList,
        getValue: getMatches,
        initialState: getMatches(mediaQueryList),
    });
}; 
```

这就是我们如何重用它作为鼠标位置监听器:

```
const getMousePosition = ({ clientX, clientY }) => [clientX, clientY];

const useMousePos = () =>
    useEvent({
        eventName: "mousemove",
        getValue: getMousePosition,
        initialState: [0, 0],
    });

const Mouse = () => {
    const [x, y] = useMousePos();

    return (
        <div>
            Your mouse is at {x},{y}
        </div>
    );
}; 
```