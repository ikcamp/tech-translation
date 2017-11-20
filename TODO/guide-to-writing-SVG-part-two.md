# 编写SVG的口袋指南（下）
![cover1.png](http://upload-images.jianshu.io/upload_images/3860275-77cbf4b90a14213c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

阅读本文前可以先浏览《[编写SVG的口袋指南（上）](http://mp.weixin.qq.com/s/y4ewNc2sEg_-DDbBEhtoRw)》
原文： [Pocket Guide to Writing SVG](http://svgpocketguide.com/book/)
贡献者： 晨雪
校对者： 逍遥珺、LittlePineapple、可木

## 第三节：工作区

在理解了SVG的基本结构以及了解如何用它来创建基础图形之后，接下来最重要的内容就是如何使用工作区。工作区就是用于绘制图形的坐标系。

想要正常的显示图片只要了解SVG的工作区即可，然而在使用更高级的功能时工作区则变得至关重要。例如，渐变和图案的绘制很大程度上依赖于坐标系。工作区是根据视口和`viewBox`属性的尺寸来定义的。
这只梨恰好能在视口和`viewBox`中完整地展示：

```
<svg width="115" height="190" viewBox="0 0 115 190">
	  <!--<path <pear's drawing path> />-->
</svg>
```

![viewboxpear1.png](http://upload-images.jianshu.io/upload_images/3860275-0d3d03773704a89c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这只梨能够完整地展示在浏览器中，并且可以根据视口的大小变化进行缩放。

### 视口

视口是SVG的可见区域。虽然SVG的宽高可以随意设置，但限制视口将意味着每次只能看到图像特定的一部分。

视口大小可以通过 `<svg>`中的`height`和`width`属性设置。

如果未定义这些值，则视口的尺寸通常由SVG中的其他属性决定，比如最外层的SVG元素的宽度。然而，不定义这些属性的话，我们的图案就可能十分容易地被截断。

### viewBox

遵照`viewBox`的规范，定义了`viewBox`并设置一些值后，一组给定的图形会根据特定的容器元素大小缩放。这些值包括四个数字，它们由逗号或空格隔开：`min-x`，`min-y`，`width`和`height`，通常被用来设置视口的范围。

`min`值表示viewBox应该开始于图像中的哪个点，`width`和`height`确定viewBox的大小。

如果我们选择不定义`viewBox`，图像将不会根据视口的范围进行缩放。

如果设置梨图像`viewBox`的`width`和`height`为50px，随着口大小的缩放，梨的可见部分将会减少。

```
<svg width="115px" height="190px" viewBox="0 0 65 140">
    <!--<path <pear's drawing path> />-->
</svg>
```

![viewboxpear2reduced.png](http://upload-images.jianshu.io/upload_images/3860275-48c3478a5882b5eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`viewBox`中的`min`值定义了`viewBox`在父元素中的原点。换句话说，`viewBox`内的这个点就是你想要开始匹配视区的位置。在上述梨图像中，`min`值设置为0,0（左上）。让我们将它们更改为50，30：`viewBox =“50 30 115 190”`。

```
<svg width="115" height="190" viewBox="50 30 115 190">
    <!--<path <pear's drawing path> />-->
</svg>
```

![viewboxpearminval.png](http://upload-images.jianshu.io/upload_images/3860275-ca6963253186e03d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`viewBox`现在从`x`轴50px的位置开始，从`y`轴30px的位置开始。随着这些值得改变，梨图像的中心区域已经改变。

### preserveAspectRatio

如果视口和`viewBox`具有不同的宽高比，`preserveAspectRatio`属性指引浏览器如何显示图像。

`preserveAspectRatio`有`<align>`、`<meetOrSlice>`两个参数。第一个参数由两部分组成，在视口中指定`viewBox`的对齐方式。第二个是可选的，并指示如何保留宽高比。

`preserveAspectRatio="xMaxYMax meet"`

这些值将`viewBox`的右下角对齐到视口的右下角。`meet`使得`viewBox`在尽可能填满视口的同时保留宽高比。

`<meetOrSlice>`有三个选项：meet（默认），slice和none。虽然`meet`确保图形尽可能多的的完整可见，`slice`尝试按`viewBox`填充视口，因此会在缩放并填充视区后切掉所有超出的部分。`none`表示不保留宽高比，因此很可能得到一个比例失真的图像。

也许这里最直接的值是“none”，它表示不按比例缩放。如果我们增加视口的像素值，下面的樱桃图像会被拉伸并看起来失真。

```
<svg width="500" height="400" viewBox="0 0 250 600" preserveAspectRatio="none">
    <!--<path <cherry drawing path> />-->
</svg>
```

![preserverationone.png](http://upload-images.jianshu.io/upload_images/3860275-edeade02c1bd0f8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面的图像的`preserveAspectRatio`设置为`xMinYMax meet`，它将`viewBox`的左下角对齐到视口（边框围起来的范围）的左下角。`meet`确保图像按照原本的宽高比尽可能地填满视口。

```
<svg width="350" height="150" viewBox="0 0 300 300" preserveAspectRatio="xMinYMax meet" style="border: 1px solid #333333;">
    <!--<path <cherry drawing path> />-->
</svg>
```

![preserveaspect2.png](http://upload-images.jianshu.io/upload_images/3860275-4aacfba5dc7a906f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同一张樱桃图案，将`meet`改为`slice`：

```
<svg width="350" height="150" viewBox="0 0 300 300" preserveAspectRatio="xMinYMax slice" style="border: 1px solid #333333;">
    <!--<path <cherry drawing path> />-->
</svg>
```

![preserveslice.png](http://upload-images.jianshu.io/upload_images/3860275-ddac0c3f86231659.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，现在对齐方式其实是无所谓的。

```
<svg width="350" height="150" viewBox="0 0 300 300" preserveAspectRatio="xMinYMid slice" style="border: 1px solid #333333;">
    <!--<path <cherry drawing path> />-->
</svg>
```

![preservenocorrelate.png](http://upload-images.jianshu.io/upload_images/3860275-c73e736a1e74fcc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面的例子中的`preserveAspectRatio`值为`xMinYMid slice`；樱桃现在沿着视口的`y`轴的中间对齐。

### 坐标系变换

使用SVG变换还可以实现图形其他变换，例如旋转，缩放，移动和倾斜。SVG使用者可以对单个元素或一组元素组使用变换。

这些变换函数包含在要处理的元素的`<transform>`属性中，并且可以在`<transform>`属性中包含多个函数来实现多种变换，例如：`transform =“translate（<tx>，<ty>）rotate（<rotation angle>）”/>`。

需要谨记的一点，变换SVG会影响你的坐标系统或工作区。这是因为transforms通过复制原始内容，然后将转换应用在新系统来创建一个[新的用户空间](http://www.w3.org/TR/SVG/coords.html#EstablishingANewUserSpace)。

下面的图像演示了在一组图形上设置（100,100）的移动时发生的坐标系变换：

![transformcoord.png](http://upload-images.jianshu.io/upload_images/3860275-8b861951e5d103ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

坐标系本身已被移动，青柠和柠檬的图像保持其在该系统内的原始位置。新用户坐标系的原点在原始坐标系中（100,100）的位置。

由于与坐标系的这种关系，许多函数不直接设置移动属性来移动图形。例如，通过设置`scale`为3使得`x`和`y`坐标值乘以“3”，图像与坐标系一起缩放，在这个过程中图像将在屏幕上变化。

在嵌套变换的情况下，效果是叠加的，因此对子元素的最终变换将基于变换之前的累积。

### translate

`translate`函数指定移动形状的细节，这里包含的两个参数表示沿着`x`和`y`轴移动：`transform =“translate（<tx>, <ty>）`”。这两个值可以用空格或逗号分隔。

这里的`y`值是可选的，如果省略，则默认值为“0”。

### rotate

`rotate`中的值将指定形状按照原点（以度为单位）旋转，对于SVG，原点为0,0（左上）：`transform =“rotate（<rotation angle>）”`。

这里还有一种方式来设置`x`和`y`值：`transform = rotate（<rotation angle> [<cx>, <cy>]）`。这些参数建立一个新的旋转中心，而不是使用默认值（0,0）。

这里是旋转20度之前和之后的苹果：`transform =“rotate（20）”`。请注意，此图像不反映此变换产生的坐标变化。

![rotationapple.png](http://upload-images.jianshu.io/upload_images/3860275-08af7dbb1d625e20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### scale

缩放可以通过`scale`函数来调整SVG元素的大小。此函数接受一个或两个参数，这些参数指定沿对应轴的水平和垂直缩放量：`transform =“scale（<sx> [<sy>]）”`。

`sy`值是可选的，如果省略，则假定它等于`sx`，以确保等比的调整大小。

“.5”的`scale`值将使图形的大小缩小为原来的大小的一半，而值“3”将按照图形的初始大小放大三倍。值“4,2”将图形放大为原始宽度的四倍和其原始高度的两倍。

### skew

SVG元素中`skewX`和`skewY`函数可以使图形倾斜或弯曲。这些函数中包含的值表示沿着对应轴的倾斜变换，以度为单位。

这里是一个苹果添加一个`skewX`值“20”之前和之后的样子：`transform =“skewX（20）”`。请注意，此图像不反映此变换产生的坐标变化。

![skewapple.png](http://upload-images.jianshu.io/upload_images/3860275-93e54a524579856a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第四节：填充和勾勒

我们可以使用`fill`填充SVG的内部，`stroke`勾勒SVG的边界。

“[Paint](http://www.w3.org/TR/SVG/painting.html#Introduction)”操作是通过`fill`或`stroke`来改变图形的颜色，渐变或图案。

### fill属性

`fill`属性用于描绘特定图形元素的内部。此属性包括纯色，渐变和图案。

通过审查`fill-rule`中声明的所有子路径和规范确定形状的内部。

当填充形状或路径时，`fill`可以像闭合路径或图形一样填充非闭合路径，而`stroke`不会渲染非闭合路径中最后一个点到第一个点之间的路径。

#### fill-rule

`fill-rule`属性指定了画布的哪部分包含在形状内的算法。但当使用更复杂的交叉或闭合路径时，画布的哪部分包含在形状内就变的不明确了。

这属性包含的值为`nonzero`，`evenodd`，`inherit`。

##### nonzero

`nonzero`值用于判断一个点是否在图形内部，方法是从这个点出发，往任意方向画一条射线，穿过整个图形，然后检测射线与图形路径的交叉点。从0开始计数，每当路径从左到右穿过该线时计数加1，每当路径从右到左穿过该线时计数减1。

统计所有交叉点的计数后计算结果如果为0，则点在图形外部，否则它在图形内部。

![fillrulenonzero.png](http://upload-images.jianshu.io/upload_images/3860275-38f247b658636723.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

基本上，如果内部路径是顺时针方向绘制的，将被视为“内部”，但是如果逆时针方向绘制，则将被视为“外部”，因此将不会被绘制。

##### evenodd

`evenodd`值也用于判断一个点是否在图形内部，方法是从这个点出发，往任意方向画一条射线，穿过整个图形，然后统计射线与图形路径的交叉点的数量。如果结果是奇数则认为点在内部，是偶数则认为点在外部。

![fillruleevenodd.png](http://upload-images.jianshu.io/upload_images/3860275-06d490602ed8448c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

已知`evenodd`规则的具体算法，与内部图形的绘制方向无关，不同于`nonzero`，我们只是在路径交叉时对其进行计数。

如上所述，这个属性一般不需要，但是对于控制一些复杂图形的填充很有用。

##### inherit

`inherit`值表示元素继承其父级的`fill-rule`。

#### fill-opacity

`fill-opacity`值是指内部填充涂料的不透明度。值“0”表示完全透明，“1”表示不透明，0-1之间的值表示基于百分比的不透明度。

### Stroke属性

SVG中有许多与描边相关的属性，可以控制和操作描边的细节。这些属性使得手编SVG得到更好的控制，同时方便编辑现有嵌入图形。

以下是使用葡萄图形为例的内联SVG。使用的属性直接设置在关联图形的元素内。

#### stroke

`stroke`属性定义特定形状和路径的“边框”。

以下葡萄图像具有紫色描边：`stroke =“#765373”`。

![stroke1.png](http://upload-images.jianshu.io/upload_images/3860275-0599859895c65e7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### stroke-width

`stroke-width`值确定葡萄的描边宽度，在葡萄图像上设定为`6px`。

此属性的默认值为1。也可以基于视口的尺寸设置百分比值。

#### stroke-linecap

`stroke-linecap`定义非闭合路径的两端的形状，并且有四个可选值：`butt`，`round`，`square`，`inherit`。

![strokelinecap.png](http://upload-images.jianshu.io/upload_images/3860275-da186430ca25c0f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`inherit`值是指元素继承由其父级的`stroke-linecap`。

下面图片中的茎的`stroke-linecap`值为`square`：

```
<svg>
    <!--<path <path for grapes> />-->
    <!--<path stroke-linecap="square" <path for stem> />-->
    <!--<path <path for leaf> />-->
</svg>
```

![strokesquarestem.png](http://upload-images.jianshu.io/upload_images/3860275-e26389eddf491815.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### stroke-linejoin

`stroke-linejoin`定义笔画的拐角在路径和形状上的外观。

![strokelinejoin.png](http://upload-images.jianshu.io/upload_images/3860275-d63f44dd3b80db47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面是一个`stoke-linejoin`值设置为`“bevel”`的葡萄：

```
<svg>
    <!--<path stroke-linejoin="bevel" <path for grapes> />-->
    <!--<path <path for stem> />-->
    <!--<path <path for leaf> />-->
</svg>
```

![strokebevel.png](http://upload-images.jianshu.io/upload_images/3860275-0a12c1761dfad91d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### stroke-miterlimit

当两条线以锐角相交并且被设置为`stroke-linejoin =“miter”`时，`stroke-miterlimit`属性指定该接合处或角将延伸多远。

这个接头的长度被称为斜接长度，它表示斜接的外尖角和内夹角之间的距离。

该值是斜接长度与`stroke-width`之比。

此属性的最小可能值是1.0。

第一个葡萄图像设置为`stroke-miterlimit =“1.0”`，这创建了一个斜角效果。第二个图像上的`stroke-miterlimit`设置为4.0。

![strokemiterlimit.png](http://upload-images.jianshu.io/upload_images/3860275-b60943d5800e5a95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### stroke-dasharray

`stroke-dasharray`属性将路径转换为虚线，而不是实线。

通过这个属性，你可以指定虚线的长度和虚线之间的距离，值用逗号或空格分隔。

如果提供奇数个值，则重复该列表以产生偶数个值。例如，`8,6,4`变成`8,6,4,8,6,4`，如下面的第二个葡萄图片所示。

在此值中只放置一个数字会导致虚线之间的空格长度等于单个虚线的长度。

![strokedasharray.png](http://upload-images.jianshu.io/upload_images/3860275-aebdb0b4c372fb81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第一个葡萄图片显示偶数列出的值对葡萄路径的影响：`stroke-dasharray =“20,15,10,8”`。

### stroke-dashoffset

`stroke-dashoffset`指定在虚线模式下，虚线的起始位置。

```
<svg>
    <!--<path stroke-dasharray="40,10" stroke-dashoffset="35" <path for grapes> />-->
    <!--<path <path for stem> />-->
    <!--<path <path for leaf> />-->
</svg>
```

![strokedashoffset.png](http://upload-images.jianshu.io/upload_images/3860275-8f19cb0a5ead9a44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上面的示例中，单个虚线长为40px，`dashoffset`为35px。在路径的起点，虚线将不会显示，直到35px位置显示第一个40px虚线的剩余部分，这是为什么第一个虚线显的更短。

### stroke-opacity

`stroke-opacity`属性可以设置描边的透明度级别。

此处的值是0和1之间的小数，0表示完全透明。

## 第五节：文本元素

`<text>`元素定义由文本组成的图形。有许多可选属性用于定制文本，比如渐变，图案，裁剪路径，蒙版或过滤器。

在SVG中提供了非常强大的编写和编辑`<text>`的功能，以创建可扩展文本，就像图形一样，可以在SVG代码中轻松更改和编辑。

在完成本节中的示例时，请记住要注意视口尺寸。如上所述，视口将确定SVG的可见部分，并且在有必要时会根据变化修改视口。

### 基本属性

SVG文本属性处在`<text>`元素内，该元素处在`<svg>`元素内。通过这些属性，我们可以控制文本的一些基本样式，以及清晰地罗列在画布上绘制的详细信息，从而能够完全掌控SVG文本在屏幕上如何放置。

#### x，y，dx，dy

根据建立的`x`和`y`值呈现`<text>`元素中的第一个字母。`x`值确定文本沿x轴的起始位置，`y`值确定文本底部的水平线在y轴的位置。

`x`和`y`在绝对空间中建立坐标，`dx`和`dy`建立相对坐标。与`<tspan>`元素结合使用时会非常灵活方便，我们后面将会进一步讨论。

```
<svg width="620" height="100">
    <text x="30" y="90" fill="#ED6E46" font-size="100" font-family="'Leckerli One', cursive">Watermelon</text>
</svg>
```

![watermelontext1.png](http://upload-images.jianshu.io/upload_images/3860275-d8d670d5ca865f4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面的文本在SVG的视口中30像素的位置开始，文本的底部从该视口顶部90像素的位置开始：`x =“30”y =“90”`。

#### 旋转

可以对单个字母或符号，和（或）作为一个整体元素进行旋转。

`rotate`属性中只有一个值时会导致每个字母按照该值旋转。也可以使用一串值来为每个字母定义和分配不同的旋转值。如果没有足够的值匹配字母数，则其余字符按照最后一个值设置旋转。

下面的文本通过`transform`元素在整个图形上设置了一个旋转，而且还为每个字母设置了值：`rotate =“20,0,5,30,10,50,5,10,65,5”`。

```
<svg width="600" height="250">
    <text x="30" y="80" fill="#ED6E46" font-size="100" rotate="20,0,5,30,10,50,5,10,65,5" transform="rotate(8)" font-family="'Leckerli One', cursive">Watermelon</text>
</svg>
```

![watermelonrotation.png](http://upload-images.jianshu.io/upload_images/3860275-d162e06e805b073c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### textLength & lengthAdjust

`textLength`属性指定文本的长度。通过更改字符之间的间距来适应此属性中指定的长度。

以下示例的`textLength`值为900px。请注意，字符之间的间距已增加以填充此空间。

```
<svg width="950" height="100">
    <text x="30" y="90" fill="#ED6E46" font-size="100" textLength="900" font-family="'Leckerli One', cursive">Watermelon</text>
</svg>
```

![watermelonspacing.png](http://upload-images.jianshu.io/upload_images/3860275-6090395312ed81d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当与`lengthAdjust`属性结合使用时，字母间距和字母大小都会被调整以适配新的长度。

值`“spacing”`导致一个类似于上面的例子的图像，其中字符之间的间距已经扩展以填充空间：'lengthAdjust =“spacing”'。

值`“spacingAndGlyphs”`表示同时调整字母间距和字母大小：`lengthAdjust =“spacingAndGlyphs”`。

![watermelonlengthadjust.png](http://upload-images.jianshu.io/upload_images/3860275-589508014d556f86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### tspan元素

由于SVG当前不支持自动换行符或换行，`<tspan>`元素的重要性不言而喻。 `<tspan>`允许我们通过挑出某些单词或字符来绘制多行文本，然后单独操作。

不需要为这些附加行定义新的坐标系，元素将这些新文本行放置在与上一行文本相关的位置。

`<tspan>`元素本身没视觉的输出，但通过在元素中指定更多具体的方法，可以选出这个特定的文本并控制其设计和定位。

在下面的示例中，“are”和“delicious”位于`<text>`元素中不同的`<tspan>`元素内。在这些tspan中都使用`dy`，使我们可以沿着y轴相对于它前面的词定位该词。

因此“are”位于距离“Watermelons”-30px位置，“delicious”位于距离“are”50px位置。

```
<svg width="775" height="500">
    <text x="15" y="90" fill="#ED6E46" font-size="60" font-family="'Leckerli One', cursive"> Watermelons
        <tspan dy="-30" fill="#bbc42a" font-size="80">are</tspan>
        <tspan dy="50">delicious</tspan>
    </text>
</svg>
```

![watermelontspan.png](http://upload-images.jianshu.io/upload_images/3860275-27a28b8c9a0fa965.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你还可以通过值列表单独移动每个字母，如下面的示例所示。然后后面的字母或符号会根据前面的字母或符号的位置进行定位，现在“delicious”会根据“are”中的“e”进行定位。

![watermelontspan2.png](http://upload-images.jianshu.io/upload_images/3860275-8b283bc61981261d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

包含“are”的`tspan`具有以下`dy`值列表：`dy =“ -  30 30 30”`。

### 间距属性

当在内联SVG中使用`<text>`元素控制字和字母的间距时，有许多属性可用，类似于矢量图形软件的功能。

了解如何使用这些属性有助于确保图形完全按预期显示。

### kerning & letter-spacing

字距调整是指调整字符之间间距的过程。`kerning`属性允许我们按照正在使用的字体中的字距调整表来调整此空间，或设置唯一的长度。

`auto`值表示字间距基于引用的正在使用的字体中的字距间距表。

下面的示例中，`kerning`的值为`auto`，在此实例中由于使用默认值而没有产生视觉影响。

```
<svg width="420" height="200">
    <text x="2" y="50%" fill="#ef9235" font-size="100" font-family="'Raleway', sans-serif" font-weight="bold" kerning="auto">Oranges</text>
</svg>
```

![orangekerning.png](http://upload-images.jianshu.io/upload_images/3860275-2021e7c2e11b0dbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

调整这些字符之间的长度可以通过简单地设置一个数值：`kerning =“30”`。

![orangekerning2.png](http://upload-images.jianshu.io/upload_images/3860275-3f6a61424e7a6e53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`inherit`值也是有效的。

`letter-spacint`具有`normal`，`<length>`或`inherit`这些可选值。这里的数值像`kerning`一样将对间距具有相同的影响。`letter-spacing`属性旨在作为使用`kerning`生效后的字符间距的补充间距。

### word-spacing

`word-spacing`属性指定单词之间的间距。

```
<svg width="750" height="200">
    <text x="2" y="50%" fill="#ef9235" font-size="70" font-family="'Raleway', sans-serif" word-spacing="30">Oranges are Orange</text>
</svg>
```

![orangewordspace.png](http://upload-images.jianshu.io/upload_images/3860275-f8827d813e69f244.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里的`normal`（默认）和`inherit`也是有效值。

### text-decoration

`text-decoration`属性在SVG文本中的选项有`underline`，`overline`和`line-through`。

虽然绘制顺序对SVG中的可见输出并不总是有影响，但是在`text-decoration`中顺序是很重要的。除`line-through`外，所有文本装饰值都应在填充和（或）描边文本之前绘制，这样可以在修饰之前渲染文本。

应在文本填充和（或）描边之后设置`line-through`，这样可以将修饰应用于文本上。

下面看下`text-decoration =“underline”`和`text-decoration =“line-through”`的区别。

![textdecoration.png](http://upload-images.jianshu.io/upload_images/3860275-e978b9c6fac288ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 沿着路径的文本

如上所述，内联SVG为我们提供了类似于矢量图形软件功能的高级定制选项。在SVG代码内部，可以将文本放置在屏幕上任何我们想要呈现的位置。

在进一步进行这种操作时，可以根据给定的`<path>`元素去渲染SVG中的 `<text>`。

### textPath元素

`textPath`元素是此功能的所有魔力所在。虽然通常SVG文本位于`<text>`元素内，但现在它将位于`<text>`元素中的`<textPath>`元素内。

然后，这个`<textPath>`将调用所选路径的`id`，这个`id`正在等待被使用的`<defs>`元素中。

基本语法：

```
<svg>
    <defs>
        <path id="testPath" d="<....>"/>
    </defs>
    <text>
        <textPath xlink:href="#testPath">Place text here</textPath>
    </text>
</svg>
```

下面是在代码中使用的矢量路径：

![pathsimple.png](http://upload-images.jianshu.io/upload_images/3860275-4d031c32e5169921.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在矢量图形软件中生成此路径之后，SVG `<path>`元素代码本身（不包括如上所示的颜色）可以被复制并放置在`<svg>`中的`<defs>`元素内，在上面的代码中也有所展示。

![pathsimpletext.png](http://upload-images.jianshu.io/upload_images/3860275-799ac213e7813a38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<svg width="620" height="200">
    <defs>
        <path id="testPath" d="M3.858,58.607 c16.784-5.985,33.921-10.518,51.695-12.99c50.522-7.028,101.982,0.51,151.892,8.283c17.83,2.777,35.632,5.711,53.437,8.628 c51.69,8.469,103.241,11.438,155.3,3.794c53.714-7.887,106.383-20.968,159.374-32.228c11.166-2.373,27.644-7.155,39.231-4.449" />
    </defs>
    <text x="2" y="40%" fill="#765373" font-size="30" font-family="'Lato', sans-serif">
        <textPath xlink:href="#testPath">There are over 8,000 grape varieties worldwide.</textPath>
    </text>
</svg>
```

#### xlink:href

`<textPath>`中的`xlink：href`属性允许我们引用要将文本渲染在上面的路径。

#### startOffset

`startOffset`属性表示从`path`起始位置开始的文本偏移长度。值“0％”表示`path`的起点，“100％”表示终点。

下面的示例中`startOffset`的值为“20％”，它使得文本在路径的20%处开始渲染。字体大小已减小，以防止其在渲染后溢出视口。

使用`<use>`元素为路径的描边添加颜色可以更清晰的看到偏移改变。

```
<svg width="620" height="200">
    <defs>
        <path id="testPath" d="M3.858,58.607 c16.784-5.985,33.921-10.518,51.695-12.99c50.522-7.028,101.982,0.51,151.892,8.283c17.83,2.777,35.632,5.711,53.437,8.628 c51.69,8.469,103.241,11.438,155.3,3.794c53.714-7.887,106.383-20.968,159.374-32.228c11.166-2.373,27.644-7.155,39.231-4.449" />
    </defs>
    <use xlink:href="#testPath" fill="none" stroke="#7aa20d" stroke-width="2"/>
    <text x="2" y="40%" fill="#765373" font-size="20" font-family="'Lato', sans-serif">
        <textPath xlink:href="#testPath" startOffset="20%">There are over 8,000 grape varieties worldwide.
        </textPath>
    </text>
</svg>
```

![pathsimpletext2.png](http://upload-images.jianshu.io/upload_images/3860275-cc71747384253882.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第六节：高级功能：渐变，图案，剪切路径

### 渐变

SVG渐变有两种类型：线性和径向。线性渐变以直线生成，而径向渐变以圆形生成。

一个非常简单的线性渐变结构如下：

```
<svg>
    <defs>
        <linearGradient id="gradientName">
            <stop offset="<%>" stop-color="<color>" />
            <stop offset="<%>" stop-color="<color>" />
        </linearGradient>
    </defs>
</svg>
```

`<svg>`包含一个`<defs>`元素，它能够创建可重复使用的定义，可以被其他标签引用。这些定义没有可见输出，直到在SVG形状或`<text>`的笔画和（或）填充属性内引用`<defs>`元素唯一ID。这些形状和（或）文本也将位于`<svg>`元素内，`<defs>`元素之外。

一旦渐变被创建并分配了一个ID，它可以通过SVG内的`fill`和（或）`stroke`属性来调用。例如，`fill =“url（#gradientName）”`。

#### 线性渐变

线性渐变沿着直线均匀变化颜色，并且在该直线上定义的每个点（节点）将表示`<linearGradient>`元素内的相关颜色。 每个点，颜色的饱和度是100%，并且它们之间的空间表示从一种颜色到下一种颜色的转变。

#### 停止节点

`<stop>`节点通过`stop-opacity =“<value>”`来设置不透明度。

下面是一个简单的线性渐变的代码，其中两个节点颜色应用于一个矩形：

```
<svg width="405" height="105">
    <defs>
        <linearGradient id="Gradient1" x1="0" y1="0" x2="100%" y2="0">
            <stop offset="0%" stop-color="#BBC42A" />
            <stop offset="100%" stop-color="#ED6E46" />
        </linearGradient>
    </defs>
    <rect x="2" y="2" width="400" height="100" fill= "url(#Gradient1)" stroke="#333333" stroke-width="4px" />
</svg>
```

![gradientpic1.png](http://upload-images.jianshu.io/upload_images/3860275-add929a1e98a311e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`offset`告诉渐变在什么位置分配关联的`stop-color`。

#### x1, y1, x2, y2

x1，y1，x2和y2属性值表示渐变停止（颜色变化）起点和终点。这些百分比将分别沿着适当的轴绘制渐变。

`y`值“100％”和`x`值“0”将生成水平渐变，而反向将生成垂直渐变。将两个值都设置为“100％”（或0以外的任何值）将呈现有角度的渐变。

#### gradientUnits

`gradientUnits`属性定义x1，x2，y1，y2值的坐标系。这里的两个可选值是'userSpaceOnUse'或'objectBoundingBox'。 `userSpaceOnUse`以绝对单位设置渐变协调系统，而`objectBoundingBox`（默认）在SVG形状本身（目标）的边界内建立此系统。

#### spreadMethod

`spreadMethod`属性的值指定,如果渐变开始或结束位置在目标的边界内，渐变将如何在形状内蔓延。如果渐变设置为不填充形状，`spreadMethod`确定渐变应如何覆盖该空白空间。这里有三个选项：'pad'，'repeat'或'reflect'。

`pad`的值（默认）指示渐变的第一个和最后一个颜色分布在未覆盖目标区域的其余部分。`repeat`值指示渐变以重复的方式填充剩余的部分。 `reflect`值指示渐变以镜像的方式填充剩余的部分。

下面的渐变的起点和终点分别是：x1 =“20％”y1 =“30％”x2 =“40％”y2 =“80％”。

![gradientspreadmethod.png](http://upload-images.jianshu.io/upload_images/3860275-e6f48c6a399ef476.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### gradientTransform

`gradientTransform`属性是可选的，允许在绘制之前对渐变进行进一步的变换，例如添加旋转。

#### xlink:href

`xlink：href`属性允许你调用另一个渐变的ID以继承其详细信息，但也可以包括不同的值。

```
<linearGradient id="repeat" xlink:href="#Gradient-1” spreadMethod="repeat" />
```

此渐变从本节开头继承第一个渐变的详细信息，但具有替代的spreadMethod值。

#### Radial Gradients

`<radialGradient>`的大多数属性与`<linearGradient>`的属性相同，除了有一组不同的坐标要使用。

#### cx, cy, r

`cx`，`cy`和`r`属性定义圆的最外部分，渐变的100％`stop-color`将绘制到此值的周长。 `cx`和`cy`定义中心坐标，而`r`设置渐变的半径。

#### fx, fy

`fx`，`fy`属性表示渐变的焦点或最内圈的坐标。基本上，渐变的中心不一定是其焦点，但可以用焦点值改变。

在默认情况下，径向渐变的焦点将居中，焦点属性可以改变这一点。以下图像的焦点值为`fx =“95％”fy =“70％”`。

```
<svg width="850px" height="300px">
    <defs>
        <radialGradient id="Gradient2" cy="60%" fx="95%" fy="70%" r="2">
            <stop offset="0%" stop-color="#ED6E46" />
            <stop offset="10%" stop-color="#b4c63b" />
            <stop offset="20%" stop-color="#ef5b2b" />
            <stop offset="30%" stop-color="#503969" />
            <stop offset="40%" stop-color="#ab6294" />
            <stop offset="50%" stop-color="#1cb98f" />
            <stop offset="60%" stop-color="#48afc1" />
            <stop offset="70%" stop-color="#b4c63b" />
            <stop offset="80%" stop-color="#ef5b2b" />
            <stop offset="90%" stop-color="#503969" />
            <stop offset="100%" stop-color="#ab6294" />
        </radialGradient>
    </defs>
    <text x="20%" y="75%" fill= "url(#Gradient2)" font-family= "'Signika', sans-serif" font-size="200">Cherry</text>
</svg>
```

![gradientfocalpoint.png](http://upload-images.jianshu.io/upload_images/3860275-91bb8e20189335a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在本示例中，焦点移动到图像的右下角。

### Patterns

Patterns（图案）通常被认为是可用于为SVG的填充和笔画着色的更复杂的涂料选项之一。建立基础和理解基本语法可以使这些看起来更复杂的patterns更容易被使用。

这里是一个应用于矩形的基本pattern语法：

```
<svg width="220" height="220">
    <defs>
        <pattern id="basicPattern" x="10" y="10" width="40" height="40" patternUnits="userSpaceOnUse">
            <circle cx="20" cy="20" r="20" fill= "#BBC42A" />
        </pattern>
    </defs>
    <rect x="10" y="10" width="200" height="200"
          stroke="#333333" stroke-width="2px" fill="url(#basicPattern)" />
</svg>
```

![patternbasic1.png](http://upload-images.jianshu.io/upload_images/3860275-25fe90890fca02ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 基本属性

patterns的属性和值定义了“画布”，设计和整体定位。Patterns可以由路径和（或）形状组成，可以绘制文本，甚至可以嵌套在另一个pattern中。

#### x, y, width, height

`<pattern>`元素内的x和y属性定义了pattern将开始的形状。在`<pattern>`元素中使用的宽度和高度定义了分配的pattern空间的实际宽度和高度。

上面引用的“basicPattern”包含以下值：`x =“10”y =“10”width =“40”height =“40”`。该pattern将从离x轴起点10px处开始，从离y轴起点10px处开始，并且实质上创建一个宽度为40px，高度为40px的“画布”。

#### patternUnits

`patternUnits`属性定义供x，y，width和height使用的坐标系。这里的两个选项是`userSpaceOnUse`和`objectBoundingBox`（默认）。

`userSpaceOnUse`将生成一个pattern坐标系，该坐标系由引用`<pattern>`的元素的坐标系确定，而`objectBoundingBox`将该映射坐标系建立为应用该pattern的元素的边界框。

#### patternContentUnits

`patternContentUnits`属性值与`patternUnits的`值相同，除了用于定义图案本身内容的坐标系。

这个值与`patternUnits`不同，默认为`userSpaceOnUse`，这意味着除非指定这些属性中的一个或两个，否则在`<pattern>`中绘制形状所使用的坐标系会区别于`<pattern>`元素正在使用的坐标系。

在`<pattern>`元素中定义`patternUnits =“userSpaceOnUse”`可简化此过程，并确保一致的工作区。

### 嵌套Patterns

Patterns也可以嵌套创建一个更独特和详细的设计。

这里是一个基本的嵌套pattern的结构：

```
<svg width="204" height="204">
    <defs>
        <pattern id="circlePattern"
                 x="4" y="4" width="75" height="75"
                 patternUnits="userSpaceOnUse">
            <circle cx="12" cy="12" r="8"
                stroke="#ed6e46" stroke-width="3" fill="#765373" />
        </pattern>
        <pattern id="rectPattern"
                 x="10" y="10" width="50" height="50"
                 patternUnits="userSpaceOnUse">
                <rect x="2" y="2" width="30" height="30"
                  stroke="#bbc42a" stroke-width="3" fill="url(#circlePattern)" />
        </pattern>
    </defs>
    <rect x="2" y="2" width="200" height="200"
          stroke="#333333" stroke-width="3" fill="url(#rectPattern)" />
</svg>
```

`<defs>`元素包含两个patterns。在`<defs>`中，矩形的pattern通过`fill`调用圆形图案，然后主矩形也通过`fill`调用矩形pattern，用嵌套pattern绘制主形状的内部。

![patternnest.png](http://upload-images.jianshu.io/upload_images/3860275-8c330af408d05379.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 剪切路径

剪切路径会限制绘制施加于SVG的区域。绘制在剪切路径设置的边界以外的任何区域都不会被渲染。

为了演示此功能的能力，让我们使用由“Apples”文本组成的剪切路径应用于番茄色矩形和绿色圆形。

下面是没有应用剪切路径的形状，设置为延伸到视口之外。

![clippingshapes.png](http://upload-images.jianshu.io/upload_images/3860275-744d0bf8aa1b795a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在，看下应用“Apples”文本到这个“画布”的代码。

```
<svg width="400px" height="200px">
    <clipPath id="clip-text">
        <text x="0" y="50%" fill="#f27678" font-size="120px" font-family=" 'Signika', sans-serif">Apples</text>
    </clipPath>
    <rect x="0" y="0" width="200" height="200" fill="#ed6e46" clip-path="url(#clip-text)" />
    <circle cx="310" cy="100" r="135" fill="#bbc42a" clip-path="url(#clip-text)" />
</svg>
```

![clippingtext.png](http://upload-images.jianshu.io/upload_images/3860275-ae5114f7fbab0ed3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

剪切路径在`<clipPath>`元素中定义，然后通过图形引用其唯一`id`调用。



## 总结

内联SVG是非常有用的编辑功能，并能够让开发者可以独立的访问所有图形元素。这种代码编辑方式的优点：不失真的缩放图形，可搜索，增强可访问性。

可能需要花一段时间来练习编写SVG，但是编写SVG会让你的代码越来越短，越来越高效，然后再探索[SMIL 动画](http://www.w3.org/TR/smil-animation/)，并尝试使用[SVG元素的CSS样式](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/SVG_and_CSS)。

希望本指南既是一个有价值的参考，同时就理解构建和操纵内联SVG的强大潜力而言，也希望能带来些启发。

相关信息及后续，请访问[本书的网站](http://svgpocketguide.com/)，如果你对这本书有任何问题或意见，可以通过[Twitter](https://twitter.com/JoniTrythall)或电子邮件[info@jonibologna.com](info@jonibologna.com)联系我。

![theend2.png](http://upload-images.jianshu.io/upload_images/3860275-95a09160aa359157.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


