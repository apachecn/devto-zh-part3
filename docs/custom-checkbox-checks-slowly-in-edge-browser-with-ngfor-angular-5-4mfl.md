# 自定义复选框在 edge 浏览器中缓慢检查，角度为 5°

> 原文：<https://dev.to/venishk/custom-checkbox-checks-slowly-in-edge-browser-with-ngfor-angular-5-4mfl>

我在 UI 中使用*ngFor 绑定元素。元素包含自定义复选框，该复选框根据数据示例中的条件检查值，从这个意义上来说， **item.isSelected= true** 。如果我点击复选框，我只是将选中的复选框数据更改为修改器数组中的**项。如果我在数组中改变了什么，它也会自动反映在 UI 中。因为数据被绑定到 ngFor，所以它再次尝试将数据重新绑定到 UI。但在 UI 中绑定值和选中复选框需要很长时间(只在 edge 浏览器中，其他如 chrome、firefox 做得很好)。请给我一些解决这个问题的建议。**

**也把这个问题贴在 StackOverflow 里我至今没有得到任何解决方案。有人，请建议任何解决办法。**

stackoverflow link :

[https://stack overflow . com/questions/54766100/custom-check-box-click-slow-in-edge-browser-with-ngfor-angular-5](https://stackoverflow.com/questions/54766100/custom-check-box-click-slow-in-edge-browser-with-ngfor-angular-5)