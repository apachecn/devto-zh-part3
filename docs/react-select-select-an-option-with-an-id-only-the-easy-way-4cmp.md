# 反应-选择:预先选择一个只有 id 的选项，最简单的方法。

> 原文：<https://dev.to/fzahra/react-select-select-an-option-with-an-id-only-the-easy-way-4cmp>

我最近开发了一个应用程序，我的同事使用 React-Select 包来管理选择框。我被选择选项的方式所震惊。你必须这样指定一个对象:

`value={{value: optionId, label: optionName}}`

对于大多数人来说，这确实很不方便，因为您很可能只从数据库中获取 Id，例如从城市列表中预先选择公司所属的城市，并且只有城市 id 保存在公司对象中，即`company: {id, name, address, cityId}`

典型 ReactSelect 组件:

T2`export const Select = ({
label, isMulti, options, placeholder, onChange, value
}) => (
<FormGroup className="settings__form-group">
<Label className="form__label" for="name">
{label}
</Label>
<ReactSelect
className="modal-select form__select"
classNamePrefix="modal-select"
isMulti={isMulti}
options={options}
placeholder={placeholder}
onChange={onChange}
value={value}
/>
</FormGroup>
);`

我的解决方案是使用 defaultValue 和选项来生成值对象:

# 1:为了让事情变得整洁，我创建了一个函数来生成值对象

T2`const generateValue = (options, defaultValue, isMulti = false) => {
const value = isMulti ? options.filter(option =>
defaultValue.includes(option.value))
: options.find(option => option.value === defaultValue);
return value;
};`

# 2:我把这个函数赋给了 ReactSelect 组件的值 props。

T2`export const Select = ({
label, isMulti, options, placeholder, onChange, defaultValue
}) => (
<FormGroup className="settings__form-group">
<Label className="form__label" for="name">
{label}
</Label>
<ReactSelect
className="modal-select form__select"
classNamePrefix="modal-select"
isSearchable={false}
isMulti={isMulti}
options={options}
placeholder={placeholder}
onChange={onChange}
styles={selectStyle}
value={options.length ? generateValue(options, defaultValue, isMulti) : '' }
/>
</FormGroup>
);`

维奥拉。！！

[Github 要诀](https://gist.github.com/FatimahSanni/b37dc7437a34480747ba306985c9b827)