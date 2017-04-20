 * 原文地址：[Getting to know CSS Grid Layout](https://cm.engineering/getting-to-know-css-grid-layout-818e43ca71a5)
 * 原文作者：[Chris Wright](https://cm.engineering/@cwrightdesign?source=post_header_lockup)
 * 译者：[郭华翔](#)
 * 校对者：[](#)


# 翻译 | CSS栅格（CSS Grid）布局入门
> 原文链接 [https://cm.engineering/getting-to-know-css-grid-layout-818e43ca71a5](https://cm.engineering/getting-to-know-css-grid-layout-818e43ca71a5)

![banner](http://n1image.hjfile.cn/res7/2017/04/20/af1c9adfa03fdb0ee5343d933d967930.jpg)

CSS栅格布局是浏览器Flexbox布局之后最重要的布局方式。我们可以忘记过去15年所使用的那些已经成为习以为常的各种“神奇数字”，hacks和变通布局方式。栅格布局提供了非常简单的声明布局方式，之后再也不需要一些常见的主流css框架，也能减少很多手动实现的布局方式

如果你以前不熟悉CSS栅格布局，那么你可以开始了解它了。它是一种适用于容器元素，并能指定子元素的间距、大小和对齐方式的布局工具。

CSS栅格布局赋予我们更强大的能力——大家熟悉的水平垂直居中布局，不需要增加标记语言就能做到。同样，这也能让我们不需要媒体查询就能根据可用空间自动适应。

## 学习的最低要求
首先一点学习栅格布局有不少新语法需要学习，但是你只需要稍微看下就能上手。本文将会用示例带你学习CSS栅格布局各种各样重要的入门概念。

## 浏览器兼容性
CSS栅格布局从Safari 10.1, Firefox 52, Opera 44, Chrome 57开始支持，微软Edge在Edge 15会更新对栅格布局的支持。

微软的浏览器（IE10–11和Edge 13-14）有一种比较旧的实现，所以有不少限制，我们会简单介绍新的实现方式和老的实现方式之间的区别，这样你能知道如何规避他们。

对于大多数布局，我们会使用下面的query特性来让老的浏览器对他们理解的特性也能工作：

```css
@supports (display: grid) {
    .grid {
        display: grid;
    }
}
```

不支持浏览器`@supports`或不支持栅格的浏览器将不会生效。

为了能正确展示文中的示例，你需要使用[支持栅格布局的浏览器](https://igalia.github.io/css-grid-layout/enable.html)。

## 创建带有间距（gutter）的的两列（column）栅格
为了演示CSS栅格布局是如何定义列，我们从下面的布局开始：
![grid-template-columns 和 grid-gap](http://n1image.hjfile.cn/res7/2017/04/20/5eb65d448f5f322c1738397169608a66.jpg)
[使用grid-template-columns 和 grid-gap创建带间距的两列布局]

为了创建上述栅格布局，我们需要使用`grid-template-columns`和`grid-gap`。
`grid-template-columns`表示栅格中的列是如何布局的，它的值是以空格分割的一连串的值，这些值标示每列的大小，值的个数表示列的数目。

例如，四列250px宽度的栅格布局像下面这样：

```
grid-template-columns: 250px 250px 250px 250px;
```

也可以使用`repeat`关键字表示：

```
grid-template-columns: repeat(4, 250px);
```

### 定义间距
`grid-gap`定义了栅格布局的间距大小，接收一个或多个值，如果定义两个值则表示列（column）和行（row）的间距大小。

在两列布局示例中，我们可以如下使用：

```
.grid {
  display: grid;
  grid-template-columns: 50vw 50vw;
  grid-gap: 1rem;
}
```

不幸的是，这个间距将会占用容器元素的整体宽度，计算出来就是`100vw + 1rem`，最终这个布局会导致出现水平滚动条。
![viewport导致的水平滚动条](http://n1image.hjfile.cn/res7/2017/04/20/874a4e878340132738c4d7eb3bc7af30.jpg)
[通过viewport单位创建带间距栅格导致的水平滚动条]

为了解决这个空间溢出问题，我们需要些不同的方法来处理，需要用分数单位或者说是**FR**。

### 分数单位
分数单位标示占用可用空间的份额，如果900px是可用空间，其中的一个元素占有1份，另外的元素占有2份——那么第一个元素的宽度会是900px的1/3，另外的元素是900px的2/3。
修改后用分数代替view-port单位的新代码如下：

```
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 1rem;
}
```

### 内容对齐
为了对齐示例中的内容，我们在子元素上使用grid布局，并加上对齐属性来定位他们到指定轨道(track)，轨道就是一个栅格的列或行的某个位置的常见的名称。栅格跟Flex布局一样，有一系列对齐的属性——共有四种值——`start`, `center`, `end`, 和`stretch`，分别对应其子元素所在的轨道。`stretch`跟其他不太一样，它会将元素从所在轨道的头拉伸到尾。

![align-items 和 justify-content](http://n1image.hjfile.cn/res7/2017/04/20/cf6d2c4188e435b0afdc9553a8e6f80a.gif)

[align-items 和 justify-content]

例子中我们要将内容水平和垂直居中，可以通过在容器上设置下面这些属性：

```
.center-content {
    display: grid;
    align-items: center;
    justify-content: center;
}
```
[示例地址](http://codepen.io/chriswrightdesign/pen/51f1dcaa9b6d815a240420106cd09a0b?editors=1100)

### 使用旧的栅格布局实现两栏布局
如果使用旧的栅格布局方式创建，我们得考虑其是否可行。旧的布局方式不仅没有`grid-gap`，而且你需要在每一个栅格元素上声明栅格元素的起始位置，否则默认会设置为1，这样所有的栅格都会堆在第一列。

旧版本的布局方式需要通过增加间距作为栅格轨道的一部分，也需要设置每个栅格从哪里开始：

```
.grid-legacy {
   display: -ms-grid;
   -ms-grid-columns: 1fr 1rem 1fr; // 取代 gap 间距
}
.grid-legacy:first-child {
   -ms-grid-column: 1;
}
.grid-legacy:last-child {
	-ms-grid-column: 3;
}
```

### 旧的布局方式实现对齐和全高度
旧的布局方式跟IE 11中Flexbox有一样的问题，[在容器上设置最小高度（min-height）不一定会生效](https://github.com/philipwalton/flexbugs#3-min-height-on-a-flex-container-wont-apply-to-its-flex-items)。这个问题通过栅格布局实现起来更方便。

为了实现这个效果我们在父容器的行属性上使用`minmax`方法，`minmax`指定了行或列的最大和最小值。

```
-ms-grid-rows: minmax(100vh, 1fr);
```

在子元素上我们能指定为grid，设置行和列为`1fr`：

```
.ms-cell {
   -ms-grid-columns: 1fr;
   -ms-grid-rows: 1fr;
}
```

最后，因为我们不能像Flexbox或最新栅格布局那样根据父元素对齐，我们必须使用元素自身的对齐方式来对齐：

```
.ms-align-center {
    -ms-grid-column: 1;
    -ms-grid-column-align: center; // 类似现代浏览器grid中的 align-self
    -ms-grid-row-align: center; // 类似现代浏览器grid中的 justify-self
}
```

[旧的两列布局示例](http://codepen.io/chriswrightdesign/pen/08b9afe6847968c1f319a5ce51e9eb95)

到此我们实现了如何创建列、实现间距、内容对齐及对老旧浏览器的支持。接下来让我们实验下如何通过grid实现留白。

## 通过CSS栅格实现留白（Negative Space）
栅格布局允许你通过`grid-column-start`属性指定列开始的位置，所以就有了可以通过栅格创建留白的可能性。

![使用grid-template-columns和grid-column-start创建留白](http://n1image.hjfile.cn/res7/2017/04/20/a6a9b8874569264a49475a76c8f3e4f9.jpg)
[使用grid-template-columns和grid-column-start创建留白]

创建留白的一种方式是在列的真实位置上设置一个数字，空出栅格元素的原始空间, 栅格元素也会被push到新的栅格列。

![grid-column-start push](http://n1image.hjfile.cn/res7/2017/04/20/504a4d5a4b26296e84acd811ef587da4.gif)
[随着grid-column-start push 第一项]

在上面的留白示例中，html结构中用一个div包裹另外一个div：

```
<div class="grid">
    <div class="child"><!-- 内容 --></div>
</div
```

栅格像这样设置：

```
.grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
}
```

为了让子元素从右侧开始，我们设置子元素从第2列开始：

```
.child {
    grid-column-start: 2;
}
```

**注意**：在Firefox 52中的一个差异导致一个垂直对齐问题——[基于FR单位的行不会拉伸得跟整个窗口一样高](https://bugzilla.mozilla.org/show_bug.cgi?id=1346699)。为了解决（address）这个问题我们设置子元素为栅格项，并给每一行设置一个想要的高度：

```
.l-grid--full-height {
    grid-template-rows: minmax(100vh, 1fr);
}
```

[设置留白示例](http://codepen.io/chriswrightdesign/pen/5a9bc28c370ad5155515c3c7a19653d8)

## 用内容死区（content dead-zones）创建空白
在四列布局中，给本来在第三列的栅格项上设置`grid-column-start:2;`，那么会找到下一个可用的第二列来填充空间。

栅格轨道会跳过某些列，直到找到下一列。我们可以利用这个方法在栅格内创建空白，没有内容的栅格也会被分配。
[\[创建空白示例\]](http://codepen.io/chriswrightdesign/pen/427c3e51c320e04662be56dad44dee74)

![](http://n1image.hjfile.cn/res7/2017/04/20/90fcea0b53d4712f827c74ec950cebd1.jpg)
[使用grid-template-columns 和 grid-column-start创建空白]

## 创建行
如果我们想分割布局为四份，我们目前所了解的关于列的布局方式对行同样有用：

```
.grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 250px 250px;
}
```

![](http://n1image.hjfile.cn/res7/2017/04/20/1e6446cad82bb945bb7f559fa0f869e8.jpg)
[同时使用grid-template-columns 和 grid-template-rows创建栅格布局]

理想情况下这个示例是没问题的。因为此时每个栅格项的内容足够少而不会撑开每行。但随着内容的变化，一切都不一样了。当示例中的内容超出指定行的大小后，看下会发生什么：

![](http://n1image.hjfile.cn/res7/2017/04/20/a2bce80e7d94d2b9f2931e061dff7780.jpg)
[内容超出声明的行高]

我们创建了250px高的两行，如果内容超过每行的高度，将会打破布局并和后面的行的内容重叠。并不是一个我们想要的结果。

## 灵活的设置最小值
这里的场景是设置了最小值，但是允许我们根据内容弹性变化。这里我们通过上面旧浏览器示例中的`minmax`关键字实现。

```
.grid {
    grid-template-rows: minmax(250px, auto) minmax(250px, auto);
}
```
[创建有最小值的弹性行](http://codepen.io/chriswrightdesign/pen/447e07c84aac9abb5514f9b9fbb18459)


现在我们已经了解了创建带有内容的行的基础方法，我们开始来创建水平和垂直交错的更复杂栅格布局。

![](http://n1image.hjfile.cn/res7/2017/04/20/cab8e4f4681e3841bcd7865650240058.jpg)
\[使用grid-column-start和span关键字创建复杂栅格布局[Unsplash](https://unsplash.com/)]

## 创建更复杂的栅格
我们开始创建更复杂的栅格布局。将栅格中的每个栅格项设置成占据多条轨道，在一列内，我们能通过`grid-column-start`和`grid-column-end`实现，或者通过如下所示更简单的写法：

```
grid-column: 1 / 3;
```

用这种实现方式的弊端是并不是很“模块化”，为了定位每块内容需要写很多代码。`span`关键字更符合模块化的思路，因为我们能放在任何地方，让栅格来控制他。我们可以定义栅格项的开始位置，及其占据的轨道数：

```
.span-column-3 {
    grid-column-start: span 3;
}
```

任何添加该class的栅格将会从其开始位置，占据三个轨道。

[\[通过span实现的复杂栅格\]](http://codepen.io/chriswrightdesign/pen/35102dd4a2cf7fbadb33dd53cb88787d)

## 使用span设计一个布局
我们能设计一个多轨道布局，通过将栅格布局打散成最小单位。本示例中的最小单位是图中高亮的部分。

![](http://n1image.hjfile.cn/res7/2017/04/20/028fe538b6c135f129441ad7f7a0f3a6.jpg)
[通过最小栅格单位结合span创建更大的栅格]

围绕最小单位，我们能灵活的使用span来创建一些有意思的布局，因为span是可以叠加的——你可以结合列和行的轨道在栅格中创建多层级。

## 不需要媒体查询（media queries）的弹性栅格
虽然上面说到的例子能在可用空间内适应变化，但是没有一个是专门为空间变化设计的。栅格有两个非常有用的特性来适应可用空间的变化。这两个属性叫`auto-fit`和`auto-fill`，像下面这样结合`repeat function`和`minmax function`使用：

```
grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
```

这些值代替了repeat中的数字，并计算在每条轨道上会填充多少行或列。二者之间最大不同是当一条轨道上空白的溢出时的他们的处理方式不同。

`auto-fit`尝试放最大的数量的重复元素待一列内且该列能自动适配而不溢出。当没有足够的空间来放置更多的元素时，之后的元素将会放到下一行，不能填满的空间将会空着。

![auto-fill](http://n1image.hjfile.cn/res7/2017/04/20/8c9786de6f128e9d4c3a8519f001baf1.gif)
[示例：auto-fill. auto-fill会保留后面空间，auto-fit会让空白收缩为0px]

`auto-fill`的表现跟`auto-fit`类似，但是任何的空白空间都会自动收缩，同时这一行的元素也会被拉升——类似flexbox的效果，列会随着可用空间变小发生折叠。

![grid-auto-fit示例](http://n1image.hjfile.cn/res7/2017/04/20/c3a4e639995fd0ce30dbd8c561fd8a26.gif)

[grid-auto-fit示例]

依赖媒体查询的布局跟窗口大小关系很大，这不适合模块化——系统内的组件应该随所处的可用空间而变化。那么在实践中会是什么样的呢？

![auto-fit](http://n1image.hjfile.cn/res7/2017/04/20/6a648b1e569c4400d70f85708d571e6f.gif)
[grid auto-fit的真实示例]

[\[栅格auto-fit示例\]](http://codepen.io/chriswrightdesign/pen/f93ce007ae9cc48eab4c577f1efac382)

## 这只是个开始

我们已经经历了快十五年的CSS浮动为主的布局方式，我们上面学习了几乎所有你能用float实现的布局——CSS栅格布局是这个领域的新代表，目前还没有很多的实践和研究。

现在最重要的步骤是开始使用它。在构建、创建更多高级布局的时候会很方便。栅格布局还有不少未知领域，一旦我们更好地理解其能力并开始与其他特性结合，我们便能用更少代码创造更多有趣、灵活的布局，并能减少些框架抽象的麻烦。

如果你感兴趣并想进一步探究CSS栅格，可以试下[Rachel Andrew的例子](http://gridbyexample.com/)，这里面通过带解释说明的实例探讨了CSS栅格布局的每一个特性。

