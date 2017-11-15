原文链接：[Adapting JavaScript Abstractions Over Time](https://css-tricks.com/adapting-javascript-abstractions-time/)

- 译者：小溪里
- 校对者：？？？


即使你还没有读过我的文章《[在处理网络数据的 JavaScript 抽象的重要性](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)》，很有可能你已经意识到在你的项目中可维护性和可扩展性很重要，这也是介绍 `JavaScript`抽象的目的。

为了介绍这篇文章，我们假设在 `JavaScript` 中抽象是一个模块。

一个模块的最初实现只是它们漫长（也许是持久的）的生命周期过程的开始。我将一个模块的生命周期分成 3 个事件。

1. 模块介绍。项目中重复使用它的最初实现和过程；
2. 调整模块。随着时间推移随时调整模块；
3. 移除模块。

在我先前的[文章](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)中，重心放在了第一点上。而在这篇文章中，我将在第二点上引入更多思考。

处理对模块中的更改是我经常碰到的一个难题。与引入模块相比，开发者维护和更改模块的方式对保证项目的可维护性和可拓展性是同等重要甚至是更加重要。我看过一个写得很好、抽象得很好的模块随着时间推移历经多次更改后背彻底毁了。我自己也经常是造成那种毁灭性更改的其中一个。

当我说毁灭性，我意思是可维护性和可扩展性角度上的毁灭。我也明白，从接近工作的最后期限和必须释放资源的意义上，放慢对你所有可能性的变化的思考都不是一种优先方案。


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
   */
  _handleError(_res) {
      return _res.ok ? _res : Promise.reject(_res.statusText);
  }

  /**
   * Get data abstraction
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
在这个示例中，公共方法 `API.get（）`返回一个Promise。使用API模块，在所有需要获取远程数据的地方，直接调用Fetch API即可， 无需调用 `window.fetch（）`。例如获取用户信息 `API.get（'user'）`或当前天气预报  `API.get（'weather'）`。实现这个功能的重要之处在于**Fetch API与我们的代码没有紧密耦合。

现在，让我们说说修改的要求！当技术主管要求我们切换到另一种获取远程数据的方法时。我们需要切换到[Axios](https://github.com/axios/axios)。我们应当如何应对？

在我们开始讨论方法之前，我们先来总结一下什么是不变的，什么是需要修改的：

1. 在公共 `API.get（）`方法中：
  - 需要修改 `axios（）`的 `window.fetch（）`调用;需要再次返回一个Promise，来保持二者之间可以保持同步，Axios是Promise的基础。
  - 服务器的响应是JSON。通过Fetch API链，用`（res => res.json（））`语句来解析响应数据。使用Axios，服务器的响应在`data`属性下，我们不需要解析它。因此，我们需要将`.then`语句改为`.then（res => res.data）`。
2. 在私有 `API._handleError` 方法中：
  - 对象响应中缺少 `ok` 布尔标志;但是，还有 `statusText` 属性。如果它的值是`OK`，我们可以将其连接起来。（附注：在 Fetch API 中 `OK` 等于 `true` 与在Axios中的 `statusText` 具有 `OK` 是不一样的。但为了便于理解，所以尽量保持简单，不再引入任何高级错误处理。）
3. 不变之处：`API.url` 保持不变，我们会发现错误并以愉快的方式提醒他们。

讲解完毕！现在让我们深入应用这些修改的实际方法。

### Approach 1: Delete code. Write code.
```js
class API {
  constructor() {
    this.url = 'http://whatever.api/v1/'; // says the same
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
Sounds reasonable enough. Commit. Push. Merge. Done.

However, there are certain cases why this might not be a good idea. Imagine the following happens: after switching to Axios, you find out that there is a feature which doesn't work with [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) (the Axios's interface for getting resource method), but was previously working just fine with Fetch's fancy new browser API. What do we do now?

Our tech lead says, let's use the old API implementation for this specific use-case, and keep using Axios everywhere else. What do you do? Find the old API module in your source control history. Revert. Add if statements here and there. Doesn't sound very good to me.

There must be an easier, more maintainable and scalable way to make changes! Well, there is.

### Approach 2: Refactor code. Write Adapters!
There's an incoming change request! Let's start all over again and instead of deleting the code, let's move the Fetch's specific logic in another abstraction, which will serve as an adapter (or wrapper) of all the Fetch's specifics.

> HEY!???For those of you familiar with the Adapter Pattern (also referred to as the Wrapper Pattern), yes, that's exactly where we're headed! See an excellent nerdy introduction here, if you're interested in all the details.

Here's the plan:
![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1509913814/adapters_iteb3k.png)

#### Step 1
Take all Fetch specific lines from the API module and refactor them to a new abstraction, FetchAdapter.

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
Refactor the API module by removing the parts which are Fetch specific and keep everything else the same. Add FetchAdapter as a dependency (in some manner):

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
That's a different story now! The architecture is changed in a way you are able to handle different mechanisms (adapters) for getting resources. Final step: You guessed it! Write an `AxiosAdapter`!

```js
const AxiosAdapter = {
  _handleError(_res) {
      return _res.statusText === 'OK' ? _res : Promise.reject(_res.statusText);
  },

  get(_endpoint) {
      return axios.get(_endpoint)
          .then(this._handleError)
          .then( res => res.data);
  }
};
```

And in the API module, switch the default `adapter` to the Axios one:

```js
class API {
  constructor(_adapter = new /*FetchAdapter()*/ AxiosAdapter()) {
    this.adapter = _adapter;

    /* ... */
  }
  /* ... */
};
```
Awesome! What do we do if we need to use the old API implementation for this specific use-case, and keep using Axios everywhere else? No problem!

```js
// Import your modules however you like, just an example.
import API from './API';
import FetchAdapter from './FetchAdapter';

// Uses the AxiosAdapter (the default one)
const API = new API();
API.get('user');
```

// Uses the FetchAdapter
const legacyAPI = new API(new FetchAdapter());
legacyAPI.get('user');
So next time you need to make changes to your project, evaluate which approach makes more sense:

* Delete code. Write code
* Refactor Code. Write Adapters.

**Judge** carefully based on your specific use-case. Over-adapter-ifying your codebase and introducing too many abstractions could lead to increasing complexity which isn't good either.

Happy adapter-ifying!


