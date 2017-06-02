React 提供了两种从 `<form>` 元素中获取值的标准方法。第一个方法被称为*受控组件* (可以看我[博客里发表的文章](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/)) ，第二种方法是使用 React 的 `ref`  属性。

受控组件很重，受控组件的特性是显示的值和组件的 state 绑定在一起。为了更新它的值，你要执行一个附加在 form 元素上的 `onChange` 事件处理函数。`onChange` 函数更新 state 属性，进而更新 form 元素的值。

（在看到下面的文章之前，如果你只是想看这篇文章的代码示例：[点击这里！](https://github.com/lorenseanstewart/react-forms-using-refs)）

下面是一个受控组件的例子：

``` JSX
import React, { Component } from 'react';

class ControlledCompExample extends Component {
  constructor() {
    super();
    this.state = {
      fullName: ''
    }
  }
  handleFullNameChange = (e) => {
    this.setState({
      fullName: e.target.value
    })
  }
  handleSubmit = (e) => {
    e.preventDefault();
    console.log(this.state.fullName)
  }
  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <label htmlFor="fullName">Full Name</label>
          <input
            type="text"
            value={this.state.fullName}
            onChange={this.handleFullNameChange}
            name="fullName" />
          <input type="submit" value="Submit" />
        </form>
      </div>
    );
  }
}

export default ControlledCompExample;
```
input 的值是 `this.state.fullName` （在第7行和第26行）。 `onChange ` 函数是 `handleFullNameChange ` （第 10 - 14 行和第 27 行）。

受控组件最主要的优势是：
1、可以很轻松的**验证**用户的输入
2、可以根据受控组件的值**动态的渲染其他的组件**。例如：一个用户在下拉列表中选择的值（例如：“dog” 或者 “ cat” ）可以控制在 form 中渲染的其他 form 组件（例如：一个设置品种的复选框）

受控组件的缺点是要写大量的代码。你需要一个 state 属性通过 props 传递给 form 元素，还需要一个函数来更新这个属性的值。

对于一个表单元素来说这真的不是什么问题 —— 但是如果你需要一个庞大并且复杂的表单（不需要动态渲染或者实时验证），这时你将会发现如果过度使用受控表单你将会写一吨的代码。

从 form 元素获取值的一个简单而且能减少工作量的方法是用 `ref` 属性。不同的 form 元素和组件构成需要不同的方法，所以这篇文章剩下的内容分为以下几个部分。

1、[文本输入框、数字输入框和选择框](#react-refs-1)
2、[子组件通过 props 传值给父组件](#react-refs-2)
3、[一组 Radio 标签](#react-refs-3)
4、[一组 Checkbox 标签](#react-refs-4)

###  1、文本输入框、数字输入框和选择框

文本和数字输入框提供了使用 `refs` 的最简单的例子。在 input 的 `refs` 属性里，添加一个箭头函数并且把 input 作为参数。我趋向于把参数命名为和元素本身一样的的名字，就像下面的第三行所示：

```JSX
<input
  type="text"
  ref={input => this.fullName = input} />
```

由于它是 input 元素本身的别名，你可以随心所欲的来命名参数：

```JSX
<input
  type="number"
  ref={cashMoney => this.amount = cashMoney} />
```

你可以设置参数并将它分配给一个属性，然后附加到这个类的 `this` 关键字上。这个输入框（例如： DOM 节点）通过 `this.fullName` 和 `this.amount` 是可以被读取的。这个输入框的值可以通过 `this.fullName.value` 和 `this.amount.value` 读取。

选择元素也可以用相同的方法（例如：下拉列表）。

```JSX
<select
  ref={select => this.petType = select}
  name="petType">
  <option value="cat">Cat</option>
  <option value="dog">Dog</option>
  <option value="ferret">Ferret</option>
</select>
```

选择元素的值可以通过 `this.petType.value` 获取。

### 2、子组件通过 props 传值给父组件

使用受控组件，父组件获取子组件的值是很简单的 —— 值已经存在于父组件中了！它从子组件传过来。一个 `onChange` 函数也被传了过来，并且通过用户和 UI 的交互来更新该值。

你可以在我[上篇文章](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/)的受控组件示例中看到它是如何运行的。

虽然该值已经存在于受控组件的父组件中，但是当使用 `ref` 的时候却不是这样。使用 `ref` 的时候，该值存在于 DOM 节点自身当中，必须和父组件有通信。

要将该值从子组件传给父组件，父组件必须传递一个 ` hook` （如果你愿意）给子组件。然后子组件给  ` hook` 附加一个节点， 以便于父组件可以读取它。

在我们更深入的探讨之前先来看一些代码。

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();
    console.log('first name:', this.firstName.value);
    this.firstName.value = 'Got ya!';
  }
  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <CustomInput
            label={'Name'}
            firstName={input => this.firstName = input} />
          <input type="submit" value="Submit" />
        </form>
      </div>
    );
  }
}

function CustomInput(props) {
  return (
    <div>
      <label>{props.label}:</label>
      <input type="text" ref={props.firstName}/>
    </div>
  );
}

export default RefsForm;
```

通过上面的代码，可以看到一个 form 组件 `RefForm` 和一个叫做 `CustomInput `  的 input 组件。通常，箭头函数都是在 input 自身上面，但是从这（15 - 27 行）可以看到它是通过 props 传递的。由于箭头函数存在于父组件中，所以 `this.firstName` 中的 `this` 指向父组件。
 
input 子组件的值被赋给父组件的 `this.firstName` 属性，所以父组件可以获得子组件的值。现在，父组件中的 `this.firstName` 指的是子组件中的 DOM 节点（例如： `CustomInput ` 中的 input）。

父组件不仅可以访问 input 中的 DOM 节点，还可以在父组件内给节点的值赋值。在上文的第7行可以看到例子。一旦表单被提交， input 的值就被设置为 “Got ya!” 。

这种方式有点点摸不着头脑，所以仔细揣摩一会，敲一下代码直到完全理解它。

    你可能会写出来更好的 radios 和 checkboxes  受控组件，但是如果你真的想要用 `refs` ，那么接下来的两篇文章会帮到你。

### 3、一组 Radio 标签

不像 text 和 number 这类 input 元素，radio 元素是成组出现的。每组中的元素都有相同的 `name` 属性，就像这样：

```JSX
<form>
  <label>
    Cat
    <input type="radio" value="cat" name="pet" />
  </label>
  <label>
    Dog
    <input type="radio" value="dog" name="pet" />
  </label>
  <label>
    Ferret
    <input type="radio" value="ferret" name="pet" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

在一组 “pet” radio 标签中有三个选项 —— “cat”、“dog” 和 “ferret”。

由于我们关心的是整个组的元素，所以给每个 radio 设置 `ref` 并不是一个好主意。而不幸的是，并没有一个 DOM 节点是封装了一组 radios 的。

可以通过下面的**三步**来检索出一组 radio 的值：
  1、在 `form` 标签上设置 ref （下面的第20行）。
  2、从 from 中取出这组 radios 。然后它应该是一组 `pet`（下面的第9行）。
- 此处返回一个节点列表和一个值。在这种情况下，这个节点列表包含三个 input 节点和被选中的值。
- 需要注意的是这个节点列表像一个数组，但是并不是哦，而且它没有数组的方法。在下个章节中还有更多关于这个话题的内容。
3、使用 `.` 方法来获取这组的值（下面的第13行）。

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {

  handleSubmit = (e) => {
    e.preventDefault();

    //  extract the node list from the form
    //  it looks like an array, but lacks array methods
    const { pet } = this.form;

    // a set of radios has value property
    // checkout out the log for proof
    console.log(pet, pet.value);
  }

  render() {
    return (
      <div>
        <form
          onSubmit={this.handleSubmit}
          ref={form => this.form = form}>
          <label>
            Cat
            <input type="radio" value="cat" name="pet" />
          </label>
          <label>
            Dog
            <input type="radio" value="dog" name="pet" />
          </label>
          <label>
            Ferret
            <input type="radio" value="ferret" name="pet" />
          </label>
          <input type="submit" value="Submit" />
        </form>
      </div>
    );
  }
}

export default RefsForm;
```

如果你正在用子组件写一个表单也是可行的。尽管组件中会有更多的逻辑，但是从一组 radio 中获取值的方法是不变的。

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();

    //  extract the node list from the form
    //  it looks like an array, but lacks array methods
    const { pet } = this.form;

    // a set of radios has value property
    // checkout out the log for proof
    console.log(pet, pet.value);
  }

  render() {
    return (
      <div>
        <form
          onSubmit={this.handleSubmit}
          ref={form => this.form = form}>
          <RadioSet
            setName={'pet'}
            setOptions={['cat', 'dog', 'ferret']} />
          <input type="submit" value="Submit" />
        </form>
      </div>
    );
  }
}

function RadioSet(props) {
  return (
    <div>
      {props.setOptions.map(option => {
        return (
          <label
            key={option}
            style={{textTransform: 'capitalize'}}>
            {option}
            <input
              type="radio"
              value={option}
              name={props.setName} />
          </label>
        )
      })}
    </div>
  );
}

export default RefsForm;
```

### 4、一组 Checkbox 标签

和一组 radio 标签不一样，一组 Checkbox 标签可能有多个值。导致获取这些值会比获取一组 radio 的值难一些。

可以通过下面的**五步**来检索出一组 checkbox 标签被选中的值：

1、在 `form` 标签上设置 ref （下面的第27行）。
2、从 from 中取出这组 checkboxes 。然后它应该是一组 `pet` （第9行）。
- 此处返回一个节点列表和一个值
- 需要注意的是这个节点列表像一个数组，但是并不是哦，而且它没有数组的方法，然后我们就需要进行下面的这一步 ... 
3、把这个节点列表转换成一个数组，然后就可以使用数组的方法了（第 12 行的 `checkboxArray ` ）。
4、使用  `Array.filter()` 获取选中的 checkboxes  （第 15 行的 `checkedCheckboxes ` ）。
5、使用  `Array.map()` 获取选中的 checkboxes 的唯一的值（第 19 行的 `checkedCheckboxesValues ` ）

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();

    //  extract the node list from the form
    //  it looks like an array, but lacks array methods
    const { pet } = this.form;

    // convert node list to an array
    const checkboxArray = Array.prototype.slice.call(pet);

    // extract only the checked checkboxes
    const checkedCheckboxes = checkboxArray.filter(input => input.checked);
    console.log('checked array:', checkedCheckboxes);

    // use .map() to extract the value from each checked checkbox
    const checkedCheckboxesValues = checkedCheckboxes.map(input => input.value);
    console.log('checked array values:', checkedCheckboxesValues);
  }

  render() {
    return (
      <div>
        <form
          onSubmit={this.handleSubmit}
          ref={form => this.form = form}>
          <label>
            Cat
            <input type="checkbox" value="cat" name="pet" />
          </label>
          <label>
            Dog
            <input type="checkbox" value="dog" name="pet" />
          </label>
          <label>
            Ferret
            <input type="checkbox" value="ferret" name="pet" />
          </label>
          <input type="submit" value="Submit" />
        </form>
      </div>
    );
  }
}

