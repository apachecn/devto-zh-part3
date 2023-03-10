# 在 C#中序列化 Selenium

> 原文：<https://dev.to/jessekphillips/serialize-selenium-by-in-c-51ik>

就我个人而言，我对 C#与 selenium 的绑定不太满意，在某种程度上，我想找出对大多数人有用的好贡献。我用过的一个是 inherented By 对象，它能够在 json 配置中存储定位器。

该对象允许将定位器直接解析成可用的对象。我有一些版本允许友好的名字，甚至可以组合不同的定位器(像 chain 但是在 locator string 中)。

继承对于提供一个友好的名称是至关重要的，当标准的 selenium 函数失败时，将打印这个名称(因为有人认为使用访问属性是一个好主意)。

我考虑过为标准元素交互创建动作对象。这实际上并不是最难的部分，以一种通用的方式来处理如何从那些动作中得到什么以及期望得到什么的差异可能是最难的。

```
 public class ElementIdentifier : By
    {
        [JsonProperty]
        [JsonConverter(typeof(StringEnumConverter))]
        public readonly How Type = How.XPath;
        [JsonProperty]
        public readonly string LocatorString;
        [JsonProperty]
        protected readonly string Window;
        [JsonProperty]
        protected readonly string Frame;
        protected By Locator;

        public ElementIdentifier() { }
        private ElementIdentifier(ElementIdentifier locator, string window = null, string frame = null)
        {
            Description = "";
            this.Locator = locator.Locator;
            this.LocatorString = locator.LocatorString;
            this.Type = locator.Type;
            this.Window = window;
            this.Frame = frame;
        }
        public ElementIdentifier(How t, string by)
        {
            Type = t;
            LocatorString = by;
            SetupLocator();
        }
        private ElementIdentifier(ByChained obj)
        {
            Description = "";
            Locator = obj;
            Type = How.Custom;
        }
        public ElementIdentifier Describe(string name)
        {
            if (!string.IsNullOrEmpty(name))
                Description = name;
            return this;
        }

        public ElementIdentifier PageContext(string window = null, string frame = null)
        {
            return new ElementIdentifier(this, window, frame).Describe(Description);
        }
        public ElementIdentifier PageContext(ElementIdentifier rhs)
        {
            return new ElementIdentifier(this, rhs.Window, rhs.Frame).Describe(Description);
        }

        public ElementIdentifier Context(ElementIdentifier rhs)
        {
            if (Type != rhs.Type)
                return new ElementIdentifier(new ByChained(new[] { rhs, this })).Describe(Description).PageContext(rhs);
            if (Type == How.XPath)
                return ElementIdentifier.Union(this, rhs).PageContext(rhs);
            if (Type == How.CssSelector)
                return ElementIdentifier.Union(this, rhs).PageContext(rhs);

            return new ElementIdentifier(new ByChained(new[] { rhs, this })).Describe(Description).PageContext(rhs);
        }

        public static ElementIdentifier Union(ElementIdentifier lhs, ElementIdentifier rhs)
        {
            if (null == lhs)
            {
                return rhs;
            }
            if (null == rhs)
            {
                return lhs;
            }

            if (lhs.Type == How.CssSelector && rhs.Type == How.XPath)
                throw new Exception($"Cannot combine locator {lhs.Type} with {rhs.Type}");
            if (lhs.Type == How.XPath && rhs.Type == How.CssSelector)
                throw new Exception($"Cannot combine locator {lhs.Type} with {rhs.Type}");

            How newType = How.XPath;
            if (lhs.Type == How.CssSelector || rhs.Type == How.CssSelector)
                newType = How.CssSelector;

            string newLocatorString = LocatorText(lhs, newType);
            switch (newType)
            {
                case How.CssSelector:
                    newLocatorString += ", ";
                    break;
                case How.XPath:
                    newLocatorString += " | ";
                    break;
            }

            newLocatorString += LocatorText(rhs, newType);

            return new ElementIdentifier(newType, newLocatorString);
        }

        private static string LocatorText(ElementIdentifier rhs, How newType)
        {
            string newLocatorString = string.Empty;
            switch (rhs.Type)
            {
                case How.ClassName:
                    if (newType == How.XPath)
                        newLocatorString = $"//*[@class='{rhs.LocatorString}']";
                    else
                        newLocatorString = $".{rhs.LocatorString}";
                    break;
                case How.CssSelector:
                    newLocatorString = $"{rhs.LocatorString}";
                    break;
                case How.Id:
                    if (newType == How.XPath)
                        newLocatorString = $"//*[@id='{rhs.LocatorString}']";
                    else
                        newLocatorString = $"#{rhs.LocatorString}";
                    break;
                case How.LinkText:
                    if (newType == How.XPath)
                        newLocatorString = $"//*[text()='{rhs.LocatorString}']";
                    else
                        newLocatorString = $"@value={rhs.LocatorString}";
                    break;
                case How.Name:
                    if (newType == How.XPath)
                        newLocatorString = $"//*[name()='{rhs.LocatorString}']";
                    else
                        newLocatorString = $"@name={rhs.LocatorString}";
                    break;
                case How.PartialLinkText:
                    //TODO: what is the name xpath/css
                    break;
                case How.TagName:
                    //TODO: what is the name xpath/css
                    break;
                case How.XPath:
                    newLocatorString = $"{rhs.LocatorString}";
                    break;
            }
            return newLocatorString;
        }

        //
        // Summary:
        //     Finds the first element matching the criteria.
        //
        // Parameters:
        //   context:
        //     An OpenQA.Selenium.ISearchContext object to use to search for the elements.
        //
        // Returns:
        //     The first matching OpenQA.Selenium.IWebElement on the current context.
        public override IWebElement FindElement(ISearchContext context)
        {
            return Locator.FindElement(context);
        }
        //
        // Summary:
        //     Finds all elements matching the criteria.
        //
        // Parameters:
        //   context:
        //     An OpenQA.Selenium.ISearchContext object to use to search for the elements.
        //
        // Returns:
        //     A System.Collections.ObjectModel.ReadOnlyCollection`1 of all OpenQA.Selenium.IWebElement
        //     matching the current criteria, or an empty list if nothing matches.
        public override ReadOnlyCollection<IWebElement> FindElements(ISearchContext context)
        {
            return Locator.FindElements(context);
        }

        [System.Runtime.Serialization.OnDeserialized]
        internal void OnDeserializedMethod(System.Runtime.Serialization.StreamingContext context)
        {
            if (string.IsNullOrEmpty(LocatorString)) return;
            SetupLocator();
        }

        private void SetupLocator()
        {
            switch (Type)
            {
                case How.ClassName:
                    Locator = ClassName(LocatorString);
                    break;
                case How.CssSelector:
                    Locator = CssSelector(LocatorString);
                    break;
                case How.Id:
                    Locator = Id(LocatorString);
                    break;
                case How.LinkText:
                    Locator = LinkText(LocatorString);
                    break;
                case How.Name:
                    Locator = Name(LocatorString);
                    break;
                case How.PartialLinkText:
                    Locator = PartialLinkText(LocatorString);
                    break;
                case How.TagName:
                    Locator = TagName(LocatorString);
                    break;
                case How.XPath:
                    Locator = XPath(LocatorString);
                    break;
            }

            Description = Locator?.ToString();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，这不是我今天使用的版本(更精简)，这个版本依赖于我没有包括的支持功能。如果有足够的兴趣，我应该能够把一些例子。