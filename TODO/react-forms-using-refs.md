 * 原文地址：[React Forms: Using Refs](https://css-tricks.com/react-forms-using-refs/)
 * 原文作者：[Loren Stewart](https://github.com/lorenseanstewart)
 * 译者：[萌萌](#)
 * 校对者：[小 boy](#)


# 翻译 | React 表单： Refs 的运用

React 提供了两种从 `<form>` 元素中获取值的标准方法。第一种方法是实现所谓的**受控组件** (可以看我[博客里发表的文章](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/)) ，第二种方法是使用 React 的 `ref`  属性。

受控组件很重，被展示的值和组件的 state 绑定是它的特性。我们通过执行一个附着在 form 元素上的 `onChange` 事件句柄，来更新被展示的值。`onChange` 函数更新 state 属性，进而更新 form 元素的值。

（在看到下面的文章之前，如果你只是想看相应的示例代码：[请移步这里](https://github.com/lorenseanstewart/react-forms-using-refs)）

受控组件示例：

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
1、便于**验证**用户的输入
2、可以根据受控组件的值**动态地渲染其他组件**。例如：一个用户在下拉列表中选择的值（如“dog” 或者 “cat” ）可以控制在 form 中渲染的其他 form 组件（例如：一个设置品种的复选框）

受控组件的缺点是要写大量的代码。你需要通过 props  把 state 属性传递给 form 元素，还需要一个函数来更新这个属性的值。

对于单一表单元素来说这真的不是什么问题 —— 但是如果你需要一个庞大并且复杂的表单（不需要动态渲染或者实时验证），过度使用受控表单会让你书写成吨的代码。

从 form 元素取值的简便的方法是使用 `ref` 属性。我们用不同的方式来应对不同的 form 元素和组件结构，所以这篇文章剩下的内容分为以下几个部分。

1. [文本输入框、数字输入框和选择框](#react-refs-1)
2. [子组件通过 props 传值给父组件](#react-refs-2)
3. [ Radio 标签集合](#react-refs-3)
4. [ Checkbox 标签集合](#react-refs-4)

###  1、文本输入框、数字输入框和选择框

使用 `ref` 的最简单的例子是文本和数字 input 元素。我们在 input 的 `ref` 属性里添加一个把 input 本身作为参数的箭头函数。我喜欢把参数命名为和元素本身一样的的名字，就像下面的第三行那个样子：

```JSX
<input
  type="text"
  ref={input => this.fullName = input} />
```

由于该参数是 input 元素本身的别名，你可以随心所欲地为它命名：

```JSX
<input
  type="number"
  ref={cashMoney => this.amount = cashMoney} />
```

接着你可以拿到该参数，并将它赋值给当前 class 内 `this` 关键字上挂载的属性（译者注：这里的 class 指的是 JSX 所处的 React 组件 class）。input（例如： DOM 节点）可以通过 `this.fullName` 和 `this.amount` 来读取。它的值可以通过 `this.fullName.value` 和 `this.amount.value` 来读取。

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

通过受控组件，父组件获取子组件的值十分简单 —— 父组件中已经有这个值了（译者注：在父组件中定义）！它被传递给子组件。同时 `onChange` 方法也被传给子组件，用户通过与 UI 互动（译者注：触发 `onChange`）来更新该值。

你可以在我[上篇文章](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/)的受控组件示例中看到它是如何运行的。

虽然该值已经存在于受控组件的父组件中，但是当使用 `ref` 的时候却不是这样。使用 `ref` 的时候，该值存在于 DOM 节点自身当中，必须向上与父组件通信。

要将该值从子组件传给父组件，父组件需要向子组件传递一个 `钩子` 。然后子组件将节点挂载到  `钩子` 上， 以便父组件读取。

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

父组件不仅可以访问 input 中的 DOM 节点，还可以在父组件内给节点的值赋值。在上文的第 7 行可以看到例子。一旦表单被提交， input 的值就被设置为 “Got ya!” 。

这种方式有点让人摸不着头脑，所以请仔细揣摩并敲代码实践一下，直至完全理解。

    你可能会写出来更好的 radio 和 checkbox  受控组件，但是如果你真的想要用 `ref` ，那么接下来的两部分会帮到你。

### 3、 Radio 标签集合

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

在 “pet” radio 标签集合中有三个选项 —— “cat”、“dog” 和 “ferret”。

由于我们关心的是整个集合的元素，所以给每个单选框设置 `ref` 并不是一个好主意。遗憾的是，没有 DOM 节点是包含了 radio 集合的。

可以通过下面的**三步**来检索出 radio 集合的值：
  1、在 `form` 标签上设置 ref （下面的第20行）。
  2、从 form 中取出这个 radio 集合。然后它应该是 `pet` 集合（下面的第9行）。
- 此处返回一个节点列表和一个值。在这种情况下，这个节点列表包含三个 input 节点和被选中的值。
- 需要注意的是这个节点列表是个类数组，它没有数组的方法。在下一部分中还有更多关于这个话题的内容。
3、使用 `.` 方法来获取这个集合的值（下面的第13行）。

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {

  handleSubmit = (e) => {
    e.preventDefault();

    //  从 form 中取出节点列表
    //  它是一个类数组，没有数组的方法
    const { pet } = this.form;

    // radio 标签集合有 value 属性
    // 查看打印出来的数据
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

如果你正在用子组件写一个表单也是可行的。尽管组件中会有更多的逻辑，但是从 radio 集合中获取值的方法是不变的。

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();

    //  从 form 中取出节点列表
    //  它是一个类数组，没有数组的方法
    const { pet } = this.form;

    // radio 标签集合有 value 属性
    // 查看打印出来的数据
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

### 4、 Checkbox 标签集合

和 radio 标签集合不一样， Checkbox 标签集合可能有多个值。导致获取这些值会比获取 radio 标签集合的值难一些。

可以通过下面的**五步**来检索出 checkbox 标签集合被选中的值：

1、在 `form` 标签上设置 ref （下面的第27行）。
2、从 form 中取出这个checkbox 集合。然后它应该是 `pet` 集合（第9行）。
- 此处返回一个节点列表和一个值
- 需要注意的是这个节点列表是一个类数组，它没有数组的方法，然后我们就需要进行下面的这一步 ... 
3、把这个节点列表转换成一个数组，然后就可以使用数组的方法了（第 12 行的 `checkboxArray ` ）。
4、使用  `Array.filter()` 获取选中的 checkbox  （第 15 行的 `checkedCheckboxes ` ）。
5、使用  `Array.map()` 获取选中的 checkbox 的唯一的值（第 19 行的 `checkedCheckboxesValues ` ）

```JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();

    //  从 form 中取出节点列表
    //  它是一个类数组，没有数组的方法
    const { pet } = this.form;

    // 把节点列表转换成一个数组
    const checkboxArray = Array.prototype.slice.call(pet);

    // 仅取出被选中的 checkbox
    const checkedCheckboxes = checkboxArray.filter(input => input.checked);
    console.log('checked array:', checkedCheckboxes);

    // 使用 .map() 方法从每个被选中的 checkbox 中把值取出来
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
 
使用子组件写 checkbox 的方法和上一部分中写 radio 的方法是一样的。

``` JSX
import React, { Component } from 'react';

class RefsForm extends Component {
  handleSubmit = (e) => {
    e.preventDefault();

    //  从 form 中取出节点列表
    //  它是一个类数组，没有数组的方法
    const { pet } = this.form;

    // 把节点列表转换成一个数组
    const checkboxArray = Array.prototype.slice.call(pet);

    // 仅取出被选中的 checkbox
    const checkedCheckboxes = checkboxArray.filter(input => input.checked);
    console.log('checked array:', checkedCheckboxes);

    // 使用 .map() 方法从每个被选中的 checkbox 中把值取出来
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

大多数情况下，越过受控组件使用 `ref` 最主要的价值是会写更少的代码。 checkbox （ radio 其次）是一个特例。对于 checkbox ，使用 `ref` 省下的代码量是很少的，所以无法说是使用受控组件好还是 `ref` 好。
