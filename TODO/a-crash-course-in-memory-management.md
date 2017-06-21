 * 原文地址：[A crash course in memory management](https://hacks.mozilla.org/2017/06/a-crash-course-in-memory-management/)
 * 原文作者：[Lin Clark](https://code-cartoons.com/)
 * 译者：[黑黑](#)

 # 翻译 | 内存管理速成课（1）

要理解为什么将ArrayBuffer和SharedArrayBuffer添加到JavaScript中，您需要了解一些关于内存管理的内容。

您可以将机器中的内存看作一堆盒子。就像你在办公室里的邮箱，或是要求小孩子们使用的收纳箱。

如果你想要为其他孩子留下一些东西，你可以把它放在一个盒子里。

![1](./img/a-crash-course-in-memory-management/01_01-768x542.png)

在每个盒子旁边都有一个数字，这些数字就是内存地址，用来告诉别人在哪里找到你留给他们的东西。

这些盒子中的每一个都具有相同的尺寸，并且可以容纳一定量的信息。盒子的尺寸取决于机器。这个大小称为字长。它通常是32位或64位。但是为了显示方便，这里我们使用8位字长。

![2](./img/a-crash-course-in-memory-management/01_02-768x185.png)

如果我们想把数字2放在其中一个盒子中，我们可以很容易地做到这一点。数字很[容易转换成二进制](https://www.khanacademy.org/math/algebra-home/alg-intro-to-algebra/algebra-alternate-number-bases/v/decimal-to-binary)。

![3](./img/a-crash-course-in-memory-management/01_03-768x351.png)

如果我们想要的东西不是数字怎么办？比如字母H？

我们需要一个类似[UTF-8](https://en.wikipedia.org/wiki/UTF-8)的编码来用数字代替这些东西。而为了把这些东西转换成数字，我们需要一个类似编码器环的工具。之后我们就可以存储它了。

![4](./img/a-crash-course-in-memory-management/01_04-768x425.png)

当我们想把它从盒子里拿出来的时候，必须通过解码器把它转换回 H。

### 自动内存管理
当你在使用JavaScript时，实际上并不需要考虑内存。它被抽象出来，你不会直接接触到内存。

JS引擎充当中介。它为您管理内存。

![5](./img/a-crash-course-in-memory-management/01_05-768x570.png)

让我们假设一些JS代码，创建一个变量。

![6](./img/a-crash-course-in-memory-management/01_06-768x572.png)

js引擎利用编码器把该值转换成二进制。

![7](./img/a-crash-course-in-memory-management/01_07-768x568.png)

它将在内存中找到可以容纳该二进制的空间。这个过程称为分配内存。

![8](./img/a-crash-course-in-memory-management/01_08-768x564.png)

然后，引擎将跟踪该变量是否仍然可以从程序中的任何地方访问。如果该变量无法再访问，则内存将被回收，以便JS引擎可以在其中添加新值。

![9](./img/a-crash-course-in-memory-management/01_09-768x582.png)

这种在内存中监控变量--字符串、对象或其他类型--并释放掉不再使用的变量所占用的内存的过程，成为垃圾回收。

像JavaScript这样不直接处理内存的语言被称为内存管理语言。

这种自动内存管理可以使开发人员更轻松。但它也增加了一些开销。而这种开销有时会使性能不可预测。

### 手动内存管理

手动管理内存的语言不同。例如，我们来看看React如何使用C写入内存（现在可以通过[WebAssembly](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)来[实现](https://www.youtube.com/watch?v=3GHJ4cbxsVQ)）。

C没有JavaScript在内存上的抽象层。而是直接在内存上运行。你可以从内存加载东西，也可以将内容存储到内存中。

![10](./img/a-crash-course-in-memory-management/01_10-768x552.png)

当您将C或其他语言编译到WebAssembly时，您使用的工具将在WebAssembly中添加一些辅助代码。例如，它会添加用于编码和解码字节的代码。这些代码称为运行环境。运行环境将辅助处理JS引擎为JS所做的那些事情。

![11](./img/a-crash-course-in-memory-management/01_11-768x555.png)

但是对于手动管理的语言，其运行时将不包括垃圾回收。

这并不意味着你完全要自己处理。即使在手动内存管理的语言中，通常会从语言运行时获得一些帮助。例如，在C中，运行时将跟踪哪些内存地址在 "空闲列表" 中打开。

![12](./img/a-crash-course-in-memory-management/01_12-768x552.png)

您可以使用函数`malloc`（内存分配的简写）来申请一些可以容纳数据的内存地址。这将把这些地址从空闲列表中拿走。当你处理完这些数据后，你须调用函数`free`释放掉由`malloc`函数申请的内存。之后，这些地址将被添加回空闲列表。

你必须弄清楚何时调用这些功能。这就是为什么它被称为手动内存管理——你自己管理内存。

作为一名开发人员，弄清楚何时清除不同部分的内存可能很难。如果您在错误的时间进行操作，可能会出现bug，甚至导致安全漏洞。如果你不这样做，你的内存就会耗尽。

这就是为什么许多现代语言使用自动内存管理的原因——以避免人为错误。但这是以性能为代价的。 我将在下一篇文章中更多地解释这一点。