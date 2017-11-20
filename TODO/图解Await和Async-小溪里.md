# 图解 Await 和 Async
### 文章目录

1. 简介
2. Promise
3. 问题：组合 Promise
4. Async 函数
5. Await
6. 错误处理
7. 讨论

### 简介
JavaScript ES7中的
[async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
使得协调异步 `promise` 变得更容易。如果你需要从多个数据库或 API 异步获取数据，则可以使用 `promise` 和回调函数。`async` / `await` 使我们更简洁地表达这种逻辑，并完成更易读和可维护的代码。

本教程将使用图表和简单示例来解释 JavaScript中 的 `async` / `await` 语法。

在讲解之前，我们从 `promises` 的简要概述开始。如果你已经了解了 JS 中的 `promise`，请随时跳过本节。

### Promises

在 JavaScript 中，`promise` 代表非阻塞异步执行的抽象对象。JS中的 `promise` 与 Java 中的[`Future`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html)
或C＃的[ `Task` ](https://msdn.microsoft.com/en-us/library/system.threading.tasks.task(v=vs.110).aspx)
类似，如果你了解它们的话那就很容易理解。

`Promise` 通常用于网络和 I/O 操作，例如读取文件或者发出 HTTP 请求。我们可以产生一个异步 `promise` ，并使用 `then` 的方法来附加一个回调函数，这个回调函数当 `promise` 完成时将会被触发，这种方法不会阻止当前的“线程”执行。回调函数本身可以返回 `promise`，使我们可以有效地链接 `promises`。

为了容易理解，在所有示例中，我们假设 [request-promise](https://github.com/request/request-promise) 库已经安装并加载为：

```js
var rp = require('request-promise');
```

我们做一个简单的 HTTP GET 请求，返回一个 `promise` :

```js
const promise = rp('http://example.com/')
```

现在，让我们来看一个例子：

```js
console.log('Starting Execution');

const promise = rp('http://example.com/'); // Line 3
promise.then(result => console.log(result)); // Line 4

console.log("Can't know if promise has finished yet...");
```

我们在**第3行**产生了一个新的 `Promise`，然后在**第4行**新加一个回调函数。因为`promise` 是异步的，所以当我们到达**第6行**时，我们不知道 `promise` 是否已经完成。如果我们多次运行代码，我们可能会每次得到不同的结果。换句话说，任何 promise 之后的代码都是与 promise 同时运行的。

**在 `promise` 完成之前，并没有办法阻止当前的操作顺序**。
这与 Java 中的 [`Future.get`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html#get--)
 不同，其允许我们阻止当前线程，然后之后完成。在 JavaScript 中，**我们不能等待 promise**。在 promise 之后调度代码的唯一方法是通过 `then` 附加回调函数。

下图描绘了该示例的计算过程：

![示例的计算过程](http://nikgrozev.com/images/blog/async-await/SimplePromiseExample.png)

> promise 的计算过程。呼叫“线程”不能等待 promise 。在 promise 之后调度代码的唯一方法是通过 `then` 方法指定回调函数。

当 promise 成功返回时，只有通过 `then` 方法指定回调函数才能执行。如果它失败了（例如由于网络错误），回调函数将不会执行。为了处理失败的 promise ，你可以通过 `catch` 附加另一个回调函数：

```js
rp('http://example.com/').
  then(() => console.log('Success')).
  catch(e => console.log(`Failed: ${e}`))
```

最后，为了测试一下，我们可以使用 `Promise.resolve` 和 `Promise.reject` 方法创建成功或失败的“虚拟” `promises` :

```js
const success = Promise.resolve('Resolved');
// 将会显示 "Successful result: Resolved"
success.
  then(result => console.log(`Successful result: ${result}`)).
  catch(e => console.log(`Failed with: ${e}`))

const fail = Promise.reject('Err');
// 将会显示 "Failed with: Err"
fail.
  then(result => console.log(`Successful result: ${result}`)).
  catch(e => console.log(`Failed with: ${e}`))
```

有关 promises 的更详细的教程，请查看[这篇文章](http://nikgrozev.com/2017/01/22/node-js-cheatsheet-part-1/#promises)

### 组合 Promise
使用单一 `Promise` 是简单有效的。但是，当我们需要对复杂的异步逻辑进行编程时，我们可能会以组合多个 Promise。编写所有的子句和匿名回调可能很容易失控。

例如，假设我们需要编写一个程序：

1. 进行HTTP请求，等待完成，打印结果;
2. 然后进行其他两个并行HTTP调用;
3. 当它们都完成时，打印结果。
 以下代码段演示了如何完成此操作：

```js
//进行第一个调用
const call1Promise = rp('http://example.com/'); // Line 2

call1Promise.then(result1 => {
  //在第一个请求完成后执行
  console.log(result1);

	const call2Promise = rp('http://example.com/'); // Line 8
	const call3Promise = rp('http://example.com/'); // Line 9

  return Promise.all([call2Promise, call3Promise]); // Lin 11
}).then(arr => { // Line 12
  //两个 promise 完成后执行
  console.log(arr[0]);
  console.log(arr[1]);
})
```

我们首先进行第一个 HTTP 调用，并使用回调以在其完成时运行（第1-3行）。在回调中，我们为后续的 HTTP 请求产生了两个 `Promise`（第8-9行）。这两个 `Promise` 同时运行并且我们需要安排一个回调，当它们都完成时。因此，当它们都执行完成时，我们需要通过Promise.all（第11行）将它们组合成一个单一的 `Promise`。回调的结果是一个 `Promise`，我们需要l连接另一个回调函数来打印结果（行12-16）。

下图描述了计算流程：

![CombinedPromises.png](http://nikgrozev.com/images/blog/async-await/CombinedPromises.png)

> 计算过程中的 Promise 组合。我们使用“Promise.all”将两个并发的 Promise组合成一个 Promise。

对于这样一个简单的例子，我们最终得到了 2 个回调，并且必须使用 `Promise.all` 来同步并发 `Promis`e。如果我们不得不再运行一些异步操作或添加错误处理怎么办？这种方法最终很容易崩溃于 `then`-s，`Promise.all` 调用和回调三者混杂在一起。

### Async

  异步函数是用于定义返回 `Promise` 的函数的快捷方式。
  例如，以下定义是等价的：

```js
function f() {
  return Promise.resolve('TEST');
}

// asyncF相当于f！
async function asyncF() {
  return 'TEST';
}
```

类似地，抛出异常的异步函数等效于返回拒绝 Promise 的函数：

```js
function f() {
  return Promise.reject('Error');
}

// asyncF相当于f！
async function asyncF() {
  throw 'Error';
}

```

### Await

当我们使用 `promise` 之后，我们只能通过`then`来传回回调函数(callback)。
不允许直接等待一个 `promise` 执行完毕是为了鼓励用户书写非阻塞的代码，不然用户会更乐意写阻塞的代码，因为它比 `promise` 和回调函数更简单。

然而，为了同步 `promise`, 我们需要允许 `promise` 之间相互等待。换句话说，如果一个异步的操作(例如封装在一个 `promise` 中)就应该去等待另一个异步的操作去完成。但是 JavaScript 解释器如何判断一个操作是否在 promise 中运行呢？

答案就是 `async` 关键字。每一个 `async` 函数都会返回一个 promise。也就是说， JavaScript 解释器就会把所有在 `aysnc` 函数中的操作封装到 promise 中并异步运行。这样就可以让它们去等待其他的 promise 完成。

按下 `await` 关键字，**await 只能在 async 函数中使用**，作用是让我们同步的等待另一个 promise 执行完毕。如果在 async 函数之外使用 promise 的话，依旧需要使用 `then` 回调函数：

```js
async function f() {
  // 返回值将作为 promise 被处理(resolve)之后的结果
  const response = await rp('http://example.com/');
  console.log(response);
}
// 不能在 async 函数之外使用 await 关键字
// 需要使用 then 回调
f().then(() => console.log('Finished'));
```
现在，来看看如何解决刚在在上面一节出现的问题：

```js
// 将解决问题的方法封装到一个异步的函数中
async function solution() {
 // 等待第一个 HTTP 调用并且打印出结果
  console.log(await rp('http://example.com/'));

  // 生成 HTTP 调用但是不等待它们执行完毕 - 同时运行
  const call2Promise = rp('http://example.com/'); // 不等待! // Line 7
  const call3Promise = rp('http://example.com/'); // 不等待! // Line 8

  //在它们都被调用之后 - 等待它们执行完毕
  const response2 = await call2Promise; // Line 11
  const response3 = await call3Promise; // Line 12

  console.log(response2);
  console.log(response3);
}

// 调用 async 函数
solution().then(() => console.log('Finished'))
```
在上面的代码段中，我们将解决方案封装到了一个 `async` 函数中，这样我们就可以直接的等待(`await`) promise 执行完毕。这样避免了使用 `then` 回调函数。 最后，我们调用了 `async` 函数，这个函数只是简单的生成了一个封装调用其他 `promise` 的 `promise`。

在第一个例子(没有 `async` 和 `await`)中，那些 `promise` 会并行启动。这种情况下我们进行了同样的操作(第7，8行)。注意，直到11到12行，我们都没有使用 `await`。当所有的promise都执行完毕(resolve)，我们才去阻塞程序的执行。之后，我们知道两个 promise 都执行完毕了(就像在之前的例子中，使用 `Promise.all(...).then(...)` 一样)。

在底层的计算过程上，这个过程和先前章节所述的过程是相同的，但是代码更加直观，可读性更好。

在引擎中，`async`/`await` 实际上转成了 promise 和 then传入的回调函数。换句话说，它是 `promise` 的语法糖。每次我们使用 `await`，解释器就会生成一个 promise，然后把其余的操作从 `async` 函数取出来放到 then 传入的回调函数中。

考虑一下下面的例子：

```js
async function f() {
  console.log('Starting F');
  const result = await rp('http://example.com/');
  console.log(result);
}
```

函数`f`在底层计算过程描述如下图。由于 `f` 是异步的，它将和调用者同步运行：

![AsyncAwaitExample.png](http://nikgrozev.com/images/blog/async-await/AsyncAwaitExample.png)

函数 `f` 开始运行并且生成了一个 `promise`。同时，函数其余的部分被封装到回调函数中，安排在promise执行完毕之后再执行。

### 错误处理
在前面几个例子中，我们假设 `promis`e 成功的解决( `reslove` ).于是，等待一个 `promise` 返回结果。事实上，如果等待的 `promise` 失败(reject)了，那么 `async` 函数将会返回一个异常(exception)。我们可以使用标准的 `try`/`catch` 去处理这种情况：

```js
async function f() {
  try{
    const promiseResult = await Promise.reject('Error');
  } catch (e) {
    console.log(e);
  }
}
```

如果一个 `async` 函数没有处理异常，不管它是一个被拒绝(`reject`)的 `promise` 还是其他的 bug 造成的，它将返回一个被拒绝(`reject`)的 `promise`:

```js
async function f() {
  // 抛出异常
  const promiseResult = await Promise.reject('Error');
}

// 将打印 "Error"
f().
  then(() => console.log('Success')).
  catch(err => console.log(err))

async function g() {
  throw "Error";
}

// 将打印 “Error”
g().
  then(() => console.log('Success')).
  catch(err => console.log(err))
```
使用已知的异常处理机制将使我们方便的处理被拒绝(`reject`)的 `promise`.

### 讨论

`Async`/`await` 是 `promises` 的一种补充语言结构。它允许我们使用较少样板的 promise。然而 `async`/`await`**不能取代纯粹 promise 的需要**。例如，如果从一个普通的函数或者全局范围内调用一个 `async` 函数，我们无法使用 `await`，我们将借助于普通的promises(译者注：原文使用的是vanilla promise):

```js
async function fAsync() {
  // 事实上返回值是 Promise.resolve(5)
  return 5;
}
// 不能调用 await fAsync(), 需要使用 then/catch
fAsync().then(r => console.log(`result is ${r}`));
```

我通常尝试将我大部分的异步逻辑封装到一个或者几个 `async` 函数中，然后从非异步的代码中调用。这极大地减少了我编写`then`/`catch`回调的数量。

`async`/`await` 结构是更简洁处理 `promise` 的语法糖。每一个 `async`/`await` 结构都可以使用纯粹的 promise 重写。最终，这是一个风格和简洁方面的问题。

学者们指出并发( concurrency )和并行( parallelism )有区别。查看[Rob Pike](https://vimeo.com/49718712)关于该主题或[我之前的帖子](http://nikgrozev.com/2015/07/14/overview-of-modern-concurrency-and-parallelism-concepts/)。并发是关于组合独立进程（在过程的一般含义中）一起工作，而并行是关于实际上同时执行多个进程。并发是关于应用程序的设计和结构，而并行性就是实际的执行。

以多线程应用程序为例。将应用程序分隔为线程定义其并发模型。这些线程在可用内核上的映射定义了其级别或并行。并发系统可以在单个处理器上有效运行，在这种情况下，它不是并行的。

![Concurrent vs. Parallel.png](http://nikgrozev.com/images/blog/Overview%20of%20Modern%20Concurrency%20and%20Parallelism%20Concepts/concurrent_vs_parallel.png)

在这种情况下，`promise` 允许我们将程序分解为可并行运行的并发模块。实际的 JavaScript执行是否并行取决于 JavaScript 解释器实现。例如，Node.js是单线程的，如果 promise 是 CPU 绑定的，那么并不会看到很多并行进程。然而，如果您通过类似 Nashorn 的工具将代码编译成 java 字节码，理论上你可以在不同的 CPU 核心上映射CPU绑定的 promise 并实现并行运行。因此，在我看来，promise（普通或通过`async`/`await`）构成了JavaScript应用程序的并发模型。

> 原文链接： [Await and Async Explained with Diagrams and Examples](http://nikgrozev.com/2017/10/01/async-await/)