export default RefsForm;
```
 
使用子组件写 checkbox 的方法和上一章节中写 radio 的方法是一样的。

``` JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();

    //  extract the node list from the form
    //  it looks like an array, but lacks array methods
    const { pet } = this.form;

    // convert node list to an array
    const checkboxArray = Array.prototype.slice.call(pet);

    // extract only the checked checkboxes
    const checkedCheckboxes = checkboxArray.filter(input => input.checked);
    console.log('checked array:', checkedCheckboxes);

    // use .map() to extract the value from each checked checkbox
    const checkedCheckboxesValues = checkedCheckboxes.map(input => input.value);
    console.log('checked array values:', checkedCheckboxesValues);
  }

  render() {
    return (
      <div>
        <form
          onSubmit={this.handleSubmit}
          ref={form => this.form = form}>
          <CheckboxSet
            setName={'pet'}
            setOptions={['cat', 'dog', 'ferret']} />
          <input type="submit" value="Submit" />
        </form>
      </div>
    );
  }
}

function CheckboxSet(props) {
  return (
    <div>
      {props.setOptions.map(option => {
        return (
          <label
            key={option}
            style={{textTransform: 'capitalize'}}>
            {option}
            <input
              type="checkbox"
              value={option}
              name={props.setName} />
          </label>
        )
      })}
    </div>
  );
}

export default RefsForm;
```

### 结论

如果你不需要：

1、实时监视 form 元素的值（例如：为了基于用户的输入渲染之后的组件）
2、实时执行自定义验证方法

那么使用 `ref` 方法获取 form 元素的值是一个很好的方法。

大多数情况下，越过受控组件使用 `ref` 最主要的价值是会写更少的代码。 checkbox （ radios 其次）是一个特例。对于 checkbox ，使用 `refs` 省下的代码量是很少的，所以无法说是使用受控组件好还是 `ref` 好。




    

