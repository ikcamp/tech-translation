原文链接：[Adapting JavaScript Abstractions Over Time](https://css-tricks.com/adapting-javascript-abstractions-time/)

- 译者：小溪里
- 校对者：郭华翔、苗冬青

# 调整JavaScript抽象的迭代方案

即使还没有读过我的文章《[在处理网络数据的 JavaScript 抽象的重要性](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)》，你也很有可能已经意识到代码的可维护性和可扩展性很重要，这也是介绍 `JavaScript` 抽象的目的。

为了更加清楚的说明，我们假设在 `JavaScript` 中抽象是一个模块。

一个模块的最初实现只是它们漫长（也许是持久的）的生命周期过程的开始。我将一个模块的生命周期分成 3 个重要阶段。

1. 引入模块。在项目中编写该模块或复用该模块；
2. 调整模块。随时调整模块；
3. 移除模块。

在我先前的[文章](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)中，重心放在了第一点上。而在这篇文章中，我将把重点放在第二点上。

模块更改是我经常碰到的一个难题。与引入模块相比，开发者维护和更改模块的方式对保证项目的可维护性和可拓展性是同等重要甚至是更加重要。我看过一个写得很好、抽象得很好的模块随着时间推移历经多次更改后被彻底毁了。我自己也经常是造成那种破坏性更改的其中一个。

当我说破坏性，我指的是对可维护性和可扩展性方面的破坏。我也明白，当面临项目最后交付期限的压力时，放慢速度以进行更好的修改设计并不是优先选择。


开发者做出非最优修改的原因可能有很多种，我在这里想特别强调一个：

### 以可维护的方式进行修改的技巧

这种方法让你的修改显得更专业。

让我们从一个 `API` 模块的代码示例开始。之所以选择这个示例，是因为与外部 `API` 通信是我在开始项目时定义的最基本的抽象之一。这里的想法是将所有与 `API` 相关的配置和设置（如基本 `URL`，错误处理逻辑等）存储在这个模块中.

我将编写一个设置 `API.url`、一个私有方法 `API._handleError()` 和一个公共方法  `API.get()`:

```js
class API {
  constructor() {
    this.url = 'http://whatever.api/v1/';
  }

  /**
   * API 数据获取的特有方法
   * 检查一个 HTTP 返回的状态码是否在成功的范围内
   */
  _handleError(_res) {
    return _res.ok ? _res : Promise.reject(_res.statusText);
  }

  /**
   * 获取数据
   * @return {Promise}
   */
  get(_endpoint) {
    return window.fetch(this.url + _endpoint, { method: 'GET' })
      .then(this._handleError)
      .then( res => res.json())
      .catch( error => {
        alert('So sad. There was an error.');
        throw new Error(error);
      });
  }
};
```

在这个模块中，公共方法 `API.get()` 返回一个 `Promise`。我们使用我们抽象出来的 `API`模块，而不是通过 `window.fetch()` 直接调用 `Fetch API` 。例如，获取用户信息 `API.get（'user'）`或当前天气预报 `API.get（'weather'）`。实现这个功能的重要意义在于**Fetch API与我们的代码没有紧密耦合。**


现在，我们面临一个修改！技术主管要求我们把获取远程数据的方式切换到[Axios](https://github.com/axios/axios)上。我们该如何应对呢？

在我们开始讨论方法之前，我们先来总结一下什么是不变的，什么是需要修改的：

1. 更改：在公共 `API.get()` 方法中
  - 需要修改 `axios()` 的 `window.fetch()`调用；需要再次返回一个 `Promise`， 以保持接口的一致, 好在 `Axios` 是基于 `Promise` 的，太棒了！
  - 服务器的响应的是 `JSON`。通过 `Fetch API` 并通过链式调用 `.then( res => res.json())` 语句来解析响应的数据。使用 `Axios`，服务器响应是在 `data` 属性中，我们不需要解析它。因此，我们需要将`.then`语句改为`.then（res => res.data）`。
2. 更改：在私有 `API._handleError` 方法中：
  - 在响应对象中缺少 `ok` 布尔标志，但是，还有 `statusText` 属性。我们可以通过它来串起来，如果它的值是 `OK`，那么一切将没什么问题（附注：在 `Fetch API` 中 `OK` 为 `true` 与在 `Axios` 中的 `statusText` 为 `OK` 是不一样的。但为了便于理解，为了不过于宽泛，不再引入任何高级错误处理。）
3. 不变之处：`API.url` 保持不变，我们会发现错误并以愉快的方式提醒他们。

讲解完毕！现在让我们深入应用这些修改的实际方法。

### 方法一：删除代码。编写代码。
```js
class API {
  constructor() {
    this.url = 'http://whatever.api/v1/'; // 一模一样的
  }

  _handleError(_res) {
      // DELETE: return _res.ok ? _res : Promise.reject(_res.statusText);
      return _res.statusText === 'OK' ? _res : Promise.reject(_res.statusText);
  }

  get(_endpoint) {
      // DELETE: return window.fetch(this.url + _endpoint, { method: 'GET' })
      return axios.get(this.url + _endpoint)
          .then(this._handleError)
          // DELETE: .then( res => res.json())
          .then( res => res.data)
          .catch( error => {
              alert('So sad. There was an error.');
              throw new Error(error);
          });
  }
};
```
听起来很合理。 提交、上传、合并、完成。

不过，在某些情况下，这可能不是一个好主意。想象以下情景：在切换到 `Axios` 之后，你会发现有一个功能并不适用于 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)（ `Axios` 的获取数据的方法），但之前使用 `Fetch API` 的新型浏览器工作得很好。我们现在该怎么办？

我们的技术负责人说，让我们使用旧的 `API` 实现这个特定的用例，并继续在其他地方使用 `Axios` 。你该做什么？在源代码管理历史记录中找到旧的 `API` 模块。还原。在这里和那里添加 `if` 语句。这样听起来并不太友好。

必须有一个更容易，更易于维护和可扩展的方式来进行更改！那么，下面的就是。

### 方法二：重构代码，做适配！

重构的需求马上来了！让我们重新开始，我们不再删除代码，而是让我们在另一个抽象中移动 `Fetch` 的特定逻辑，这将作为所有 `Fetch` 特定的适配器（或包装器）。

> HEY!???对于那些熟悉适配器模式（也被称为包装模式）的人来说，是的，那正是我们前进的方向！如果您对所有的细节感兴趣，请参阅这里我的介绍。

如下所示：
![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1509913814/adapters_iteb3k.png)

#### 步骤1
将跟 `Fetch` 相关的几行代码拿出来，单独抽象为一个新的方法 `FetchAdapter`。

```js
class FetchAdapter {
  _handleError(_res) {
    return _res.ok ? _res : Promise.reject(_res.statusText);
  }

  get(_endpoint) {
    return window.fetch(_endpoint, { method: 'GET' })
      .then(this._handleError)
      .then( res => res.json());
  }
};
```

#### 步骤2
重构API模块，删除 `Fetch` 相关代码，其余代码保持不变。添加 `FetchAdapter` 作为依赖（以某种方式）：

```js
class API {
  constructor(_adapter = new FetchAdapter()) {
    this.adapter = _adapter;

    this.url = 'http://whatever.api/v1/';
  }

  get(_endpoint) {
    return this.adapter.get(_endpoint)
      .catch( error => {
        alert('So sad. There was an error.');
        throw new Error(error);
      });
  }
};
```

现在情况不一样了！这种结构能让你处理各种不同的获取数据的场景（适配器）改。最后一步，你猜对了！写一个 `AxiosAdapter`！

```js
const AxiosAdapter = {
  _handleError(_res) {
    return _res.statusText === 'OK' ? _res : Promise.reject(_res.statusText);
  },

  get(_endpoint) {
    return axios.get(_endpoint)
      then(this._handleError)
      .then( res => res.data);
  }
};
```

在 `API` 模块中，将默认适配器改为 `AxiosAdapter`：

```js
class API {
  constructor(_adapter = new /*FetchAdapter()*/ AxiosAdapter()) {
    this.adapter = _adapter;

    /* ... */
  }
  /* ... */
};
```

真棒！如果我们需要在这个特定的用例中使用旧的 `API` 实现，并且在其他地方继续使用`Axios`？没问题！

```js
//不管你喜欢与否，将其导入你的模块，因为这只是一个例子。
import API from './API';
import FetchAdapter from './FetchAdapter';

//使用 AxiosAdapter（默认的）
const API = new API();
API.get('user');


// 使用FetchAdapter
const legacyAPI = new API(new FetchAdapter());
legacyAPI.get('user');
```

所以下次你需要改变你的项目时，评估下面哪种方法更有意义：

* 删除代码，编写代码。
* 重构代码，写适配器。

**总结**请根据你的场景选择性使用。如果你的代码库滥用适配器和引入太多的抽象可能会导致复杂性增加，这也是不好的。

愉快的去使用适配器吧！
