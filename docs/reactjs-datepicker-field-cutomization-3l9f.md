# reactjs 日期选取器字段自定义

> 原文：<https://dev.to/jeyanthkanagaraju/reactjs-datepicker-field-cutomization-3l9f>

嗨，

我使用 React-DatePicker 库来显示和获取日期输入。但是，我想根据需要定制和显示日期格式。下面是我的代码。

现在，它显示为“2019 年 5 月 29 日”。我想显示为“2019 年 5 月 29 日，星期三”。此外，默认情况下，它不会在日期字段的右上角显示向下箭头。可以请你让我知道，如何启用它。因此，如果用户点击箭头，它应该打开日历。

```
import React, { Component } from 'react'
import DatePicker from "react-datepicker";
import "react-datepicker/dist/react-datepicker.css";

class AddDates extends Component {
  constructor(props){
    super(props);
    this.state={
        startDate: new Date(),
    }
  }

  handleChange = () => {

  }
  render() {
    return (
        <div>
               <DatePicker
                    selected={this.state.startDate}
                    onChange={this.handleChange}
                    timeIntervals={15}
                    dateFormat="d MMMM, yyyy"
                />

        </div>
     )
   }
 }

export default AddDates 
```