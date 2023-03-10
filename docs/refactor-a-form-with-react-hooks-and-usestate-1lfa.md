# 用 React 钩子和 useState 重构表单

> 原文：<https://dev.to/damcosset/refactor-a-form-with-react-hooks-and-usestate-1lfa>

## 简介

React 钩子是我决定以后再看的东西之一。我已经读到和听到了关于它的伟大的事情，所以我后来就是现在。我有一个带有表单的组件，我认为它可以用钩子重构，所以我从它开始。从小步开始总是比较容易。

## 之前

没什么特别的，我们使用 *material-ui* 框架来创建一个对话框组件。然后我们有三个文本字段(文本输入)在里面:

```
 export default class AddItemPopup extends React.Component {

    constructor(props){
        super(props)
        this.state = {
            name: '',
            quantity: 0,
            unitCost: 0
        }
    }

    handleInputChange = e => {
        const {name, value} = e.target
        this.setState({
            [name]: value
        })
    }

    addItem = () => {
        const {name, quantity, unitCost} = this.state

        if(!name || !quantity || !unitCost) return

        this.props.saveItem(this.state)
    }

    render(){

        const {open, closePopup} = this.props
        const {name, quantity, unitCost} = this.state
        return(
            <Dialog 
                open={open}
                onClose={closePopup}>
                <DialogTitle>Add new item</DialogTitle>
                <DialogContent>
                    <TextField 
                        name='name'
                        label='Item name/Description'
                        onChange={this.handleInputChange}
                        value={name}/>
                    <TextField 
                        name='quantity'
                        label='Quantity'
                        onChange={this.handleInputChange}
                        value={quantity}/>
                    <TextField 
                        name='unitCost'
                        label='Unit Cost'
                        onChange={this.handleInputChange}
                        value={unitCost}/>
                </DialogContent>
                <DialogActions>
                    <Button onClick={closePopup} color="secondary" variant="contained">
                        Cancel
                    </Button>
                    <Button onClick={this.addItem} color="primary" variant="contained">
                            Save
                    </Button>
                </DialogActions>
            </Dialog>
        )
    }
} 
```

我在文件的顶部给你保存了导入，但是你得到了这个想法。一个类组件，有一个窗体和一个状态来跟踪窗体输入的值。现在，让我们使用 useState 钩子重写这个组件。

```
// Import the hook first
import React, {useState} from 'react'

const AddItemPopup = ({
    open, 
    closePopup,
    saveItem
}) => {

    const handleInputChange = e => {
        const {name, value} = e.target
        setValues({...values, [name]: value})
    }

    const addItem = () => {
        const {name, quantity, unitCost} = values

        if(!name || !quantity || !unitCost) return

        saveItem(values)
    }
        // Declare our state variable called values
        // Initialize with our default values

    const [values, setValues] = useState({name: '', quantity: 0, unitCost: 0})
    return(
        <Dialog 
        open={open}
        onClose={closePopup}>
        <DialogTitle>Add new item</DialogTitle>
            <DialogContent>
                <TextField 
                    name='name'
                    label='Item name/Description'
                    onChange={handleInputChange}
                    value={values.name}/>
                <TextField 
                    name='quantity'
                    label='Quantity'
                    onChange={handleInputChange}
                    value={values.quantity}/>
                <TextField 
                    name='unitCost'
                    label='Unit Cost'
                    onChange={handleInputChange}
                    value={values.unitCost}/>
            </DialogContent>
            <DialogActions>
                <Button onClick={closePopup} color="secondary" variant="contained">
                    Cancel
                </Button>
                <Button onClick={addItem} color="primary" variant="contained">
                        Save
                </Button>
            </DialogActions>
        </Dialog>
    )
}

export default AddItemPopup 
```

嘣！我们的组件现在变成了一个函数。我们做了什么:

*   useState 返回两个东西:当前状态(这里为*值*)和一个让你更新它的函数(这里为*设置值*)
*   *useState* 接受一个参数:初始状态。
*   onChange 处理函数现在使用这个 *setValues* 函数来修改组件的内部状态。正如你所看到的，变量*的值*在组件的任何地方都是可访问的。

*注意*:我们可以使用三个不同的钩子来分别更新每个输入，无论你认为哪个对你来说更可读；)