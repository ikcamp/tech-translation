* 原文地址：[javascript-2017-beyond-browser](http://developer.telerik.com/topics/web-development/javascript-2017-beyond-browser/)
* 原文作者：[TJ VanToll](http://developer.telerik.com/author/tvantoll/)
* 译者：[赵安冬](#)
* 校对者：[金晶](#)


技术世界在发展，JavaScript也在同步发展。JavaScript在软件世界建起地盘的头几年，它从没想过涉足服务应用程序、移动端应用程序以及机器人之类业务。今天，随着JavaScript的发展，这门语言已经进入了聊天机器人、虚拟现实以及物联网等新领域。

除了不断开拓新领域，在服务端、移动端以及桌面端应用等生态中，JavaScript的地位也越来越稳固。在本文中，我们将首先回顾去年所做的若干预测，然后展望2017年JavaScript会在浏览器之外开拓哪些新地盘。先来看看JavaScript在服务端应用程序中的情况吧。

## Node.js

Node.js是构建服务器端应用程序的开源运行时库，这类JavaScript代码不是在浏览器中运行的。在过去的几年里，Node已经从初创公司中流行的技术框架演变为各种规模公司所使用的主流开发技术。

Node的包管理工具npm也不再是托管服务端应用程序模块的工具，而是转变为了分发JavaScript代码的规范化的工具。也许npm上的包的数量是最能表现Node的发展趋势。在去年的预测中，我们制作了下面的图表，比较了各种语言中包管理的数据，显示出了npm的优势。

![](http://ofyfg9y7t.bkt.clouddn.com/module-counts-2016.jpg)
 
截至2015年12月，modulecounts.com的模块数量

在过去一年里，npm的增长并没有放缓的迹象。事实上，npm包的数量从20万增长到了大约35万，促使整个Y轴比例尺都被迫调整。
 
 ![](http://ofyfg9y7t.bkt.clouddn.com/module-counts-2017.jpg)

截至2016年12月，modulecounts.com统计的包数量

增长背后的因素有很多，其中一个就是很多公司在基础服务中使用了Node。这同我们去年预测的结果相吻合。

> “在2016年，我们可以预见到更多的公司将会进一步采用Node和他的包管理工具npm。因为Node的长期支持计划，微软、IBM、Intel、Progress等大公司将会继续使用Node，用来替代一些.NET、Java之类的传统企业解决方案。”

从Node的增长趋势来看，上面的预测结果并不意外。关于Node的案例研究表明，一部分中等规模的公司已经开始使用Node，包括Netflix，GoDaddy和Capital One等。

Node在关键基础设施中得到了应用，其中最惹人注目的非NASA莫属了。你也可以看看NASA对Node的研究，在这里我只摘录一段话。

> “在考虑宇航员的生命安全时，轻微的打嗝或者服务中断都会酿成生死事故。从EVA（舱外活动）的数据到太空中宇航员的各个领域里，Node.js都有助于确保所有人与事的安全。”

但是Node的发展并非只有NASA帮忙。Node的包管理工具npm已经成为了存储跨环境JavaScript代码的不二选择，包管理工具的统一化反之也推动了Node的发展。

在本文中，我们讨论的每个框架、每项技术都使用npm来存储和分发其源代码。在npm中搜索“jquery”，“polymer”，“react”，“cordova”或“nativescript”，你大概就能了解npm现在的规模。随着JavaScript的普及，npm也越来越受欢迎。npm越普及，Node.js发展越快。我们相信，这个趋势将会在一段时间内继续保持下去。

![](http://ofyfg9y7t.bkt.clouddn.com/angular-npm-downloads.jpg)

 
在npmjs.com搜索“angular”得到近1万个结果。Angular是通过npm分发的众多类库之一。

在2017年，我们相信更多的公司将从传统的开发方式（比如JAVA和C#）切换到Node。我们相信TypeScript也将有助于推动Node的成长，因为它对Java和C#的开发人员更加友好。Node对LTS版本的支持承诺也将有助于这一趋势，因为它保证了这些公司使用的版本会在未来几年得到持续的支持和维护。

总的来说，大公司不喜欢维护多套开发系统和语言，而借助Node，这些公司可以用单一语言来整合所有的开发系统，还不仅仅是是服务器端的代码。下来我们看看JavaScript是如何影响移动端的。

## PhoneGap和Cordova

PhoneGap以及它的基石Cordova，是JavaScript进入原生开发领域的初次尝试。Cordova将web代码封装在WebView中，借由WebView来驱动原生的移动应用。这种方法允许Web开发人员使用他们已经掌握的技能（即JavaScript）来开发移动应用程序，正因为如此，在很多年里，Cordova都是开发移动应用的重要选择。

但是这种情况开始慢慢改变了。今天，Cordova面临了很多替代方案的挑战，它们大部分使用与Cordova类似的基于JavaScript的方案。也许Cordova最大的挑战来自谷歌主导的Progressive Web Apps（简称PWAs）。

![](http://ofyfg9y7t.bkt.clouddn.com/progressive-web-apps.jpg)

Google的Progressive Web Apps主页

PWAs为web世界了带来了很多近似原生的功能，比如推送通知、离线访问和主屏幕图标等。去年，我们预测Google将开始慢慢推行PWA方法。事实证明，这一预测还是过于保守，因为Google已经明确表示，他们将开展多种活动来推广PWAs。在最近的Chrome开发者峰会，以及今年的Google I/O会议上，谷歌都为PWAs安排了大量讨论。

PWAs和我们的讨论息息相关，因为它已经开始蚕食 Cordova的领域——需要使用原生功能的Web应用程序。如果你的web应用需要离线访问或者推送通知的功能，选择基于PWA 而不是 Cordova会是个更好的方案。尽管很难测量有多少人在混合应用中选择了PWAs，但已经有很多证据表明Cordova的使用量正在缩减。下面是最近两年Cordova每周被人们下载的次数。你可以看到，尽管Cordova下载数没有大幅波动，但增幅已经没有那么明显了。
 
 ![](http://ofyfg9y7t.bkt.clouddn.com/cordova-downloads-per-week.jpg)


从2014年12月至2016年12月，“cordova”npm软件包的每周下载量。（数据来自npm-stat.com）

衰退还有一个原因。尽管我们认为PWA正在蚕食Cordova的份额，但我们也相信，移动领域中更新的开发方式也在蚕食了Cordova的份额。

## Native mobile apps

JavaScript驱动的原生移动应用，这种概念由Appcelerator倡导，借助Facebook的React Native和Progress的NativeScript，目前已经流行开来。用JavaScript开发的原生应用程序不使用WebView，因此，不需要考虑基于Cordova的应用程序遇到的Web性能问题 。

在去年的讨论中，我们预测2016年将会是这些框架成熟并广泛使用的一年，现在看来这些预测是准确的。在过去的两年里，React Native的每周下载次数在持续增加。
 
 ![](http://ofyfg9y7t.bkt.clouddn.com/react-native-downloads.jpg)

从2014年12月到2016年12月，“react-native”npm软件包的每周下载量。（数据来自npm-stat.com）

NativeScript也有同样的趋势。
 
 ![](http://ofyfg9y7t.bkt.clouddn.com/nativescript-downloads.jpg)
 
从2014年12月至2016年12月，“nativescript”npm软件包的每周下载量。（数据来自npm-stat.com）

变化不只体现在这些JavaScript驱动的原生框架的下载数据提升上，最近的一项调查研究（State of JavaScript 2016）表明，JavaScript开发人员对React Native和NativeScript都很感兴趣。
 
 ![](http://ofyfg9y7t.bkt.clouddn.com/state-of-js-mobile-results.jpg)

State of JavaScript 2016对移动开发领域兴趣调查的结果

对JavaScript调查分析总结出了这些结果。

> 在兴趣分数上，“Cordova”和“PhoneGap”的得分很低，这也许是它们的性能问题导致的。虽然Cordova和PhoneGap所依赖的手机浏览器和JavaScript引擎有了很大提升，但还是不如运行原生代码（如React Native）。

在2017年，随着越来越多的JavaScript开发人员开始尝试构建原生应用，我们期待这些使用JavaScript构建原生应用的框架能够加速发展。React框架的快速发展也将使React Native获益，而NativeScript则宣布在5月份完成Angular 2的支持，很多项目也会从Angular 1升级到Angular 2，NativeScript也将会从中获益。我们也希望JavaScript驱动原生框架能够吸引原生iOS和Android开发人员，因为它允许你只用一份代码就能在两个平台上构建真正的原生应用程序。

JavaScript越来越多地侵占了曾经以Objective-C和Java等语言为主的移动端领域。但这不是JavaScript正在侵入的唯一新领域。下面我们将讨论转到桌面应用程序 。

## 桌面应用

根据传统，如果要构建Windows或Mac应用程序，就要使用针对专门平台的工具，如WPF和Windows Forms，或者采用跨平台的方案，比如Java或Adobe Air 。不过，像上文中讨论的其他软件生态一样，基于JavaScript的解决方案也在蚕食这个领域。

在去年的讨论中，我们讨论了用来构建桌面应用程序的两个最流行的JavaScript框架——NW.js和GitHub的Electron，同时判断其使用量在2016年将大幅增长。从现实来看，增长已经出现了，Electron现在也已经成为开发基于JavaScript的桌面应用程序的重要选择。

如果比较“electron”和“nw”在npm上下载量，你将会看到“electron”（红线）和React Native的趋势类似，而NW.js的下载曲线相对平坦。
 
 ![](http://ofyfg9y7t.bkt.clouddn.com/electron-vs-nw-downloads.jpg)
 
从2016年9月至十一月2016年，“electron”和“NW”npm包的周下载量。（数据来自npm-stat.com）

2015年12月，在GitHub上，Electron有2万个 star，NW.js有2万5千个；今天，Elecron拥有近4万个star，NW.js则刚刚超过3万。

Electron也被主流桌面应用所接纳。该框架现在为Visual Studio Code提供支持。Visual Studio Code由微软提供，是广受欢迎的编辑器，到4月份已经获得了超过五百万用户。Electron还在React和Angular社区做了推广，所以在这两个框架中使用Electron的教程可以很容易地在网上被找到。

我们预计，Electron在2017年将会继续占据统治地位。我们期待Electron能够跟最流行的框架（主要是React和Angular）进一步集成，从而获得软件供应商更多的关注。而且随着JavaScript继续侵入传统上由Java和基于Microsoft技术主导的领域，我们希望Electron将继续被用作WPF，Java和Adobe Air等开发的替代品。

使用单一语言完成你的所有开发需求，这个方案不但有足够吸引力，还采取了JavaScript的一些最新的开发方式。最后，让我们看看JavaScript在一些新的软件领域的表现。

## JavaScript的新边界

如果你向分析师询问发展中国家的发展情况，他们脱口而出的是虚拟现实，聊天机器人和物联网（IoT）等一系列流行概念。

在所有这些新技术中，JavaScript在聊天机器人这个领域是最重要的，人们使用JavaScript来开发从简单的Slack机器人乃至进行商业交易的复杂机器人。在聊天机器人领域中，大多数的框架在他们的SDK中都集成了Node库，包括Botkit，Microsoft的Bot Framework和Facebook的wit.ai。微软的Bot框架的文档甚至介绍了为什么要用Node来开发机器人。

> “基于Node的Bot Builder是很有力的构建机器人的框架，可以处理各种形式的交互，给出更多的引导，它可以将这些可能性很清楚地展示给用户，它使用一些框架（如Express和Restify），可以让开发人员用熟悉的方式来开发机器人。”

重用JavaScript同样为许多流行的IoT库（如Losant和zetta）以及Leap Motion等设备提供了Node API。 Chrome浏览器团队和A-Frame框架团队就是其中的典型，还有不少团队在虚拟现实中使用JavaScript。

![](http://ofyfg9y7t.bkt.clouddn.com/chrome-vr.jpg)
 
Google Chrome小组拥有一系列令人印象深刻的虚拟现实实验，它们都是基于JavaScript构建的，你也可以自己尝试。

然而在C ++，Python和C＃主导 的领域，JavaScript并不具有很大的优势。比如，Oculus Rift设备主要使用C ++，Microsoft的HoloLens则需要你用C＃编写。

我们预计这一趋势将在2017年开始发生改变。随着JavaScript的普及以及运行速度的提高，JavaScript将继续延伸到像VR和物联网这样的领域。随着新的软件开发生态系统的涌现，我们期待JavaScript能够快速上升为一等公民。

## 万金油 JavaScript

10年前，在服务器上使用JavaScript是不可想象的; 今天，Node拥有350万用户，年增长率达100％。5年前，使用JavaScript来驱动原生iOS或Android应用程序还只是星星之火， 今天NativeScript和React Native正以惊人的速度增长。3年前，使用JavaScript构建桌面应用程序很少见， 今天Electron每月下载超过1万次。

JavaScript不会用于所有场景下编程，因为许多其它语言更适合于解决某些具体场景下的问题。但是不管采用什么开发平台，JavaScript的广泛使用一定会是个重要因素。关于这个话题，Jeff Atwood有一句广为流传的话，也许用它来结尾再合适不过了，因为他的发言总是充满了预见性。

> “可以用JavaScript编写的应用程序，最终都将用JavaScript编写。”

原文地址：http://developer.telerik.com/topics/web-development/JavaScript-2017-beyond-browser/
