原文链接：[Adapting JavaScript Abstractions Over Time](https://css-tricks.com/adapting-javascript-abstractions-time/)

- 译者：小溪里
- 校对者：？？？


即使你还没有读过我的文章《[在处理理网络数据的 JavaScript 抽象的重要性](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)》，很有可能你已经意识到在你的项目中可维护性和可扩展性很重要，这也是介绍 `JavaScript`抽象的目的。

为了介绍这篇文章，我们假设在 `JavaScript` 中抽象是一个模块。

一个模块的最初实现只是它们漫长（也许是持久的）的生命周期过程的开始。我将一个模块的生命周期分成 3 个事件。

1. 模块介绍。项目中重复使用它的最初实现和过程；
2. 调整模块。随着时间推移随时调整模块；
3. 移除模块。

在我先前的[文章](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)中，重心放在了第一点上。而在这篇文章中，我将在第二点上引入更多思考。

处理对模块中的更改是我经常碰到的一个难题。与引入模块相比，开发者维护和更改模块的方式对保证项目的可维护性和可拓展性是同等重要甚至是更加重要。我看过一个写得很好、抽象得很好的模块随着时间推移历经多次更改后被彻底毁了。我自己也经常是造成那种毁灭性更改的其中一个。

当我说毁灭性，我意思是可维护性和可扩展性角度上的毁灭。我也明白，从接近工作的最后期限和必须释放资源的意义上，放慢对你所有可能性的变化的思考都不是一种优先方案。


开发者做出的更改也许不是最理想的理由可能有无数种，我在这里想特别强调一个：

### 以可维护的方式进行修改的技巧
这是一种可以像专业人员一样进行修改的方法。

从示例--一个API模块开始讲解。之所以选择这个示例，是因为与外部API通信是我在开始项目时定义的最基本的概念之一。这个方法是将所有与API相关的配置和设置（如基本URL，错误处理逻辑等）存储在这个模块中.

我将介绍一个设置: `API.url`，一个私有方法 :`API._handleError（）`和一个公共方法: `API.get（）`:

```js
class API {
  constructor() {
    this.url = 'http://whatever.api/v1/';
  }

  /**
   * Fetch API's specific way to check
   * whether an HTTP response's status code is in the successful range.
   * API 数据获取的特有方法
   * 检查一个 HTTP 返回的状态码是否在成功的范围内
   */
  _handleError(_res) {
      return _res.ok ? _res : Promise.reject(_res.statusText);
  }

  /**
   * 获取数据的抽象
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
在这个示例中，公共方法 `API.get（）`返回一个 `Promise`。使用 `API` 模块，在所有需要获取远程数据的地方，直接调用 `Fetch API`即可， 无需调用 `window.fetch（）`。例如获取用户信息 `API.get（'user'）`或当前天气预报  `API.get（'weather'）`。实现这个功能的重要之处在于**Fetch API与我们的代码没有紧密耦合。**

现在，让我们说说修改的要求！当技术主管要求我们切换到另一种获取远程数据的方法时。我们需要切换到[Axios](https://github.com/axios/axios)。我们应当如何应对？

在我们开始讨论方法之前，我们先来总结一下什么是不变的，什么是需要修改的：

1. 在公共 `API.get（）`方法中：
  - 需要修改 `axios（）`的 `window.fetch（）`调用;需要再次返回一个Promise，来保持二者之间可以保持同步，Axios是Promise的基础。
  - 服务器的响应是JSON。通过Fetch API链，用`（res => res.json（））`语句来解析响应数据。使用Axios，服务器的响应在`data`属性下，我们不需要解析它。因此，我们需要将`.then`语句改为`.then（res => res.data）`。
2. 在私有 `API._handleError` 方法中：
  - 对象响应中缺少 `ok` 布尔标志;但是，还有 `statusText` 属性。如果它的值是`OK`，我们可以将其连接起来。（附注：在 Fetch API 中 `OK` 等于 `true` 与在Axios中的 `statusText` 具有 `OK` 是不一样的。但为了便于理解，所以尽量保持简单，不再引入任何高级错误处理。）
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
听起来很合理。 发布、上传、合并、完成。

不过，在某些情况下，这可能不是一个好主意。想象一下，发生以下情况：在切换到 `Axios` 之后，您会发现有一个功能不适用于 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)（ Axios 的获取资源方法的界面），但之前使用 Fetch 的新型浏览器 API 工作得很好。我们现在该干什么？

我们的技术负责人说，让我们使用旧的 `API` 实现这个特定的用例，并继续在其他地方使用 `Axios` 。你该做什么？在源代码管理历史记录中找到旧的 `API` 模块。还原。在这里和那里添加 `if` 语句。这样听起来并不太友好。

必须有一个更容易，更易于维护和可扩展的方式来进行更改！那么，下面的就是。

### 方法二：重构代码。写适配器！

有一个马上到来的变更请求！让我们重新开始，而不是删除代码，让我们在另一个抽象中移动 `Fetch` 的特定逻辑，这将作为所有 `Fetch` 特定的适配器（或包装器）。

> HEY!???对于那些熟悉适配器模式（也被称为包装模式）的人来说，是的，那正是我们前进的方向！如果您对所有的细节感兴趣，请参阅这里我的介绍。

计划如下：
![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1509913814/adapters_iteb3k.png)

#### 步骤1
从 `API` 模块中获取所有提取特定行，并将它们重构为新的抽象 `FetchAdapter`。

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
#### Step 2
通过删除特定于提取的部分来重构 `API`模块，并保持其他所有内容相同。添加 `FetchAdapter` 作为依赖（以某种方式）：

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

现在这是一个不一样的情况了！该体系结构以您能够处理获取资源的不同机制（适配器）的方式进行更改。最后一步：你猜对了！写一个AxiosAdapter！

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

在 `API` 模块中，将默认适配器切换到 `Axios`：

```js
class API {
  constructor(_adapter = new /*FetchAdapter()*/ AxiosAdapter()) {
    this.adapter = _adapter;

    /* ... */
  }
  /* ... */
};
```
真棒！如果我们需要在这个特定的用例中使用旧的 `API` 实现，并且在其他地方继续使用Axios，我们该怎么做？没错，就是这样！
```js
// Import your modules however you like, just an example.
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

**总结**根据你的具体使用情况仔细判断。如果你的代码库滥用适配器和引入太多的抽象可能会导致复杂性增加，这也是不好的。

学会愉快的使用适配器！