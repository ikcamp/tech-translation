* 原文地址：[javascript-2017-beyond-browser](http://developer.telerik.com/topics/web-development/javascript-2017-beyond-browser/)
* 原文作者：[TJ VanToll](http://developer.telerik.com/author/tvantoll/)
* 译者：[赵安冬](#)
* 校对者：[?](#)

# JavaScript在2017年 - 超越浏览器

随着技术世界的发展，JavaScript也随之发展。在开始的时候，JavaScript并不是软件世界发展的重点，不像服务器端应用程序，移动应用程序和机器人。而今天，JavaScript的发展已经将语言带到了聊天机器人，虚拟现实，物联网等方面。

除了到达新的前沿之外，JavaScript的作用已经变得更加稳定，在长期以来一直在服务器端Node.js应用程序以及移动和桌面应用程序框架的一部分生态系统中。在这篇文章中，我们将回顾一年前我们对JavaScript所做的一些预测，也对2017年的JavaScript浏览器之外的JavaScript进行了一些预测。我们先来看看JavaScript在服务器端应用程序开发是如何做的。

## Node.js

Node.js是一个开源运行时库，用于构建服务器端应用程序的，可以在浏览器环境之外运行JavaScript代码。在过去几年中，Node已经从初创公司流行的技术转变为各种规模公司使用的主流开发方式。

Node的包管理器npm已经从服务器端应用程序的托管实用程序模块转换到规范的地方来，用来存储可分发的JavaScript代码。也许节点上升的最佳指标是存储在npm上的绝对数量。在去年的预测中，我们列出了以下图表，显示了npm在替代语言中的优势。

![](http://ofyfg9y7t.bkt.clouddn.com/module-counts-2016.jpg) 


截至2015年12月，modulecounts.com的模块数量

快速前进一年，npm的增长没有放缓的迹象。事实上，npm从大约20万到35万包的移动迫使“模块计数”站点重新配置其图表的y轴。

![](http://ofyfg9y7t.bkt.clouddn.com/module-counts-2017.jpg)

截至2016年12月，modulecounts.com的模块数量

有一些因素导致了这一增长，其中一个重要的因素就是越来越多的企业公司在其基础设施中使用Node。在去年的讨论中，我们进行了以下预测。

> “2016年可预见更多的公司将会进一步采用Node及其包管理工具npm。继续采用Node的大型公司有微软，IBM，Intel，Progress等， 以及企业级的功能，如长期支持计划，可能意味着企业中节点采用的增长，替代了像.NET和Java这样的典型企业解决方案。“

这确实是Node成长的风险或独特的预测。Node的案例研究表明有一小部分规模中等的公司现在已经采用Node，包括Netflix，GoDaddy和Capital One等。

但也许NASA在关键基础设施中使用Node是最明显的。你可以阅读NASA对Node研究，但是我将在这里摘录一段话。

> “当你考虑到航天员的安全的时候，小小的打嗝和服务中断就变成了生死攸关的情况，从EVA（舱外活动）的数据直到太空的宇航员，Node.js有助于确保所有人和事物的安全。“

但是，不仅仅只有NASA促进了Node的成长。Node的包管理器npm已成为在跨环境中存储JavaScript代码的不二选择，包管理器上的整合也有助于推动Node自身的发展。

在本文中，我们讨论的每个框架和技术都使用npm来存储和分发他的源代码。
用npm搜索“jquery”，“polymer”，“react”，“cordova”或“nativescript”可以让您了解npm现在的规模。随着JavaScript的普及，npm越来越受欢迎。随着npm的普及，Node.js也越来越多。我们相信这个趋势不会很快结束。

<center>![](http://ofyfg9y7t.bkt.clouddn.com/angular-npm-downloads.jpg)</center>

在npmjs.com搜索“angular”返回近10,000个结果。Angular是通过npm分发的许多库之一。

在2017年，我们相信更多的公司将从更传统的开发方法（如Java和C＃）切换到Node。我们相信，TypeScript（一个Microsoft写的JavaScript超集）将有助于推动Node的成长，因为它使JavaScript对Java和C＃开发人员更加平易近人。Node对长期支持版本的承诺也将有助于这一增长，因为它使这些公司保证其使用的版本将在未来几年得到支持。

总的来说，大型企业不喜欢维护多种开发系统和语言，而Node允许这些公司以一种语言来整合其所有的开发。而且整合不仅仅适用于服务器端代码。让我们远瞻一下JavaScript如何影响移动世界。

## PhoneGap和Cordova

PhoneGap和基于开源框架PhoneGap的Cordova，是JavaScript的第一次进入原生开发的领域。Cordova的基本方法是将Web代码封装在WebView中，并使用WebView驱动本地移动应用程序。这种方法允许Web开发人员使用他们已经拥有的技能（即JavaScript）构建移动应用程序，因为Cordova多年来一直是构建移动应用程序的引人注目的选择。

但是这开始改变了。今天，Cordova正在面临替代发展方式的挑战，其中大部分利用与Cordova相近的基于JavaScript的技术。Cordova最大的挑战者也许是由Google领导的Progressive Web Apps或PWAs的理念。

![](http://ofyfg9y7t.bkt.clouddn.com/progressive-web-apps.jpg)

Google的Progressive Web Apps主页

PWAs为网络世界带来了许多类似原生的功能，例如推送通知，离线访问和主屏幕图标。去年，我们预测Google将开始慢慢推行PWA方法。事实证明，这一预测是并不是正确的，因为Google已经明确表示，他们通过多次活动致力于PWA方法。最近的Chrome开发者峰会与今年的Google I / O会议一样，对PWA进行了大量的讨论。


PWAs与我们的讨论相关，因为它们开始蚕食Cordova应用--需要一些原生功能的Web应用程序。如果您有一个需要离线访问或推送通知的网络应用程序，那么用PWA来构建基于Cordova的本机应用程序是一个非常好的方案。尽管很难衡量有多少人在混合应用程序中选择了PWA，但大多数数据表明Cordova的使用已经缩减。例如，在过去两年里，Cordova每周下载的次数。正如你所看到的，尽管Cordova的数字仍然很健康，但趋势线不再像往年那样向上。

![](http://ofyfg9y7t.bkt.clouddn.com/cordova-downloads-per-week.jpg)

从2014年12月至2016年12月，每周下载“cordova”的npm软件包。（数据来自npm-stat.com）

但是这个衰退还有另外一个因素。尽管我们认为PWA的使用正在蚕食Cordova的份额，但我们也相信在移动领域相对较新的入口也蚕食了部分Cordova的份额。

## 原生移动应用

由Appcelerator倡导，Facebook和Progress开始推广使用JavaScript开发原生应用的框架，分别命名为React Native和NativeScript。JavaScript驱动的本机应用程序不使用WebView，因此，它们不会遇到与基于Cordova的应用程序相同的基于Web的性能问题。

在去年的讨论中，我们预计2016年将是这些框架成熟的一年，并开始广泛使用，预测似乎是准确的。例如，您可以看到在过去两年里，React Native的每周下载次数持续增加。

![](http://ofyfg9y7t.bkt.clouddn.com/react-native-downloads.jpg)

从2014年12月到2016年12月，每周下载“react-native”的npm软件包。（数据来自npm-stat.com）

NativeScript也有同样的趋势曲线。

![](http://ofyfg9y7t.bkt.clouddn.com/nativescript-downloads.jpg)


从2014年12月至2016年12月，每周下载“nativescript”npm软件包。（数据来自npm-stat.com）

而不仅仅是下载这些JavaScript驱动的本机框架的数字。最近的JavaScript 2016调查显示，JavaScript开发人员对React Native和NativeScript都很感兴趣。

![](http://ofyfg9y7t.bkt.clouddn.com/state-of-js-mobile-results.jpg)

 State of JavaScript 2016调查的对移动开发方法的兴趣

对JavaScript调查状态的分析总结了这些结果。

> 在兴趣分数上，“Cordova”和“PhoneGap”的得分很低，这使得您可能会怀疑是否因为自己的性能问题导致的。虽然Cordova和PhoneGap依赖于底层的手机浏览器和它的JavaScript引擎做了很大的提升，但还是比运行本机代码（如React Native）慢。

在2017年，随着越来越多的JavaScript开发人员寻求构建移动应用程序，这些JavaScript驱动的本机框架的增长将加速。React Native可以从React框架的持续大量使用中获益，而NativeScript则宣布在5月份完成Angular 2支持，从越来越多的从Angular 1升级到Angular 2的开发人员中获益。我们也期待JavaScript驱动原生框架来吸引原生iOS和Android开发人员，因为JavaScript驱动的本机框架允许您从单个代码库（而不是两个代码库）构建真正的本机应用程序。

手机上的JavaScript越来越多地侵占了曾经以Objective-C和Java等语言为主的领域。但这不是JavaScript正在获得使用的唯一新领域。让我们将讨论转到桌面应用程序的主题。


## 桌面应用

传统上，如果您要构建Windows或Mac应用程序，您可以使用平台特定的工具，如WPF和Windows Forms，或使用Java或Adobe Air的跨平台接口。但是，像本文中讨论的所有其他软件生态系统一样，基于JavaScript的解决方案正在慢慢地进入这个领域。

在去年的讨论中，我们讨论了用于构建桌面应用程序的两个最流行的JavaScript框架--NW.js和GitHub的Electron，并且理论认为，它们的使用在2016年将会大幅增长。实际上，增长已经发生，但是对于Electron来说，这已经成为基于JavaScript的桌面应用程序开发的事实上的选择。

例如，如果您比较“electon”和“nw”JavaScript软件包的下载次数，您将看到“electon”（红线）现在以与“React Native”竞争的规模运行，而NW.js下载量相对平坦。

![](http://ofyfg9y7t.bkt.clouddn.com/electron-vs-nw-downloads.jpg)


从“electon”和“NW”NPM包，从2016年9月至十一月2016年数据的周下载量npm-stat.com。

2015年12月，Electron有20,000个GitHub stars，NW.js有25000个; 今天，Elecron拥有近40,000颗星，而NW.js刚刚超过30,000。

electon还开始在主流桌面应用程序中获得牵引力。该框架现在为Visual Studio代码提供支持，Visual Studio代码是微软的受欢迎的编辑器，该公司在四月份拥有超过五百万用户。electon还成功地普及了React和Angular社区，并且很容易在网络上使用两个框架找到Electron使用教程。

2017年，我们期望Electon的统治地位继续下去。我们期待看到进一步的Electon工具集成与网络最流行的框架 - 主要是React和Angular - 以及软件供应商的更多关注。而且随着JavaScript继续侵入传统上由Java和Microsoft技术主导的世界，我们预计Electon将继续被用作WPF，Java和Adobe Air等方法的替代品。

使用单一语言满足您的所有开发需求的吸引力是强大的，甚至采取JavaScript的一些最新和最新的开发方法。让我们结束我们的讨论，看看JavaScript在一些的新软件世界的表现。

## JavaScript的新边界

如果您向分析人员询问发展中国家的发展情况，那么他们将会突然说出虚拟现实，聊天室和物联网（IoT）等一系列流行语。

所有这些新技术中，JavaScript在chatbot生态系统中是最重要的，人们使用JavaScript来构建从简单的Slack机器人器到所有类似于商业交易的复杂机器人。chatbot世界中的大多数框架都包含了SDK中的Node库，包括Botkit，Microsoft的Bot Framework和Facebook的wit.ai。微软的Bot框架的文档甚至包括以下介绍，为什么要用Node来构建机器人。

> “Node Builder的Bot Builder是构建机器人的一个强大的框架，可以处理自由形式的交互和更多的引导，可以将这些可能性明确地显示给用户，它易于使用和模型化框架，如Express＆Restify，为开发人员提供一个熟悉的方式来控制机器人。

重用JavaScript同样为许多流行的IoT库（如Losant和zetta）以及Leap Motion等设备提供了Node API。以Chrome浏览器团队和A-Frame框架团队为代表，还有不少团队在虚拟现实中使用JavaScript。

![](http://ofyfg9y7t.bkt.clouddn.com/chrome-vr.jpg)

Google Chrome小组拥有一系列令人印象深刻的JavaScript构建的虚拟现实实验，您可以自己尝试。

这就是说，JavaScript在许多领域仍然是一个有更多的性能优势的工具，如C ++，Python和C＃扮演了主导角色。例如，流行的Oculus Rift设备主要使用C ++，而Microsoft的HoloLens则需要您在C＃中编写。

我们预计这一趋势将在2017年开始发生变化。随着JavaScript的普及和速度不断提高，语言将继续发展到像VR和物联网这样的环境。随着新的软件开发生态系统的涌现，我们期待JavaScript越来越被列为一流的公民。

## JavaScript的所有东西

十年前在服务器上使用JavaScript是不可想象的; 今天，Node拥有350万用户，年增长率达100％。五年前，使用JavaScript来驱动本地iOS或Android应用程序是一个基础; 今天的NativeScript和React Native正以惊人的速度增长。三年前使用JavaScript构建桌面应用程序很少见; 今天Electon每月下载超过100,00次。

JavaScript不会用于所有编程，因为许多其他语言更适合于解决某些问题和用例。然而，JavaScript的广泛使用确保始终是一个因素，而不管开发平台如何。也许是杰夫·阿特伍德（Jeff Atwood）关于这个话题的引人注目的引语，就是把这个讨论结束的最好方法，因为他的发言从来没有像是更有预言性的。

> “可以用JavaScript编写的应用程序最终将以JavaScript编写。”




