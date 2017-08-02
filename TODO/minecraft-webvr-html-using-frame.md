# 使用A-Frame打造WebVR版《我的世界》

原文链接：https://css-tricks.com/minecraft-webvr-html-using-frame/<br />
翻译：Felix

我是 Kevin Ngo，一名就职于 [Mozilla VR 团队](https://mozvr.com/)的 web 虚拟现实开发者，也是 [A-Frame](https://aframe.io/) 的核心开发人员。今天，我们来看看如何使用 A-Frame 构建一个够在 HTC Vive、Oculus Rift、Samsung GearVR、Google Cardboard、桌面设备以及移动设备上运行的、支持空间追踪（room-scale）技术的 WebVR 版《我的世界》示例。该示例基于 A-Frame，且仅使用 11 个 HTML 元素！

![](https://css-tricks.com/images/minesweeper-webvr-demo.gif)

## A-Frame
几年前，Mozilla 发明并开发了 [WebVR](https://webvr.rocks/) —— 一套在浏览器中创造身临其境 VR 体验的 JavaScript API —— 并将其发布在一个实验版本的 Firefox 浏览器中。此后，WebVR 得到了 Google、Microsoft、Samsung 以及 Oculus 等其他公司的广泛支持。而现在，WebVR 更是在短短几个月内就被内嵌在发行版的 Firefox 浏览器中，并被设置为默认开启！

为什么会诞生 WebVR？Web 为 VR 带来了开放性；在 Web 上，内容并不由管理员所控制，用户也不被关在高高的围墙花园（walled garden）中。Web 也为 VR 带来了连通性；在 Web 上，我们能够在世界中穿梭 —— 就像我们点击超链接在页面见穿梭一样。随着 WebGL 的成熟以及诸如 Web Assembly 和 Service Workers 规范的提出，WebVR 已经准备好了。

[Mozilla VR 团队](https://mozvr.com/)创造了 A-Frame 框架来为 WebVR 生态系统抛砖引玉，该框架给予开发者构建 3D 和 VR 世界的能力。

<hr />

![](https://res.cloudinary.com/css-tricks/image/fetch/q_auto,f_auto/https://cdn.css-tricks.com/wp-content/uploads/2017/03/aframe-homepage.png)
A-Frame 官方网站首页
<hr />

[A-Frame](https://aframe.io/) 是一个构建虚拟现实体验设的 web 框架，它基于 HTML 和[实体组件范式](https://aframe.io/docs/0.5.0/introduction/#entity-component-system)（the Entity-Component pattern）。HTML 是所有计算机语言中最易理解的语言，这使得任何人都能[快速上手](https://aframe.io/docs/0.5.0/introduction/getting-started.html) A-Frame。下面是一个使用 HTML 搭建的完整的 3D 和 VR 场景，它能够在诸如桌面设备和移动设备等任何 VR 平台运行：
```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>

<a-scene>
  <a-sphere position="0 1.25 -5" radius="1.25" color="#EF2D5E"></a-sphere>
  <a-box position="-1 0.5 -3" rotation="0 45 0" width="1" height="1" depth="1" color="#4CC3D9"></a-box>
  <a-cylinder position="1 0.75 -3" radius="0.5" height="1.5" color="#FFC65D"></a-cylinder>
  <a-plane position="0 0 -4" rotation="-90 0 0" width="4" height="4" color="#7BC8A4"></a-plane>
  <a-sky color="#ECECEC"></a-sky>
</a-scene>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/BjygdO)

就是这样！只用使用**一行 HTML（<a-scene>）即可搞定 3D 和 VR 样板代码搭建**，包括：canvas、场景、渲染器、渲染循环、摄像机以及 raycaster。然后，我们可以通过使用添加子元素的方式来为场景添加对象。**无需构建**，就只是一个简单的、可随意拷贝粘贴的 HTML 文件。

<hr />

![](https://res.cloudinary.com/css-tricks/image/fetch/q_auto,f_auto/https://cdn.css-tricks.com/wp-content/uploads/2017/03/e155c380-aa92-11e6-9507-f19403783a7b.jpg)
<hr />

我们还可以动态查询和操作 A-Frame 的 HTML，就像使用[标准 JavaScript 和 DOM APIs](https://aframe.io/docs/0.5.0/guides/using-javascript-and-dom-apis.html) （例如 querySelector、getAttribute、addEventListener、setAttribute）那样。

```js
// 使用 `querySelector` 查询场景图像。
var sceneEl = document.querySelector('a-scene');
var boxEl = sceneEl.querySelector('a-box');

// 使用 `getAttribute` 获得实体的数据。
console.log(box.getAttribute('position'));
// >> {x: -1, y: 0.5, z: -3}

// 使用 `addEventListener` 监听事件。
box.addEventListener('click', function () {
  // 使用 `setAttribute` 修改属性。
  box.setAttribute('color', 'red');
});
```

<hr />

![](https://res.cloudinary.com/css-tricks/image/fetch/q_auto,f_auto/https://cdn.css-tricks.com/wp-content/uploads/2017/03/js.jpg)
<hr />

而且，因为这些只是 HTML 和 JavaScript，因此 A-Frame 和许多现存的框架和库兼容良好：
<hr />

![](https://res.cloudinary.com/css-tricks/image/fetch/q_auto,f_auto/https://cdn.css-tricks.com/wp-content/uploads/2017/03/5f3f10b6-aa94-11e6-9d71-94c3e4350d08.png)
兼容 d3、Vue、React、Redux、jQuery、Angular
<hr />

尽管 A-Frame 的 HTML 看起来比较简单，但是 A-Frame 的 API 却远远比简单的 3D 声明强大。A-Frame 是一个[实体组件系统（ECS）框架](https://aframe.io/docs/0.5.0/introduction/#entity-component-system)，ECS 在游戏开发中是一种流行的模式，值得注意的是 ECS 也被 Unity 引擎所使用。其概念包括：
- 在场景中，所有的对象都是*实体（entities）*，空对象本身什么也不能做，类似空 `<div>`。A-Frame 使用 HTML 元素在 DOM 中表示实体。
- 接下来，我们在实体中插入*组件（components）* 来提供外观、行为和功能。在 A-Frame 中，组件被注册在 JavaScript 中，并且可以被用来做任何事情。它们可使用完整的 [three.js](http://threejs.org/) 和 DOM APIs。组件注册后，可以附加在 HTML 实体上。

ECS 的优势在于它的可组合性；我们可以混合和搭配这些可复用的组件来构建出更复杂的 3D 对象。A-Frame 更上一层楼，将这些组件声明化，并使其作为 DOM 的一部分，就像我们待会在《我的世界》示例中看到那样。

## 示例骨架
现在来关注我们的示例。我们将搭建一个基本的 VR 立体像素制作器（voxel builder），它主要用于支持位置追踪（positional tracking）和追踪控制器（tracked controllers）的空间追踪 VR 设备（例如 HTC Vive 及 Oculus Rift + Touch）。

我们会从 HTML 骨架开始。如果你想要快速浏览（完整的 11 行 HTML），[点击这里到 GitHub 查看源代码](https://github.com/ngokevin/kframe/tree/csstricks/scenes/aincraft/)。

```js
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>

<body>
  <a-scene>
  </a-scene>
</body>
```

## 添加地面
`<a-plane>` 和 `<a-circle>` 都是常被用作添加地面的图元，不过我们会使用 `<a-cylinder>` 来更好地配合控制器完成灯光计算工作。圆柱（cylinder）的半径为 30 米，待会我们要添加的天空将会和这个半径值匹配起来。注意 A-Frame 中的单位是米，以匹配 WebVR API 返回的现实世界中的单位。

地面的纹理部署在 `https://cdn.aframe.io/a-painter/images/floor.jpg`。我们将纹理添加进项目中，并使用该纹理制作一个扁的圆柱实体。

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>

<a-scene>
  <a-cylinder id="ground" src="https://cdn.aframe.io/a-painter/images/floor.jpg" radius="32" height="0.1"></a-cylinder>
</a-scene>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/MpbXXe)

## 预加载资源
通过 `src` 属性指定的 URL 资源将在运行时加载。

由于网络请求会对渲染的性能产生负面影响，所以我们可以*预加载*纹理以保证资源被下载完成前不进行渲染工作，预加载可以通过[资源管理系统（asset management system）](https://aframe.io/docs/0.5.0/core/asset-management-system.html)来完成。

我们将 `<a-assets>` 置入 `<a-scene>` 中，将资源（例如图片、视频、模型及声音等）置入 `<a-assets>` 中，并通过选择器（例如 #myTexture）将资源指向我们的实体。

让我们将地面纹理移动到 `<a-assets>` 中，使用 `<img>` 元素来预加载它：

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>

<a-scene>
  <a-assets>
    <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
  </a-assets>

  <a-cylinder id="ground" src="#groundTexture" radius="32" height="0.1"></a-cylinder>
</a-scene>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/LWbrBQ)

## 添加背景
让我们使用 [`<a-sky>` 元素](https://aframe.io/docs/0.5.0/primitives/a-sky.html)为 `<a-scene>` 添加一个 360° 的背景。`<a-sky>` 是一个在内部粘贴材质的巨大 3D 球体。就像普通图片一样，`<a-sky>` 可以通过 `src` 属性接受图片地址。最终我们将可以使用一行 HTML 代码实现身临其境的 360° 图片。稍后你也可以在 [Flickr 球面投影图片池（需翻墙）](https://www.flickr.com/groups/equirectangular/)中选择一些 360° 图片来做练习。

我们可以添加普通的颜色背景（例如 `<a-sky color="#333"></a-sky>`）或[渐变](https://github.com/zcanter/aframe-gradient-sky)，不过这次让我们来添加一张背景纹理图片。该图片被部署在 `https://cdn.aframe.io/a-painter/images/sky.jpg`。我们所使用的图片是一张适用于半球体的图片，所以首先我们需要将刚刚的球体使用 `theta-length="90"` 水平截成半球体，另外我们将球的半径设置为 30 米以匹配地面。

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>

<a-scene>
  <a-assets>
    <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
    <img id="skyTexture" src="https://cdn.aframe.io/a-painter/images/sky.jpg">
  </a-assets>
  
  <a-cylinder id="ground" src="#groundTexture" radius="30" height="0.1"></a-cylinder>

  <a-sky id="background" src="#skyTexture" theta-length="90" radius="30"></a-sky>
</a-scene>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/PpbaBL)

## 添加体素
在我们的 VR 应用中，体素（voxels）的写法类似 `<a-box>`，但会添加一些自定义的 A-Frame 组件。不过让我们先大致了解实体-组件范式，来看看像 `<a-box>` 这样的图元是怎样合成的。

在这个部分，我们将会对若干 A-Frame 组件的实现做一些深入探讨。在实践中，我们经常会通过已由 A-Frame 社区开发人员编写好的 HTML 来使用组件，而不是从头构建它们。

### 实体-组件范式
在 A-Frame 场景中的每一个对象都是 `<a-entity>`，其本身什么也不能做，就像一个空 `<div>` 一样。我们将组件（不要和 Web Components 或 React Components 混淆）插入实体来给予其外观、行为和逻辑。

对于一个盒子来说，我们会为其配置及添加 A-Frame 的基础[几何组件](https://aframe.io/docs/0.5.0/components/geometry.html)和[材质组件](https://aframe.io/docs/0.5.0/components/material.html)。组件使用 HTML 属性来表示，组件属性默认使用类似 CSS 样式的表示方法来表示。下面是一个 `<a-box>` 的基础组件拆解写法，可以看到 `<a-box>` 事实上包裹了若干组件：

```html
<a-box color="red" depth="0.5" height="0.5" shader="flat" width="0.5"></a-box>

<a-entity geometry="primitive: box; depth: 0.5; height: 0.5; width 0.5"
          material="color: red; shader: standard"></a-entity>
```
使用组件的好处是它们的具有可组合性。我们可以通过混合和搭配一堆已有的组件来构造出各种各样的对象。

在 3D 开发中，我们可能构建出的对象类型在数量和复杂性上是无限的，因此我们需要一个简便的、全新的、非传统继承式的对象定义方法。与 2D web 相比，我们不再拘泥于使用一小撮固定的 HTML 元素并将它们嵌套在很深的层次结构中。

### 随机颜色组件
A-Frame 中的组件由 JavaScript 定义，它们可使用完整的 [three.js](http://threejs.org/) 和 DOM APIs，它们可以做任何事。所有的对象都由一捆组件来定义。

现在将刚刚所描述的模式付诸实践，通过书写一个 A-Frame 组件，为我们的盒子设置随机颜色。组件通过 AFRAME.registerComponent 注册，我们可以定义 schema（组件的数据）以及生命周期方法（组件的逻辑）。对于随机颜色组件，我们并不需要设置 schema，因为它不能被配置。但我们会定义一个 `init` 处理函数，该函数会在组件首次附加到它的实体时被调用。

```js
AFRAME.registerComponent('random-color', {
  init: function () {
    // ...
  }
});
```

对于随机颜色组件，我们的意图是为其附加的实体设置随机颜色。在组件的方法中，可以使用 `this.el` 访问实体的引用。

为了使用 JavaScript 来改变颜色，我们使用 `.setAttribute()` 来设置材质组件的颜色属性。A-Frame 只引入了少数 API，大多数 API 和原生 web 开发 API 保持一致。[点此详细了解如何在 A-Frame 中使用 JavaScript 和 DOM API](https://aframe.io/docs/0.5.0/guides/using-javascript-and-dom-apis.html)。

我们还需要将 `material` 组件添加到预先初始化组件列表中，以保证材质不会被 `material` 组件覆盖掉。

```js
AFRAME.registerComponent('random-color', {
  dependencies: ['material'],

  init: function () {
    // 将材质组件的颜色属性设置为随机颜色
    this.el.setAttribute('material', 'color', getRandomColor());
  }
});

function getRandomColor() {
  const letters = '0123456789ABCDEF';
  var color = '#';
  for (var i = 0; i < 6; i++ ) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
}
```

在组件被注册后，我们可以**直接使用 HTML** 来链接该组件。A-Frame 框架中的所有代码都是对 HTML 的扩展，而且这些扩展可以用于其他对象和其他场景。很棒的是，开发者可以写一个向对象添加物理元素的组件，使用这个组件的人甚至不会察觉到 JavaScript 在他的场景中加入了这个物理元素！

注意力回到刚刚的盒子实体，将 `random-color` 作为 HTML 属性插入到 `random-color` 组件中。我们将组件保存为一个 JS 文件，然后在场景代码之前引用它：

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
<script src="https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/random-color.js"></script>

<a-scene>
  <a-assets>
    <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
    <img id="skyTexture" src="https://cdn.aframe.io/a-painter/images/sky.jpg">
  </a-assets>
  
  <a-sky id="background" src="#skyTexture" theta-length="90" radius="30"></a-sky>

  <a-cylinder id="ground" src="#groundTexture" radius="30" height="0.1"></a-cylinder>
  
  <!-- 随机颜色的盒子 -->
  <a-entity geometry="primitive: box; depth: 0.5; height: 0.5; width 0.5"
            material="shader: standard"
            position="0 0.5 -2"
            random-color></a-entity>
</a-scene>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/ryWKqy)

组件可以插入到任何实体中，但并不需要像在传统继承模式中那样创建或扩展类。如果我们想在类似 `<a-shpere>` 或 `<a-obj-model>` 中附加组件，直接加就是了！

```html
<!-- 在其他实体上重用并附加随机颜色组件 -->
<a-sphere random-color></a-sphere>
<a-obj-model src="model.obj" random-color></a-obj-model>
```

如果我们想要将这个组件分享给他人使用，也没问题。我们可以在 [A-Frame 仓库](https://aframe.io/registry/)中获取 A-Frame 生态系统中许多便利的组件，这类似 Unity 的 Asset Store。如果我们使用组件开发应用程序，那么就应当保证我们的代码在内部是模块化和可重用的！

### 对齐组件
我们将使用 `snap` 组件来将盒子对齐到网格以避免它们重叠。我们不会深入到该组件的实现原理，不过你可以看看 [snap 组件的源代码](https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/snap.js)（20 行 JavaScript 代码）。

将 snap 组件附加到盒子实体上，让盒子每半米对齐，同时使用 offset 来使盒子居中：

```html
<a-entity
   geometry="primitive: box; height: 0.5; width: 0.5; depth: 0.5"
   material="shader: standard"
   random-color
   snap="offset: 0.25 0.25 0.25; snap: 0.5 0.5 0.5"></a-entity>
```

现在，我们有了一个由一捆组件构成的盒子实体，该实体可以用来描述我们场景中的所有体素（砖块）。

## Mixins
我们可以创建 [mixin](https://aframe.io/docs/0.5.0/core/mixins.html) 来定义可复用的组件集合。

与使用 `<a-entity>` 为场景添加一个对象不同，我们使用 `<a-mixin>` 来创建可复用的体素，使用它们就像使用预设实体一样。

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
<script src="https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/random-color.js"></script>
<script src="https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/snap.js"></script>

<a-scene>
  <a-assets>
    <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
    <img id="skyTexture" src="https://cdn.aframe.io/a-painter/images/sky.jpg">
    <a-mixin id="voxel"
       geometry="primitive: box; height: 0.5; width: 0.5; depth: 0.5"
       material="shader: standard"
       random-color
       snap="offset: 0.25 0.25 0.25; snap: 0.5 0.5 0.5"></a-mixin>
  </a-assets>

  <a-sky id="background" src="#skyTexture" theta-length="90" radius="30"></a-sky>

  <a-cylinder id="ground" src="#groundTexture" radius="30" height="0.1"></a-cylinder>
  
  <a-entity mixin="voxel" position="-1 0 -2"></a-entity>
  <a-entity mixin="voxel" position="0 0 -2"></a-entity>
  <a-entity mixin="voxel" position="0 1 -2">
    <a-animation attribute="rotation" to="0 360 0" repeat="indefinite"></a-animation>
  </a-entity>
  <a-entity mixin="voxel" position="1 0 -2"></a-entity>
</a-scene>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/OpbEaY)

随后我们使用 mixin 添加了若干体素：

```html
<a-entity mixin="voxel" position="-1 0 -2"></a-entity>
<a-entity mixin="voxel" position="0 0 -2"></a-entity>
<a-entity mixin="voxel" position="0 1 -2">
  <a-animation attribute="rotation" to="0 360 0" repeat="indefinite"></a-animation>
</a-entity>
<a-entity mixin="voxel" position="1 0 -2"></a-entity>
```

接下来，我们将通过使用追踪控制器根据用户交互来动态创建体素。让我们开始向程序中添加一双手吧。

# 添加手部控制器
添加 HTC Vive 或 Oculus Touch 追踪控制器非常简单：

```html
<!-- Vive -->
<a-entity vive-controls="hand: left"></a-entity>
<a-entity vive-controls="hand: right"></a-entity>

<!-- Rift -->
<a-entity oculus-touch-controls="hand: left"></a-entity>
<a-entity oculus-touch-controls="hand: right"></a-entity>
```

我们将使用抽象的 `hand-controls` 组件来同时兼容 Vive 和 Rift 的控制，它提供基本的手模型。左手负责移动位置，右手负责放置砖块。

```html
<a-entity id="teleHand" hand-controls="left"></a-entity>
<a-entity id="blockHand" hand-controls="right"></a-entity>
```

### 为左手添加瞬移功能
我们将为左手增加瞬移的能力，当按住左手控制器按钮时，从控制器显示一条弧线，松开手时，瞬移到弧线末端的位置。在此之前，我们已经自己写了一个实现随机颜色的 A-Frame 组件。

但也可以使用社区中已有的开源组件，然后直接通过 HTML 使用它们！

对于瞬移来说，有一个来自于 @fernandojsg 的[瞬移控制组件](https://github.com/fernandojsg/aframe-teleport-controls/)。遵循 README，我们使用 `<script>` 标签引入 `teleport-controls` 组件，并将其附加到控制器实体上。

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
<script src="https://unpkg.com/aframe-teleport-controls@0.2.x/dist/aframe-teleport-controls.min.js"></script>

<!-- ... -->

<a-entity id="teleHand" hand-controls="left" teleport-controls></a-entity>
<a-entity id="blockHand" hand-controls="right"></a-entity>
```

随后我们来配置 `teleport-controls` 组件，将瞬移的 `type` 设置为弧线。默认来说，`teleport-controls` 的瞬移只会发生在地面上，但我们也可以指定 `collisionEntities` 通过选择器来允许瞬移到砖块*和*地面上。这些属性是 `teleport-controls` 组件创建的 API 的一部分。

```html
<a-entity id="teleHand" hand-controls="left" teleport-controls="type: parabolic; collisionEntities: [mixin='voxel'], #ground"></a-entity>
```

就是这样！**只要一个 script 标签和一个 HTML 属性，我们就能瞬移了。**在 [A-Frame 仓库](https://aframe.io/registry/)中可以找到更多很酷的组件。

### 为右手添加体素生成器功能

在 2D 应用程序中，对象内置了处理点击的能力，而在 WebVR 中对象并没有这样的能力，需要我们自己来提供。幸运的是，A-Frame 拥有许多处理交互的组件。VR 中用于类似光标点击的场景方法是使用 raycaster，它射出一道激光并返回激光命中的物体。然后我们通过监听交互事件及查看 raycaster 来获得命中点信息。

A-Frame 提供基于注视点的光标（注：就像 FPS 游戏的准心那样），可以利用此光标点击正在注视的物体，但也有可用的[控制器光标组件](https://github.com/bryik/aframe-controller-cursor-component/)来根据 VR 追踪控制器的位置发射激光，就像刚刚使用 `teleport-controls` 组件那样，我们通过 script 标签将 `controller-cursor` 组件引入，然后附加到实体上。这次轮到右手了：

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
<script src="https://unpkg.com/aframe-teleport-controls@0.2.x/dist/aframe-teleport-controls.min.js"></script>
<script src="https://unpkg.com/aframe-controller-cursor-component@0.2.x/dist/aframe-controller-cursor-component.min.js"></script>

<!-- ... -->

<a-entity id="teleHand" hand-controls="left" teleport-controls="type: parabolic; collisionEntities: [mixin='voxel'], #ground"></a-entity>
<a-entity id="blockHand" hand-controls="right" controller-cursor></a-entity>
```

现在当我们按下追踪控制器上的按钮时，`controller-cursor` 组件将同时触发控制器和交互实体的 `click` 事件。A-Frame 也提供了诸如 `mouseenter` 及 `mouseleave` 这样的事件。事件包含了用户交互的详细信息。

这赋予了我们点击的能力，但我们还得写一些响应点击事件处理生成砖块的逻辑。可以使用事件监听器及 `document.createElement` 来完成：

```javascript
document.querySelector('#blockHand').addEventListener(`click`, function (evt) {
  // 创建一个砖块实体
  var newVoxelEl = document.createElement('a-entity');

  // 使用 mixin 来将其变为体素
  newVoxelEl.setAttribute('mixin', 'voxel');

  // 使用命中点的数据来设置砖块位置。
  // 上文所述的 `snap` 组件是 mixin 的一部分，它将会把砖块对齐到最近的半米
  newVoxelEl.setAttribute('position', evt.detail.intersection.point);

  // 使用 `appendChild` 添加到场景中
  this.appendChild(newVoxelEl);
});
```

为了概括性地处理在命中点创建实体这样的需求，我们创建了 `intersection-spawn` 组件，该组件接受任何事件和属性列表的配置。我们不会详细讨论其实现，但你可以[在 GitHub 上查看这个简单的 **intersection-spawn** 组件的源码](https://github.com/ngokevin/kframe/blob/csstricks/scenes/aincraft/components/intersection-spawn.js)。我们将 `intersection-spawn` 的能力附加到右手上：

```html
<a-entity id="blockHand" hand-controls="right" controller-cursor intersection-spawn="event: click; mixin: voxel"></a-entity>
```

现在当我们点击时，就可以生成体素了！

### 添加移动设备和桌面设备支持
我们通过组合组件了解到了如何构建一个自定义类型的对象（例如，一个具有点击功能和点击时生成砖块的手部控制器）。组件的好处之一是它们可以在不同的上下文中被重用。我们将 `intersection-spawn` 组件和基于注视点的 `cursor` 组件结合起来，便可以在一点都不改变组件的情况下，实现在移动设备和桌面设备中生成砖块的功能了。

```html
<a-entity id="blockHand" hand-controls="right" controller-cursor intersection-spawn="event: click; mixin: voxel"></a-entity>

<a-camera>
  <a-cursor intersection-spawn="event: click; mixin: voxel"></a-cursor>
</a-camera>
```

## 试试看
[在 GitHub 上查看源码](https://github.com/ngokevin/kframe/tree/csstricks/scenes/aincraft/)

我们的 VR 体素构建器最终使用 11 个 HTML 元素实现。我们可以在桌面或移动设备上预览它。在桌面设备上，我们可以通过拖动和点击来生成砖块；在移动设备上，我们可以平移设备和点击屏幕来生成砖块。

```html
<script src="https://aframe.io/releases/0.5.0/aframe.min.js"></script>
<script src="https://unpkg.com/aframe-teleport-controls@0.2.x/dist/aframe-teleport-controls.min.js"></script>
<script src="https://unpkg.com/aframe-controller-cursor-component@0.2.x/dist/aframe-controller-cursor-component.min.js"></script>
<script src="https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/random-color.js"></script>
<script src="https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/snap.js"></script>
<script src="https://rawgit.com/ngokevin/kframe/csstricks/scenes/aincraft/components/intersection-spawn.js"></script>

<body>
  <a-scene>
    <a-assets>
      <img id="groundTexture" src="https://cdn.aframe.io/a-painter/images/floor.jpg">
      <img id="skyTexture" src="https://cdn.aframe.io/a-painter/images/sky.jpg">
      <a-mixin id="voxel"
         geometry="primitive: box; height: 0.5; width: 0.5; depth: 0.5"
         material="shader: standard"
         random-color
         snap="offset: 0.25 0.25 0.25; snap: 0.5 0.5 0.5"
      ></a-mixin>
    </a-assets>

    <a-cylinder id="ground" src="#groundTexture" radius="30" height="0.1"></a-cylinder>

    <a-sky id="background" src="#skyTexture" theta-length="90" radius="30"></a-sky>

    <!-- Hands. -->
    <a-entity id="teleHand" hand-controls="left" teleport-controls="type: parabolic; collisionEntities: [mixin='voxel'], #ground"></a-entity>
    <a-entity id="blockHand" hand-controls="right" controller-cursor intersection-spawn="event: click; mixin: voxel"></a-entity>

    <!-- Camera. -->
    <a-camera>
      <a-cursor intersection-spawn="event: click; mixin: voxel"></a-cursor>
    </a-camera>
  </a-scene>
</body>
```
[在 CodePen 中打开](https://codepen.io/mozvr/pen/OpbwMJ)

如果你有 VR 头盔（例如 HTC Vive、Oculus Rift + Touch），那么可以找一个[支持 WebVR 的浏览器](https://webvr.rocks/)并打开示例。

如果你想使用桌面或移动设备观看 VR 是什么样的，[可以查看录制好的 VR 动作捕捉和手势演示](https://ngokevin.github.io/kframe/scenes/aincraft/?avatar-recording=recording.json)。
