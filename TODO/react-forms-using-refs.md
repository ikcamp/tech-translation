React provides two standard ways to grab values from `<form>` elements. The first method is to implement what are called *controlled components* (see my [blog post on the topic](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/)) and the second is to use React's `ref` property.

Controlled components are heavy duty. The defining characteristic of a controlled component is the displayed value is bound to component state. To update the value, you execute a function attached to the `onChange` event handler on the form element. The `onChange` function updates the state property, which in turn updates the form element's value.

(Before we get too far, if you just want to see the code samples for this article: [here you go!](https://github.com/lorenseanstewart/react-forms-using-refs))

Here's an example of a controlled component:

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

The value of the input is `this.state.fullName` (lines 7 and 26). The `onChange` function is `handleFullNameChange` (lines 10 - 14, and line 27).

The main advantages of controlled components are:

  1.You are set up to easily **validate** user input.
  2.You can **dynamically render other components** based on the value of the controlled component. For example, the value a user selects from a dropdown (e.g. 'dog' or 'cat') can control which other form components (e.g. a checkbox set of breeds) are rendered in the form.

The downside to controlled components is the amount of code you have to write. You need a state property to pass to the form element as `props`, and you need a function to update the value of this property.

For one form element this isn't an issue – but if you have a large, complex form (that doesn't need dynamic rendering or real-time validation), you'll find yourself writing a ton of code if you overuse controlled components.

An easier and less labor-intensive way to grab values from a form element is to use the `ref` property. Different form elements and component compositions require different strategies, so the rest of this post is divided into the following sections.

1.[Text inputs, number inputs, and selects](#react-refs-1)
2.[Passing props from child to parent](#react-refs-2)
3.[Radio sets](#react-refs-3)
4.[Checkbox sets](#react-refs-4)


###  1.Text inputs, number inputs, and selects

Text and number inputs provide the most straightforward example of using `ref`s. In the `ref` attribute of the input, add an arrow function that takes the input as an argument. I tend to name the argument the same as the element itself as seen on line 3 below:

```JSX
<input
  type="text"
  ref={input => this.fullName = input} />
```

Since it's an alias for the input element itself, you can name the argument whatever you'd like:

```JSX
<input
  type="number"
  ref={cashMoney => this.amount = cashMoney} />
```

You then take the argument and assign it to a property attached to the class's `this` keyword. The inputs (i.e. the DOM node) are now accessible as `this.fullName` and `this.amount`. The values of the inputs are accessible as `this.fullName.value` and `this.amount.value`.
The same strategy works for select elements (i.e. dropdowns).

```JSX
<select
  ref={select => this.petType = select}
  name="petType">
  <option value="cat">Cat</option>
  <option value="dog">Dog</option>
  <option value="ferret">Ferret</option>
</select>
```

The value selected is accessible as `this.petType.value`.

### 2、Passing props from child to parent

With a controlled component, getting the value from a child component to a parent is straightforward – the value already lives in the parent! It's passed down to the child. An `onChange` function is also passed down and updates the value as the user interacts with the UI.

You can see this at work in the controlled component examples in my [previous post](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/).

While the value already lives in the parent's state in controlled components, this is not so when using `ref`s. With `ref`s, the value resides in the DOM node itself, and must be communicated *up* to the parent.

To pass this value from child to parent, the parent needs to pass down a *'hook'*, if you will, to the child. The child then attaches a node to the 'hook' so the parent has access to it.

Let's look at some code before discussing this further.

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

Above you see a form component `RefForm`, and an input component called `CustomInput`. Usually, the arrow function is on the input itself, but here it's being passed down as a prop (see lines 15 and 27). Since the arrow function resides in the parent, the `this` of `this.firstName` lives in the parent.

The value of the child input is being assigned to the `this.firstName` property of the parent, so the child's value is available to the parent. Now, in the parent, `this.firstName` refers to a DOM node in the child component (i.e. the input in `CustomInput`).

Not only can the DOM node of the input be *accessed* by the parent, but the value of the node can also be *assigned* from within the parent. This is demonstrated on line 7 above. Once the form is submitted, the value of the input is set to 'Got ya!'.

This pattern is a bit mind bending, so stare at it for a while and play around with the code until it sinks in.
 
You may be better off making radios and checkboxes controlled components, but if you really want to use `refs` the next two sections are for you.

### 3. Radio sets

Unlike text and number input elements, radios come in sets. Each element in a set has the same `name` attribute, like so:

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

There are three options in the "pet" radio set – "cat", "dog", and "ferret".

Since the whole set is the object of our concern, setting a `ref` on each radio input is not ideal. And, unfortunately, there's no DOM node that encapsulates a set of radios.

Retrieving the value of the radio set can be obtained through **three steps**:
1.Set a ref on the `<form>` tag (line 20 below).
2.Extract the set of radios from the form. In this case, it is the `pet` set (line 9 below).
- A node list and a value is returned here. In this case, this node list includes three input nodes, and the value selected.
- Keep in mind that a node list looks like an array but is not, and lacks array methods. There's more on this topic in the next section.

3.Grab the value of the set using dot notation (line 13 below).

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

This works even if you are composing a form from children components. Although there's more logic in the components, the technique for getting the value from the radio set remains the same.

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

### 4.Checkbox sets

Unlike a radio set, a checkbox set may have multiple values selected. This makes extracting these values a little more complicated than extracting the value of a radio set.

Retrieving the selected values of the checkbox set can be done through these **five steps**:

1.Set a ref on the `<form>` tag (line 27 below).
2.Extract the set of checkboxes from the form. In this case, it is the `pet` set (line 9).
- A node list and a value is returned here.
- Keep in mind that a node list looks like an array but is not, and lacks array methods, which takes us to the next step...

3.Convert the node list to an array, so array methods are available (`checkboxArray` on line 12).
4.Use `Array.filter()` to grab only the checked checkboxes (`checkedCheckboxes` on line 15).
5.Use `Array.map()` to keep only the values of the checked checkboxes (`checkedCheckboxesValues` on line 19).

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
 
Using a checkbox set child component works just like the radio set example in the previous section.

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

### Conclusion

If you don't need to:

1.monitor the value of a form element in real-time (e.g. in order to render subsequent components based on user input), or
2.perform custom validation in real-time,

then using `refs` to grab data from form elements is a good bet.

The primary value of using `refs` over controlled component is that, in most cases, you will write less code. The exceptional case is that of checkbox sets (and radios to a lesser degree). For checkbox sets, the amount of code you save by using refs is minimal, so it's less clear whether to use a controlled component or `ref`s.
