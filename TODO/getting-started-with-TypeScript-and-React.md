- 原文地址：[Getting started with TypeScript and React](http://javascriptplayground.com/blog/2017/04/react-typescript/)
- 原文作者：[Jack_Franklin](https://twitter.com/Jack_Franklin)


#             开始使用 TypeScript 和 React
[Tom Dale](https://medium.com/@tomdale/glimmer-js-whats-the-deal-with-typescript-f666d1a3aad0)    和其他人有一些关于 TypeScript 比较好的博文，跟随这些博文，我最近开始使用 TypeScript。今天，我将展示如何从零开始建立一个 TypeScript 工程，以及如何使用 Webpack 管理构建过程。我也将陈述关于 TypeScript 的最初感想，尤其是使用 TypeScript 和 ReactJS。

我不会深入到 TypeScript 语法的具体细节，你可以阅读 [TypeScript handbook](https://www.typescriptlang.org/docs/tutorial.html) 或者免费书籍 [TypeScript Deep Dive](https://basarat.gitbooks.io/typescript/content/docs/getting-started.html)，它们是关于 TypeScript 比较好的入门材料。

**更新：**如果你想用德语阅读这篇文章，你可以 [thanks to the awesome folks at Reactx.de](https://reactx.de/artikel/reactjs-typescript/)

## 安装配置 TypeScript
第一步要做的事情是使用 Yarn 将 TypeScript 安装到本地的 `node_modules` 目录，首先，使用 `yarn init` 创建一个工程：

```javascript
yarn init
yarn add typescript
```
当你安装了 TypeScript，你就可以使用 `tsc` 命令行工具，这个工具可以编译 TypeScript，编译时会创建一个开始文件 `tsconfig.json`，你可以编辑这个文件。你可以运行 `tsc --init` 获得这个文件-如果你已经在本地安装了 `TypeScript`，你需要运行 `./node_modules/.bin/tsc --init`。

**注意：**[你可以在我的dotfiles中看到](https://github.com/jackfranklin/dotfiles/blob/master/zsh/zshrc#L101)，我将 `$PATH` 定义为 `./node_modules/.bin` 这个目录。这有点危险，因为我可能不经意地运行这个目录下的任何可执行的文件，但是我愿意承担这个风险，因为我知道这个目录下安装了什么，而且这能节省很多打字时间！

`tsc --init` 这个命令会生成一个 `tsconfig.json` 文件，所有 `TypeScript`编译器的配置都存在于这个文件中。在默认配置的基础上，我做了一些改变，下面是我正在用的一个配置：

```javascript
{
  "compilerOptions": {
    "module": "es6", // 使用 ES2015 模块
    "target": "es6", // 编译成 ES2015 (Babel 将做剩下的事情)
    "allowSyntheticDefaultImports": true, // 看下面
    "baseUrl": "src", // 可以引入相关的东西到这个文件夹下
    "sourceMap": true, // 使 TypeScript 生成 sourcemaps
    "outDir": "ts-build", // 构建输出目录 (因为我们大部分时间都在使用 Webpack，所以不太相关)
    "jsx": "preserve", // 开启 JSX 模式, 但是 "preserve" 告诉 TypeScript 不要转换它(我们将使用 Babel)
    "strict": true,
  },
  "exclude": [
    "node_modules" // 不要在 node_modules 目录中运行任何代码
  ]
}
```
### allowSyntheticDefaultImports

将这个属性的值设置为 true，它允许你使用 ES2015 默认的 imports 风格, 即使你导入的代码没有使用 ES2015 默认导出。

举个例子，当你导入不是用 ES2015 编写的 React 时（虽然源码是，但是 React 使用一个构建好的版本），就可以利用上面的属性设置。这意味着，在技术上，它没有 ES2015 的默认导出，所以当你导入时， TypeScript 会告诉你设置 allowSyntheticDefaultImports 的值为 true。尽管如此，像 Webpack 这样的构建工具能够导入正确的代码，所以我将这个属性设置为 true，相比使用 `import * as React from 'react'`，我更喜欢 `import React from 'react'`这种方式。

### strict:true

[TypeScript 2.3 版本](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript#new---strict-master-option)引入了一种新的配置选项，`strict`。当将这个值设置为 true 时，TypeScript 编译器会尽可能的严格——如果你将一些 JS 转为 TS，这可能不是你想要的，但是对于一些新的项目，使其尽可能的严格是有意义的。这还引入了一些不同的配置，比较常见的有 `noImplicitAny` 和 `strictNullChecks`:

### noImplicitAny
将 TypeScript 引入一个现有的项目，当你不声明变量的类型时，TypeScript 不会抛出错误。但是，当我使用 scratch 新建一个 TypeScript 项目，我希望编译器尽可能地严格。
TypeScript 默认做的一件事是将变量设置为 `any` 类型。`any` 是 TypeScript 中避免类型检查的有效手段，它可以是任何值。当你转换 JavaScript 时，使用 `any` 是很有用的，但是最好还是尽可能地严格。当将 noImplicitAny 设置为 true，你必须为变量设置类型。举个例子，当将 `noImplicitAny` 设置为 true 时，下面的代码会报错：

```javascript
function log(thing) {
  console.log('thing', thing)
}
```
如果你想了解更多关于 noImplicitAny 的信息，可以阅读[TypeScript Deep Dive](https://basarat.gitbooks.io/typescript/docs/options/noImplicitAny.html)

### strictNullChecks

这是另一个使 TypeScript 编译器严格的选项。TypeScript Deep Dive 这本书[有一个很好的章节介绍这个选项](https://basarat.gitbooks.io/typescript/docs/options/strictNullChecks.html)。如果将这个选项设置为true，TypeScript 会更容易识别出你引用的一个可能是 undefined 值的地方，并将展示这个错误。例如：

```javascript
person.age.increment()
```
当将 strictNullChecks 设置为 true，TypeScript 会认为 person 或者 person.age 可能是 undefined，只有当你处理了这个错误它才不会报错。这会阻止运行时错误，所以这是一个从一开始就避免错误出现的非常好的选项。

## 配置 Webpack, Babel and TypeScript

我是 Webpack 的脑残粉；我喜欢它的插件生态系统、开发者工作流，喜欢它擅长管理复杂的应用和构建流程。所以，即使我们可能仅仅使用 TypeScript 编译器，我仍然喜欢引入 Webpack。因为 TypeScript 编译器输出 ES2015 + React，所以我们需要 Babel 做这项工作。让我们安装 Webpack，Babel，相关的 presets 和[ts-loader](https://github.com/TypeStrong/ts-loader)，ts-loader 是 TypeScript 在 Webpack 中的插件。

还有 [awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader) ，也是 TypeScript 在 Webpack 中的插件，但是我首先找到的是 `ts-loader` 而且到目前为止它非常不错。如果谁使用了 `awesome-typescript-loader`，我很乐意看到关于它们两者的对比。

```javascript
yarn add webpack babel-core babel-loader babel-preset-es2015 babel-preset-react ts-loader webpack-dev-server
```

此时此刻，我必须感谢Tom Duncalf,他在博客中发表的[TypeScript 1.9 + React](http://blog.tomduncalf.com/posts/setting-up-typescript-and-react/)，对我来说，是一个特别好的开始，我极力推荐它。

在 Webpack 中没有特别的配置，但是我还是在代码中列出一些注释来解释它：

```javascript
const webpack = require('webpack')
const path = require('path')

module.exports = {
  // 设置 sourcemaps
  devtool: 'eval',

  // 我们应用的入口, 在 `src` 目录 (我们添加到下面的 resolve.modules):
  entry: [
    'index.tsx'
  ],

  // 配置 devServer 的输出目录和 publicPath
  output: {
    filename: 'app.js',
    publicPath: 'dist',
    path: path.resolve('dist')
  },

  // 配置 devServer 
  devServer: {
    port: 3000,
    historyApiFallback: true,
    inline: true,
  },

  // 告诉 Webpack 加载 TypeScript 文件
  resolve: {
    // 首先寻找模块中的 .ts(x) 文件, 然后是 .js 文件
    extensions: ['.ts', '.tsx', '.js'],

    // 在模块中添加 src, 当你导入文件时，可以将 src 作为相关路由
    modules: ['src', 'node_modules'],
  },

  module: {
    loaders: [
      // .ts(x) 文件应该首先经过 Typescript loader 的处理, 然后是 babel的处理
      { test: /\.tsx?$/, loaders: ['babel-loader', 'ts-loader'], include: path.resolve('src') }
    ]
  },
}
```

我们按照上面的方式配置 loaders ，从而使 `.ts(x)` 文件首先经过 `ts-loader` 的处理。按照 `tsconfig.json` 中的配置，使用 TypeScript 编译 `.ts(x)` 文件 —— 在 `ES2015`中发布。然后，我们使用 Babel 对它进行降级到ES5 。我创建了一个包含presets的 `.babelrc` 文件：

```javascript
{
  "presets": ["es2015", "react"]
}
```

现在我们已经做好了写TypeScript应用的准备。

## 写一个 TypeScript React 组件
现在，我们准备好建立 `src/index.tsx` ,这是我们这个应用的入口。我们可以创建一个虚拟的组件，渲染它，查看它是否正常运行。

```javascript
import React from 'react'
import ReactDOM from 'react-dom'

const App = () => {
  return (
    <div>
      <p>Hello world!</p>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('app'))
```

如果你运行webpack，会看到下面的错误：

```javascript
ERROR in ./src/index.tsx
(1,19): error TS2307: Cannot find module 'react'.

ERROR in ./src/index.tsx
(2,22): error TS2307: Cannot find module 'react-dom'.
```

发生上面的错误是因为 TypeScript 试图确认 React 的类型，React 输出了什么。对于 React DOM ，TypeScript 会做同样的事情。在 TypeScript 中，React 并没有被授权，所以 TypeScript 并没有包含相关信息。幸运地是，在这种情况下，社区已经创建了 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) ，这是一个对于模块来说很大的类型库。

最近，安装机制改变了；在 npm `@types` scope 下，所有的类型被发布。为了获得 React 和 ReactDOM 的类型，我们运行下面的命令：

```javascript
yarn add @types/react
yarn add @types/react-dom
```
通过上面的处理，错误不见了。无论何时，你安装一个依赖时，都应该试着安装 `@types` 包，或者你想查看是否有被支持的类型，你可以在 [TypeSearch](https://microsoft.github.io/TypeSearch/) 网站上查看。

## 本地运行app
为了在本地运行 app，我们只需要运行 `webpack-dev-server` 命令。我设置了一个 script， `start`, 它能做上面的事情：

```javascript
"scripts": {
  "start": "webpack-dev-server"
}
```

服务会找到 webpack.config.json 这个文件，使用它创建我们的应用。

如果你运行 `yarn start` ,你会看到来自于服务的输出，包含 `ts-loader` 的输出，这些能够确认应用是否正常运行。

```javascript
$ webpack-dev-server
Project is running at http://localhost:3000/
webpack output is served from /dist
404s will fallback to /index.html
ts-loader: Using typescript@2.3.0 and /Users/jackfranklin/git/interactive-react-introduction/tsconfig.json
Version: webpack 2.4.1
Time: 6077ms
 Asset     Size  Chunks                    Chunk Names
app.js  1.14 MB       0  [emitted]  [big]  main
webpack: Compiled successfully.
```
我创建了一个 `index.html` 文件，它能够加载编译后的代码：

```javascript
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>My Typescript App</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="dist/app.js"></script>
  </body>
```

在端口3000，我们将会看到 `Hello world!`，我们让 `TypeScript` 运行了！

## 定义一个模块类型
在现在的工程中，我想使用 [React Ace module](https://github.com/securingsincity/react-ace)包含一个代码编辑器。但是，没有针对于 React Ace module 的模块类型，并且也没有 `@types/react-ace` 。在这种情况下，我们必须在我们的应用中增加类型，这样可以使 TypeScript 知道它的类型。这看起来非常烦人，使用 TypeScript 的好处至少是知道所有的第三方依赖，这样会节省调试时间。

定义一个只包含类型的文件，后缀是 `.d.ts` ('d' 代表 ‘declaration’)，你可以从 [TypeScript docs](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction)了解更多。在你的工程中，TypeScript将会自动地找到这些文件，你不需要显式地导入它们。

我创建了 `react-ace.d.ts` 文件，添加下面的代码，创建模块，作为一个默认导出的 React 组件。

```javascript
declare module 'react-ace' {
    interface ReactAceProps {
      mode: string
      theme: string
      name: string
      editorProps?: {}
      showPrintMargin?: boolean
      minLines?: number
      maxLines?: number
      wrapEnabled?: boolean
      value: string
      highlightActiveLine?: boolean
      width?: string
      fontSize?: number
    }

    const ReactAce: React.ComponentClass<ReactAceProps>
    export = ReactAce
}
```

我首先创建了一个 TypeScript 接口，这个接口包含组件的属性，`export = ReactAce` 标明组件通过模块被导出。通过定义属性的类型，TypeScript 会告诉我是否弄错了属性的类型或者忘记设置一个类型，这是非常有价值的。

## 测试
最后，通过配置 TypeScript，我们会有一个好的测试。我是 Facebook 的 [Jest](https://facebook.github.io/jest/) 的超级粉丝，我在 google 上做了一些搜索，确认它是否能用 TypeScript 运行。结果发现，这是可行的，[`ts-jest`](https://www.npmjs.com/package/ts-jest) 包可以做一些很重的转换。除此之外，还有一个做类型检查的 `@types/jest` 包。

非常感谢 `RJ Zaworski`，他在博客上发表了 [TypeScript and Jest](https://rjzaworski.com/2016/12/testing-typescript-with-jest) ，这使我开始了解这个主题。一旦你安装了 `ts-jest`, 你必须在 `package.json` 中配置 Jest，下面是配置：

```javascript
"jest": {
  "moduleFileExtensions": [
    "ts",
    "tsx",
    "js"
  ],
  "transform": {
    "\\.(ts|tsx)$": "<rootDir>/node_modules/ts-jest/preprocessor.js"
  },
  "testRegex": "/*.spec.(ts|tsx|js)$"
},
```

第一个配置告诉 Jest 寻找 `.ts` 和 `.tsx` 文件。 `transform` 对象告诉 Jest 通过 ts-jest 预处理器运行任何 TypeScript 文件，ts-jest预处理器通过TypeScript编译器运行 TypeScript 文件，产出能让 Jest 识别的JavaScript。最后，我更新了 `testRegex` 设置，目的是寻找任何 `*.spec.ts(x)` 文件，我更喜欢用这种方式命名转换。

通过上面这些配置，我可以运行 `jest` 并且让每一件事情都如预期一样运行。

## 使用TSLint规范代码

尽管 TypeScript 在代码中会给出很多检查提示，我仍然想要一个规范器，做些代码风格和质量检查。就像 JavaScript 的 ESLint, [TSLint](https://palantir.github.io/tslint/) 是检查 TypeScript的最好选择。它和 ESlint 的工作方式相同 - 用一系列生效或不生效的规则，还有一个 [TSLint-React](https://github.com/palantir/tslint-react) 包增加 React 的具体规则。

你可以通过 `tslint.json` 文件配置 TSLint，我的配置文件如下。我用 `tslint:latest` 和 `tslint-react` presets，它们可以使用很多规则。我不赞成一些默认设置，所以我重写了他们 - 你可以和我的配置不同 - 这完全取决于你！

```javascript
{
    "defaultSeverity": "error",
    "extends": ["tslint:latest", "tslint-react"],
    "jsRules": {},
    "rules": {
      // 用单引号, 但是在 JSX 中，强制使用双引号
      "quotemark": [true, "single", "jsx-double"],
      // 我更喜欢没有分号 :)
      "semicolon": [true, "never"],
      // 这个规则使每个接口以 I 开头，这点我不喜欢
      "interface-name": [true, "never-prefix"],
      // 这个规则强制对象中的 key 按照字母顺序排列 
      "object-literal-sort-keys": false
    },
    "rulesDirectory": []
}
```

我可以运行 `tslint --project tsconfig.json` 规范我的项目

## 结论

总之，目前为止，用 TypeScript 开发我很高兴。我肯定会发表更多关于我是如何使用这门语言的具体细节的博文，按照构建过程，配置所有的工具，开始使用类型，这真的很高兴。如果你正在将你的 JS 应用结构化，想要一个更强大的编译器避免错误并减少调试时间，我极力推荐你尝试 TypeScript。

如果你想看源码或者以本文中的例子作为开始，我[在 GitHub 上放了一个例子](https://github.com/javascript-playground/react-typescript-jest-demo)，如果你有任何问题，可以提 issue。
 