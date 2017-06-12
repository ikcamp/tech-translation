# React 表单：受控组件

本文涵盖以下受控组件：
- 文本输入框
- 数字输入框
- 单选框
- 复选框
- 文本域
- 下拉选择框

同时也包含:
- 表单数据的清除和重置
- 表单数据的提交
- 表单校验

> [点击这里](https://github.com/lorenseanstewart/react-controlled-form-components)直接查看示例代码。   
> 检出[示例代码](http://lorenstewart.me/react-controlled-form-components/)。    
> **请在使用示例代码时打开浏览器的控制台。**    

## 介绍

在学习 [React.js](https://facebook.github.io/react/) 时我遇到了一个问题，那就是很难找到受控组件的真实示例。受控文本输入框的例子倒是很丰富，但复选框、单选框、下拉选择框的例子却不尽人意。

本文列举了真实的受控表单组件示例，要是在学习 React 的时候我早点发现这些示例就好了。文中列举了几乎所有的表单元素，而日期和时间输入框则需要另开篇幅详细讨论。

有时候，人们很容易为了某些需求比如表单而引入框架，因为它能加速开发。而对于表单，我发现当需要添加自定义行为或表单校验时，使用框架会使事情变得更复杂。不过一旦掌握合适的 React 套路，你会发现构建表单组件并非难事，并且有些东西完全可以自己动手，丰衣足食。请把本文的示例代码当作你创建表单组件的起点或灵感之源。

除了提供单独的组件代码，我还将这些组件放进表单中，方便你理解子组件更新父组件 state 的方式，以及父组件通过 props 更新子组件的方式。

**注意：**本[表单示例](http://lorenstewart.me/react-controlled-form-components/)由不错的 [create-react-app](https://github.com/facebookincubator/create-react-app) 构建配置生成，如果你还没有安装该构建配置，我强烈推荐你安装一下（`npm install -g create-react-app`）。目前这是搭建 React 应用最简单的方式。

## 什么是受控组件?

受控组件有两个特点:
1. 受控组件提供方法，让我们在每次 `onChange` 事件发生时控制它们的数据，而不是一次性地获取表单数据（例如用户点提交按钮时）。“被控制“ 的表单数据保存在 state 中（在本文示例中，是父组件或容器组件的 state）。
2. 受控组件的展示数据是其父组件通过 props 传递下来的。

(译注：这里作者的意思是通过受控组件， 可以拿到用户操作表单的实时数据，从而更新父组件 state ，再单向渲染表单元素 UI ，其中 state 的变动是可以跟踪的。如果不使用受控组件，在用户实时操作表单时，比如在输入框输入文本时，不会同步到父组件的 state，虽然能同步输入框本身的 value，但父组件的 state 无法感知，因此只能在某一时间，比如提表单时一次性地拿到(通过 refs 或者选择器)表单数据，而不能实时控制）

这是一个单向循环 —— （数据）从（1）子组件流入到（2）父组件的 state，接着（3）通过 props 回到子组件，它是 React.js 应用架构中，单向数据流的含义。

## 表单结构

我们的顶级组件叫做 `App`，这是它的代码：

```jsx
import React, { Component } from 'react';  
import '../node_modules/spectre.css/dist/spectre.min.css';  
import './styles.css';  
import FormContainer from './containers/FormContainer';

class App extends Component {  
  render() {
    return (
      <div className="container">
        <div className="columns">
          <div className="col-md-9 centered">
            <h3>React.js Controlled Form Components</h3>
            <FormContainer />
          </div>
        </div>
      </div>
    );
  }
}

export default App;  
```

`App` 只负责渲染 `index.html` 页面。整个 `App` 组件最有趣的部分是 13 行，`FormContainer` 组件。

## 插曲: 容器（智能）组件 VS 普通（木偶）组件

是时候提及一下容器（智能）组件和普通（木偶）组件了。容器组件包含业务逻辑，它会发起数据请求或进行其他业务操作。普通组件则从它的父（容器）组件接受数据。木偶组件有可能触发更新 state 这类逻辑行为，但它仅仅是通过从父（容器）组件传入的方法来达到该目的（译注：这里还是指更新父组件的 state，通过传入方法来更新 state 本质上还是通过父组件的传参来控制子组件，子组件即 “木偶” ）。

**注意：** 虽然在我们的表单应用里父组件就是容器组件，但我要强调，并非所有的父组件都是容器组件。木偶组件嵌套木偶组件也是非常好的。

## 回到应用结构

`FormContainer` 组件包含了表单元素组件，它在 `componentDidMount` 的生命周期钩子方法里请求数据，此外还包含更新表单应用 state 的逻辑行为。在下面的预览代码里，我移除了表单元素的 props 和 change 事件句柄，这样看起来更简洁清晰（拉到文章底部，可以看到完整代码）。

```jsx
import React, {Component} from 'react';  
import CheckboxOrRadioGroup from '../components/CheckboxOrRadioGroup';  
import SingleInput from '../components/SingleInput';  
import TextArea from '../components/TextArea';  
import Select from '../components/Select';

class FormContainer extends Component {  
  constructor(props) {
    super(props);
    this.handleFormSubmit = this.handleFormSubmit.bind(this);
    this.handleClearForm = this.handleClearForm.bind(this);
  }
  componentDidMount() {
    fetch('./fake_db.json')
      .then(res => res.json())
      .then(data => {
        this.setState({
          ownerName: data.ownerName,
          petSelections: data.petSelections,
          selectedPets: data.selectedPets,
          ageOptions: data.ageOptions,
          ownerAgeRangeSelection: data.ownerAgeRangeSelection,
          siblingOptions: data.siblingOptions,
          siblingSelection: data.siblingSelection,
          currentPetCount: data.currentPetCount,
          description: data.description
        });
    });
  }
  handleFormSubmit() {
    // submit logic goes here
  }
  handleClearForm() {
    // clear form logic goes here
  }
  render() {
    return (
      <form className="container" onSubmit={this.handleFormSubmit}>
        <h5>Pet Adoption Form</h5>
        <SingleInput /> {/* Full name text input */}
        <Select /> {/* Owner age range select */}
        <CheckboxOrRadioGroup /> {/* Pet type checkboxes */}
        <CheckboxOrRadioGroup /> {/* Will you adopt siblings? radios */}
        <SingleInput /> {/* Number of current pets number input */}
        <TextArea /> {/* Descriptions of current pets textarea */}
        <input
          type="submit"
          className="btn btn-primary float-right"
          value="Submit"/>
        <button
          className="btn btn-link float-left"
          onClick={this.handleClearForm}>Clear form</button>
      </form>
  );
}
```

我们勾勒出了应用基础结构，接下来我们一起浏览下每个子组件的细节。

## `<SingleInput />` 组件

该组件可以是 `text` 或 `number` 输入框，这取决于你传入的 props。通过 React 的 PropTypes，我们可以非常好地记录组件拿到的 props。如果漏传 props 或传入错误的数据类型, 浏览器的控制台中会出现警告信息。

下面列举 `<SingleInput />` 组件的 PropTypes：

```js
SingleInput.propTypes = {  
  inputType: React.PropTypes.oneOf(['text', 'number']).isRequired,
  title: React.PropTypes.string.isRequired,
  name: React.PropTypes.string.isRequired,
  controlFunc: React.PropTypes.func.isRequired,
  content: React.PropTypes.oneOfType([
    React.PropTypes.string,
    React.PropTypes.number,
  ]).isRequired,
  placeholder: React.PropTypes.string,
};
```

PropTypes 声明了 prop 的类型（string、 number、 array、 object 等等），其中包括了必需（`isRequired）和非必需的 prop，当然它还有更多的用途（欲知更多细节，请查看 [React 文档](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)）。

下面我们逐个讨论这些 PropType：

1. `inputType` 接收两个字符串： `'text'` 或 `'number'`。该设置指定了渲染 `<input type="text" />` 组件还是 `<input type="number" />` 组件。
2. `title`： 接收一个字符串，我们将它渲染到输入框的 label 元素中。
3. `name`： 输入框的 name 属性。
4. `controlFunc`：它是从父组件或容器组件传下来的方法。因为该方法挂载在 React 的 onChange 句柄上，所以每当 input 输入值改变时，该方法都会被执行，从而更新父组件或容器组件的 state。
5. `content`: 输入框内容。受控输入框只会显示通过 props 传入的数据。
6. `placeholder`: 输入框的占位符文本，是一个字符串。

既然该组件不需要任何逻辑行为和内部 state，那我们可以将它写成纯函数组件（pure functional component）。纯函数组件被挂载到一个 `const` 常量上。下面是 `<SingleInput />` 组件的所有代码。本文列举的所有表单元素组件都是纯函数组件。

```jsx
import React from 'react';

const SingleInput = (props) => (  
  <div className="form-group">
    <label className="form-label">{props.title}</label>
    <input
      className="form-input"
      name={props.name}
      type={props.inputType}
      value={props.content}
      onChange={props.controlFunc}
      placeholder={props.placeholder} />
  </div>
);

SingleInput.propTypes = {  
  inputType: React.PropTypes.oneOf(['text', 'number']).isRequired,
  title: React.PropTypes.string.isRequired,
  name: React.PropTypes.string.isRequired,
  controlFunc: React.PropTypes.func.isRequired,
  content: React.PropTypes.oneOfType([
    React.PropTypes.string,
    React.PropTypes.number,
  ]).isRequired,
  placeholder: React.PropTypes.string,
};

export default SingleInput;  
```

接着，我们用 `handleFullNameChange` 方法（它被传入到 `controlFunc` prop 属性）来更新 `<FormContainer />` 容器组件的 state。

```js
// FormContainer.js

handleFullNameChange(e) {  
  this.setState({ ownerName: e.target.value });
}
// make sure to have:
// this.handleFullNameChange = this.handleFullNameChange.bind(this);
// in the constructor
```

随后我们将容器组件更新后的 state （译注：这里指 state 上挂载的 ownerName 属性）通过 `content` prop 传回 `<SingleInput />` 组件。

## `<Select />` 组件

选择组件（例如下拉选择组件），接收以下 props：

```jsx
Select.propTypes = {  
  name: React.PropTypes.string.isRequired,
  options: React.PropTypes.array.isRequired,
  selectedOption: React.PropTypes.string,
  controlFunc: React.PropTypes.func.isRequired,
  placeholder: React.PropTypes.string
};
```

1. `name`：填充表单元素上 `name` 属性的一个字符串变量。
2. `options`：是一个数组（本例是字符串数组）。通过在组件的 render 方法中使用 `props.options.map()`， 该数组中的每一项都会被渲染成一个选择项。
3. `selectedOption`: 用以显示表单填充的默认选项，或用户已选择的选项（例如当用户编辑之前已提交过的表单数据时，可以使用这个 prop）。
4. `controlFunc`：它是从父组件或容器组件传下来的方法。因为该方法挂载在 React 的 onChange 句柄上，所以每当改变选择框组件的值时，该方法都会被执行，从而更新父组件或容器组件的 state。
5. `placeholder`：作为占位文本的字符串，用来填充第一个 `<option>` 标签。本组件中，我们将第一个选项的值设置成空字符串（参看下面代码的第10行）。

```jsx
import React from 'react';

const Select = (props) => (  
  <div className="form-group">
    <select
      name={props.name}
      value={props.selectedOption}
      onChange={props.controlFunc}
      className="form-select">
      <option value="">{props.placeholder}</option>
      {props.options.map(opt => {
        return (
          <option
            key={opt}
            value={opt}>{opt}</option>
        );
      })}
    </select>
  </div>
);

Select.propTypes = {  
  name: React.PropTypes.string.isRequired,
  options: React.PropTypes.array.isRequired,
  selectedOption: React.PropTypes.string,
  controlFunc: React.PropTypes.func.isRequired,
  placeholder: React.PropTypes.string
};

export default Select;  
```

请注意 option 标签中的 `key` 属性（第14行）。React 要求被循环操作渲染的每个元素必须拥有独一无二的 `key` 值，我们这里的 `.map()` 方法就是所谓的循环操作。既然选择项数组中的每个元素是独有的，我们就把它们当成 `key` prop。该 `key` 值协助 React 追踪 DOM 变化。虽然在循环操作或 mapping 时忘加 `key` 属性不会中断应用，但是浏览器的控制台里会出现警告，并且渲染性能将受到影响。

以下是控制选择框组件（记住，该组件存在于 `<FormContainer />` 组件中）的句柄方法（该方法从 `<FormContainer />` 组件传入到子组件的 `controlFun` prop 中）

```jsx
// FormContainer.js

handleAgeRangeSelect(e) {  
  this.setState({ ownerAgeRangeSelection: e.target.value });
}
// make sure to have:
// this.handleAgeRangeSelect = this.handleAgeRangeSelect.bind(this);
// in the constructor
```

## `<CheckboxOrRadioGroup />` 组件

`<CheckboxOrRadioGroup />` 与众不同， 它从 props 拿到传入的数组（像此前 `<Select />` 组件的选项数组一样），通过遍历数组来渲染一组表单元素的集合 —— 可以是复选框集合或单选框集合。

让我们深入 PropTypes 来更好地理解 `<CheckboxOrRadioGroup />` 组件。

```jsx
CheckboxGroup.propTypes = {  
  title: React.PropTypes.string.isRequired,
  type: React.PropTypes.oneOf(['checkbox', 'radio']).isRequired,
  setName: React.PropTypes.string.isRequired,
  options: React.PropTypes.array.isRequired,
  selectedOptions: React.PropTypes.array,
  controlFunc: React.PropTypes.func.isRequired
};
```

1. `title`：一个字符串，用以填充单选或复选框集合的 label 标签内容。
2. `type`：接收 `'checkbox'` 或 `'radio'` 两种配置的一种，并用指定的配置渲染输入框（译注：这里指复选输入框或单选输入框）。
3. `setName`：一个字符串，用以填充每个单选或复选框的 `name` 属性值。
4. `options`：一个由字符串元素组成的数组，数组元素用以渲染每个单选框或复选框的值和 label 的内容。例如，`['dog', 'cat', 'pony']` 数组中的元素将会渲染三个单选框或复选框。
5. `selectedOptions`：一个由字符串元素组成的数组，用来表示预选项。在示例 4 中，如果 `selectedOptions` 数组包含 `'dog'` 和 `'pony'` 元素，那么相应的两个选项会被渲染成选中状态，而 `'cat'` 选项则被渲染成未选中状态。当用户提交表单时，该数组将会是用户的选择数据。
6. `controlFunc`：一个方法，用来处理从 `selectedOptions` 数组 prop 中添加或删除字符串的操作。

这是本表单应用中最有趣的组件，让我们来看一下：

```jsx
import React from 'react';

const CheckboxOrRadioGroup = (props) => (  
  <div>
    <label className="form-label">{props.title}</label>
    <div className="checkbox-group">
      {props.options.map(opt => {
        return (
          <label key={opt} className="form-label capitalize">
            <input
              className="form-checkbox"
              name={props.setName}
              onChange={props.controlFunc}
              value={opt}
              checked={ props.selectedOptions.indexOf(opt) > -1 }
              type={props.type} /> {opt}
          </label>
        );
      })}
    </div>
  </div>
);

CheckboxOrRadioGroup.propTypes = {  
  title: React.PropTypes.string.isRequired,
  type: React.PropTypes.oneOf(['checkbox', 'radio']).isRequired,
  setName: React.PropTypes.string.isRequired,
  options: React.PropTypes.array.isRequired,
  selectedOptions: React.PropTypes.array,
  controlFunc: React.PropTypes.func.isRequired
};

export default CheckboxOrRadioGroup;  
```
`checked={ props.selectedOptions.indexOf(option) > -1 }` 这一行代码表示单选框或复选框是否被选中的逻辑。

属性 `checked` 接收一个布尔值，用来表示 input 组是否应该被渲染成选中状态。我们在检查到 input 的值是否是 `props.selectedOptions` 数组的元素之一时生成该布尔值。
`myArray.indexOf(item)` 方法返回 item 在数组中的索引值。如果 item 不在数组中，返回 `-1`，因此，我们写了 `> -1`。

注意，`0` 是一个合法的索引值，所以我们需要 `> -1` ，否则代码会有 bug。如果没有 `> -1`，`selectedOptions` 数组中的第一个 item —— 其索引为 0 —— 将永远不会被渲染成选中状态，因为 `0` 是一个类 `false` 的值（译注：在 `checked` 属性中，`0` 会被当成 `false` 处理）。

本组件的句柄方法同样比其他的有趣。

```jsx
handlePetSelection(e) {

  const newSelection = e.target.value;
  let newSelectionArray;

  if(this.state.selectedPets.indexOf(newSelection) > -1) {
    newSelectionArray = this.state.selectedPets.filter(s => s !== newSelection)
  } else {
    newSelectionArray = [...this.state.selectedPets, newSelection];
  }

    this.setState({ selectedPets: newSelectionArray });
}
```

如同所有句柄方法一样，事件对象被传入方法，这样一来我们就能拿到事件对象的值（译注：准确来说，应该是事件目标元素的值）。我们将该值赋给`newSelection` 常量。接着我们在方法顶部附近定义 `newSelectionArray` 变量。因为我们将在一个 `if/else` 代码块里对该变量进行赋值，所以用 `let` 而非 `const` 来定义它。我们在代码块外部进行定义，这样一来被定义变量的作用域就是方法内部的最外沿，并且方法内的代码块都能访问到外部定义的变量。

该方法需要处理两种可能的情况。

如果 input 组件的值**不在** `selectedOptions` 数组中，我们要将值添加进该数组。
如果 input 组件的值**在** `selectedOptions` 数组中，我们要从数组中删除该值。

`添加（第 8 - 10 行）：` 为了将新值添加进选项数组，我们通过解构旧数组（数组前的三点`...`表示解构）创建一个新数组，并且将新值添加到数组的尾部 `newSelectionArray = [...this.state.selectedPets, newSelection];`。

注意，我们创建了一个新数组，而不是通过类似 `.push()` 的方法来改变原数组。不改变已存在的对像和数组，而是创建新的对象和数组，这在 React 中是又一个最佳实践。开发者这样做可以更容易地追逐 state 的变化，而第三方 state 管理库，如 Redux 则可以做高性能的浅检查，而不是阻塞性能的深检查。

删除（第 6 - 8 行）：`if` 代码块借助此前用到的 `.indexOf()` 小技巧，检查选项是否在数组中。如果选项已经在数组中，通过 `.filter()`. 方法，该选项将被移除。 该方法返回一个包含所有满足 filter 条件的元素的新数组（记住要避免在 React 直接修改数组或对象！）。

```js
newSelectionArray = this.state.selectedPets.filter(s => s !== newSelection)  
```

在这种情况下，除了传入到方法中的选项之外，其他选项都会被返回。

## `<TextArea />` 组件

The `<TextArea />` component is very similar to the components covered already. Its props should be familiar by now, with the exception of `resize` and `rows`.
`<TextArea />` 组件和我们已提到的那些非常相似，除了 `resize` 和 `rows`，目前你应该对它的 prop 很熟悉了。

```jsx
TextArea.propTypes = {  
  title: React.PropTypes.string.isRequired,
  rows: React.PropTypes.number.isRequired,
  name: React.PropTypes.string.isRequired,
  content: React.PropTypes.string.isRequired,
  resize: React.PropTypes.bool,
  placeholder: React.PropTypes.string,
  controlFunc: React.PropTypes.func.isRequired
};
```

1. `title`：接收一个字符串，用以渲染文本域的 label 标签内容。
2. `rows`：接收一个整数，用来指定文本域的行数。
3. `name`：文本域的 name 属性。
4. `content`：文本域的内容。受控组件只会显示通过 props 传入的数据。
5. `resize`： 接受一个布尔值，用来指定文本域能否调整大小。
6. `placeholder`：充当文本域占位文本的字符串。
7. `controlFunc`： 它是从父组件或容器组件传下来的方法。因为该方法挂载在 React 的 onChange 句柄上，所以每当改变选择框组件的值时，该方法都会被执行，从而更新父组件或容器组件的 state。

`<TextArea />` 组件的完整代码：

```jsx
import React from 'react';

const TextArea = (props) => (  
  <div className="form-group">
    <label className="form-label">{props.title}</label>
    <textarea
      className="form-input"
      style={props.resize ? null : {resize: 'none'}}
      name={props.name}
      rows={props.rows}
      value={props.content}
      onChange={props.controlFunc}
      placeholder={props.placeholder} />
  </div>
);

TextArea.propTypes = {  
  title: React.PropTypes.string.isRequired,
  rows: React.PropTypes.number.isRequired,
  name: React.PropTypes.string.isRequired,
  content: React.PropTypes.string.isRequired,
  resize: React.PropTypes.bool,
  placeholder: React.PropTypes.string,
  controlFunc: React.PropTypes.func.isRequired
};

export default TextArea;  
```

`<TextAreas />` 组件的控制方法和 `<SingleInput />` 如出一辙。细节部分请参考 `<SingleInput />` 组件。

## 表单操作

`handleClearForm` 和 `handleFormSubmit` 方法操作整个表单。

#### 1. handleClearForm

Since we are using unidirectional data flow throughout our form, clearing the form's options is a breeze. Each value of each element is controlled by the state of the `<FormContainer />`. The container's state is passed into the child components via props. The values being displayed by the form components change only when the `<FormContainer />`'s state changes.

Clearing the data displayed in the form's child components is as easy as setting the container's state to empty arrays and empty strings (and `0` in the case of our number input).

```jsx
handleClearForm(e) {  
  e.preventDefault();
  this.setState({
    ownerName: '',
    selectedPets: [],
    ownerAgeRangeSelection: '',
    siblingSelection: [],
    currentPetCount: 0,
    description: ''
  });
}
```

Voilà! `e.preventDefault()` prevents the page from reloading, and the `setState()` function clears the form.

#### 2. handleFormSubmit

In order to submit this form's data, we construct an object out of the appropriate state properties. Then use an AJAX library or technique to send this data to an API (which is not covered in this post).

```jsx
handleFormSubmit(e) {  
  e.preventDefault();

  const formPayload = {
    ownerName: this.state.ownerName,
    selectedPets: this.state.selectedPets,
    ownerAgeRangeSelection: this.state.ownerAgeRangeSelection,
    siblingSelection: this.state.siblingSelection,
    currentPetCount: this.state.currentPetCount,
    description: this.state.description
  };

  console.log('Send this in a POST request:', formPayload);
  this.handleClearForm(e);
}
```
Notice that the form is cleared after submitting, by invoking `this.handleClearForm(e)`.

## Validation

Controlled form components are a great foundation for custom validation. Suppose you would like to exclude the letter 'e' from the `<TextArea />` component.

```jsx
handleDescriptionChange(e) {  
  const textArray = e.target.value.split('').filter(x => x !== 'e');

  console.log('string split into array of letters',textArray);

  const filteredText = textArray.join('');
  this.setState({ description: filteredText });
}
```

The `textArray` above is created by splitting the string `e.target.value` into an array of individual letters. Then the letter 'e' (or whatever character you would like to exclude) is filtered out. The array of letters is joined again, and the new string is set to component state. Not to bad!

This code above is in [the repo for this post](https://github.com/lorenseanstewart/react-controlled-form-components), but commented out, so feel free to tweak it meet your own purposes.

## `<FormContainer />`

As promised, here is the complete code for the `<FormContainer />` component:

```jsx
import React, {Component} from 'react';  
import CheckboxOrRadioGroup from '../components/CheckboxOrRadioGroup';  
import SingleInput from '../components/SingleInput';  
import TextArea from '../components/TextArea';  
import Select from '../components/Select';

class FormContainer extends Component {  
  constructor(props) {
    super(props);
    this.state = {
      ownerName: '',
      petSelections: [],
      selectedPets: [],
      ageOptions: [],
      ownerAgeRangeSelection: '',
      siblingOptions: [],
      siblingSelection: [],
      currentPetCount: 0,
      description: ''
    };
    this.handleFormSubmit = this.handleFormSubmit.bind(this);
    this.handleClearForm = this.handleClearForm.bind(this);
    this.handleFullNameChange = this.handleFullNameChange.bind(this);
    this.handleCurrentPetCountChange = this.handleCurrentPetCountChange.bind(this);
    this.handleAgeRangeSelect = this.handleAgeRangeSelect.bind(this);
    this.handlePetSelection = this.handlePetSelection.bind(this);
    this.handleSiblingsSelection = this.handleSiblingsSelection.bind(this);
    this.handleDescriptionChange = this.handleDescriptionChange.bind(this);
  }
  componentDidMount() {
    // simulating a call to retrieve user data
    // (create-react-app comes with fetch polyfills!)
    fetch('./fake_db.json')
      .then(res => res.json())
      .then(data => {
        this.setState({
          ownerName: data.ownerName,
          petSelections: data.petSelections,
          selectedPets: data.selectedPets,
          ageOptions: data.ageOptions,
          ownerAgeRangeSelection: data.ownerAgeRangeSelection,
          siblingOptions: data.siblingOptions,
          siblingSelection: data.siblingSelection,
          currentPetCount: data.currentPetCount,
          description: data.description
        });
      });
  }
  handleFullNameChange(e) {
    this.setState({ ownerName: e.target.value });
  }
  handleCurrentPetCountChange(e) {
    this.setState({ currentPetCount: e.target.value });
  }
  handleAgeRangeSelect(e) {
    this.setState({ ownerAgeRangeSelection: e.target.value });
  }
  handlePetSelection(e) {
    const newSelection = e.target.value;
    let newSelectionArray;
    if(this.state.selectedPets.indexOf(newSelection) > -1) {
      newSelectionArray = this.state.selectedPets.filter(s => s !== newSelection)
    } else {
      newSelectionArray = [...this.state.selectedPets, newSelection];
    }
    this.setState({ selectedPets: newSelectionArray });
  }
  handleSiblingsSelection(e) {
    this.setState({ siblingSelection: [e.target.value] });
  }
  handleDescriptionChange(e) {
    this.setState({ description: e.target.value });
  }
  handleClearForm(e) {
    e.preventDefault();
    this.setState({
      ownerName: '',
      selectedPets: [],
      ownerAgeRangeSelection: '',
      siblingSelection: [],
      currentPetCount: 0,
      description: ''
    });
  }
  handleFormSubmit(e) {
    e.preventDefault();

    const formPayload = {
      ownerName: this.state.ownerName,
      selectedPets: this.state.selectedPets,
      ownerAgeRangeSelection: this.state.ownerAgeRangeSelection,
      siblingSelection: this.state.siblingSelection,
      currentPetCount: this.state.currentPetCount,
      description: this.state.description
    };

    console.log('Send this in a POST request:', formPayload)
    this.handleClearForm(e);
  }
  render() {
    return (
      <form className="container" onSubmit={this.handleFormSubmit}>
        <h5>Pet Adoption Form</h5>
        <SingleInput
          inputType={'text'}
          title={'Full name'}
          name={'name'}
          controlFunc={this.handleFullNameChange}
          content={this.state.ownerName}
          placeholder={'Type first and last name here'} />
        <Select
          name={'ageRange'}
          placeholder={'Choose your age range'}
          controlFunc={this.handleAgeRangeSelect}
          options={this.state.ageOptions}
          selectedOption={this.state.ownerAgeRangeSelection} />
        <CheckboxOrRadioGroup
          title={'Which kinds of pets would you like to adopt?'}
          setName={'pets'}
          type={'checkbox'}
          controlFunc={this.handlePetSelection}
          options={this.state.petSelections}
          selectedOptions={this.state.selectedPets} />
        <CheckboxOrRadioGroup
          title={'Are you willing to adopt more than one pet if we have siblings for adoption?'}
          setName={'siblings'}
          controlFunc={this.handleSiblingsSelection}
          type={'radio'}
          options={this.state.siblingOptions}
          selectedOptions={this.state.siblingSelection} />
        <SingleInput
          inputType={'number'}
          title={'How many pets do you currently own?'}
          name={'currentPetCount'}
          controlFunc={this.handleCurrentPetCountChange}
          content={this.state.currentPetCount}
          placeholder={'Enter number of current pets'} />
        <TextArea
          title={'If you currently own pets, please write their names, breeds, and an outline of their personalities.'}
          rows={5}
          resize={false}
          content={this.state.description}
          name={'currentPetInfo'}
          controlFunc={this.handleDescriptionChange}
          placeholder={'Please be thorough in your descriptions'} />
        <input
          type="submit"
          className="btn btn-primary float-right"
          value="Submit"/>
        <button
          className="btn btn-link float-left"
          onClick={this.handleClearForm}>Clear form</button>
      </form>
    );
  }
}

export default FormContainer;
```
## Conclusion

Admittedly, building controlled form components with React requires some repetition (e.g, the handler functions in the container), but the control you have over your app and the transparency of state change is well worth the up-front effort. Your code will be maintainable, and very performant.

If you'd like to be notified when I publish a new post, you can sign up for my mailing list in the navbar of the blog.

