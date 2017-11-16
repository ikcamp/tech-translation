原文链接：[Adapting JavaScript Abstractions Over Time](https://css-tricks.com/adapting-javascript-abstractions-time/)

- 译者：小溪里
- 校对者：？？？


Even if you haven't read my post [The Importance Of JavaScript Abstractions When Working With Remote Data](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/), chances are you're already convinced that maintainability and scalability are important for your project and the way toward that is introducing abstractions.

For the purposes of this post, let's assume that an abstraction, in JavaScript, is a module.


The initial implementation of a module is only the beginning of the long (and hopefully lasting) process of their life-being. I see 3 major events in the lifecycle of a module:

1. Introduction of the module. The initial implementation and the process of re-using it around the project.
2. Changing the module. Adapting the module over time.
3. Removing the module.

In my [previous post](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/) the emphasis was just on that first one. In this article, think more about that second one.

Handling changes to a module is a pain point I see frequently. Compared to introducing the module, the way developers maintain or change it is equally or even more important for keeping the project maintainable and scalable. I've seen a well-written and abstracted module completely ruined over time by changes. I've sometimes been the one who has made those disastrous changes!

When I say disastrous, I mean disastrous from a maintainability and scalability perspective. I understand that from the perspective of approaching deadlines and releasing features which must work, slowing down to think about all the potential image of your change isn't always an option.

The reasons why a developer's changes might not be as optimal are countless. I'd like to stress one in particular:

### The Skill of Making Changes in Maintainable Manner

Here's a way you can start making changes like a pro.

Let's start with a code example: an API module. I choose this because communicating with an external API is one of the first fundamental abstractions I define when I start a project. The idea is to store all the API related configuration and settings (like the base URL, error handling logic, etc) in this module.

Let's introduce only one setting, `API.url`, one private method, `API._handleError()`, and one public method, `API.get()`:

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
In this module, our only public method, API.get() returns a Promise. In all places where we need to get remote data, instead of directly calling the Fetch API via window.fetch(), we use our API module abstraction. For example to get user's info API.get('user') or the current weather forecast API.get('weather'). The important thing about this implementation is that **the Fetch API is not tightly coupled with our code**.

Now, let's say that a change request comes! Our tech lead asks us to switch to a different method of getting remote data. We need to switch to [Axios](https://github.com/axios/axios). How can we approach this challenge?

Before we start discussing approaches, let's first summarize what stays the same and what changes:

1. **Change**: In our public `API.get()` method:
  * We need to change the `window.fetch()` call with `axios()`. And we need to return a Promise again, to keep our implementation consistent. Axios is Promise based. Excellent!
  * Our server's response is JSON. With the Fetch API chain a `.then( res => res.json())` statement to parse our response data. With Axios, the response that was provided by the server is under the `data` property and we don't need to parse it. Therefore, we need to change the .then statement to `.then( res => res.data )`.
2. **Change**: In our private ```API._handleError``` method:
  * The `ok` boolean flag is missing in the object response. However, there is `statusText` property. We can hook-up on it. If its value is `'OK'`, then it's all good.
  Side note: yes, having `ok` equal to `true` in Fetch API is not the same as having 'OK' in Axios's `statusText`. But let's keep it simple and, for the sake of not being too broad, leave it as it is and not introduce any advanced error handling.
3. **No change**: The `API.url` stays the same, along with the funky way we catch errors and `alert` them.

All clear! Now let's drill down to the actual approaches to apply these changes.

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


