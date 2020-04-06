{{meta {load_files: ["code/intro.js"]}}}

# Introduction

{{quote {author: "Ellen Ullman", title: "Close to the Machine: Technophilia and its Discontents", chapter: true}

我们以为在为我们的目的创作系统。我们以为它溶入了我们形象中……但是电脑终究不是我们。它只是我们缩影中微小的一部分：专门负责逻辑、制度、规矩、和清晰度。

quote}}

{{figure {url: "img/chapter_picture_00.jpg", alt: "Picture of a screwdriver and a circuit board", chapter: "framed"}}}

这是本关于命令((电脑))的书。尽管电脑现如今随处可见，但是它们并没有看上去那么简单。尤其是让它们按照你的意思工作的时候。

如果你只是想让电脑做些稀疏平常的事情，比如查邮件、用计算器，那你只需要打开相对应的((程序))。但不是所有的问题都有相应的解决方式。

编程这时就有用武之地了。_编程_ 是创建程序的行为 —— 一组精准的指令告诉电脑要做什么。因为电脑是无法思考的冰冷机器，所以编程从根本上来讲，是枯燥乏味的。

{{index [programming, "joy of"], speed}}

幸运的是，如果你克服了这个难题，或者享受严谨的思考，从而命令这些笨拙的机器，那么你会很有成就感。编程可以让你几秒钟内完成看似 _不可能完成_ 的任务。它可以让你的电脑做那些曾经做不到的事。同时它也是不错的脑力抽象思维训练。

大部分的编程都需要 ((编程语言))。_编程语言_ 是人造语言，专门用来命令电脑的。我们和电脑最有效率的沟通方式其实大范围的借鉴了人类本身的沟通方式。和人类语言一样，电脑语言也是由不同的词语、短句结合起来，从而表达新的概念。

{{index [JavaScript, "availability of"], "casual computing"}}

曾经命令模式的界面，比如 80、90年代的 BASIC 和 DOS，是和电脑沟通的主流方式。如今它们几乎被更简易但不够灵活的视觉界面替代。不过编程语言依旧存在，如果你知道去哪里找寻。比如 JavaScript 就被内置到了每一个现代网络((浏览器))里，因此几乎每个电子设备都兼容。

{{indexsee "web browser", browser}}

本书尽可能让你足够了解 JavaScript，从而用它编写有趣的应用。

## On programming

{{index [programming, "difficulty of"]}}

除了讲解 JavaScript 以外，本书还会包括基本的编程理论。编程其实很难。尽管基本规则简洁明了，但是这些简单的基本概念往往叠加、组合，从而形成许多新的且更加复杂的理论。好比你在搭建一个迷宫，一不小心就会迷失。

{{index learning}}

有些时候阅读本书会让你变得沮丧。如果你从未接触过编程，那么你需要吸收理解大量新的内容。这些内容会被 _连接_ 在一起成为新的知识点。

一分耕耘，一分收获。如果你学的吃力，不要怀疑自己，轻言放弃。坚持就是胜利。休息片刻，重新来过。一定要确保你明白了相关例题和((习题))。虽说学海无涯，不过你可以逐渐利用已掌握的知识举一反三。

{{quote {author: "Ursula K. Le Guin", title: "The Left Hand of Darkness"}

{{index "Le Guin, Ursula K."}}

当行动变得无利可图是，去采集信息；当信息变得无利可图时，去休息。

quote}}

{{index [program, "nature of"], data}}

一个程序包括了许多东西。它是一篇由程序员谱写的乐章，它直接对电脑下达运行指令，它是电脑内存中的数据，与此同时它亦控制着同一内存的运行。我们熟悉的事物中找不到可以用来比喻程序的东西。硬要比拟的话，可以想象一个机器，涉及许多分散的零件，但要让全部零件运转，我们需要考虑这些零件间的关系及对整体系统的作用。

((电脑))是一个容纳这些非物质零件的实体机器。电脑本身只能做非常笨拙的事情。它们之所以有用是因为它们的运行((速度))惊人。一个程序可以十分巧妙的结合数以万计的简单运算，从而完成相当复杂的事情。

{{index [programming, "joy of"]}}

程序是一次思想建设。它本身无成本、无体积、而且在我们打字的过程中，轻松增长。

但一不小心，程序的大小和((复杂度))就会变得难以附加，甚至连原作者都难以理解。编程的主要问题在于如何掌控程序。一个正常运行的程序是赏心悦目的。编程的造诣是对其复杂度的掌控自如。一个出色的程序的复杂度应该化繁为简。

{{index "programming style", "best practices"}}

有些程序员认为仅用一小部分众所周知的技术是掌控复杂度的最好方法。他们对程序制定了严格的规则（“最佳做法”）后，小心翼翼的踏步在这个狭小的安全地带。

{{index experiment}}

然而这不仅无聊，且无效。随着时代的日新月异，程序也在与时俱进。编程领域正处于突飞猛进的发展阶段，这里有无限的空间去探索、去尝试。程序设计中存有许多致命的错误，而你应该通过这些错误，慢慢摸索出其背后的原理。好的程序是在反复实践中总结出来的，而不是按部就班那一成不变的规定。

## Why language matters

{{index "programming language", "machine code", "binary data"}}

起初，在电脑最初的阶段，并没有任何编程语言。当时的程序长这个样子:

```{lang: null}
00110001 00000000 00000000
00110001 00000001 00000001
00110011 00000001 00000010
01010001 00001011 00000010
00100010 00000010 00001000
01000011 00000001 00000000
01000001 00000001 00000001
00010000 00000010 00000000
01100010 00000000 00000000
```

{{index [programming, "history of"], "punch card", complexity}}

上面这个程序是把 1 到 10 的整数相加后输出结果：`1 + 2 + ... + 10 = 55`. 它可以在一个简单的、虚构的机器上运行。对于早期的电脑而言，编程者需要确保大量开关的位置正确，或者在纸盒版上打孔后将其馈送到计算机中。你可以想象这个过程是多么的无聊乏味且易出错。就连编写最简单的程序都需要大量的才智和耐心。编写复杂的程序简直匪夷所思。

{{index bit, "wizard (mighty)"}}

当然，手动输入这些神秘的位模式（1 & 0），好比一个魔法师，的确让程序员印象深刻。这也是值得吹嘘的。

{{index memory, instruction}}

上一个程序中的每一行都是一个指令。翻译成中文则是：

 1. 把数字0存入内存地址0。
 2. 把数字1存入内存地址1。
 3. 把内存地址1的值存入内存地址2。
 4. 用内存地址2的值减去11。
 5. 如果内存地址2的值是数字0的话，去第九个指令。
 6. 把内存地址1的值加入内存地址0。
 7. 内存地址1的值加1。
 8. 回第三个指令。
 9. 返回内存地址0的值。

{{index readability, naming, binding}}

虽然改成中文后的指令相对易懂了些，但它还是让人一头雾水。用名字来代替数字的步骤和内存地址会更好理解。

```{lang: "text/plain"}
将“总数”设置为0。
将“计数”设置为1。
[循环]
将“对比”设置为“计数”
“对比”减去11
如果“对比”是0，跳至[结束]。
 “总数”加“计数”
 “计数”加1。
 跳至[循环]。
[结束]
返回“总数”
```

{{index loop, jump, "summing example"}}

你现在看明白上面的程序是如何运行的了么？头两行分别给两个内存地址它们的起始值：“总数”是用来储存计算结果的，“计数”是用来记录当前的数字的。“对比”那行最为古怪。该程序想知道“计数”是否等于11，从而决定它是否要停止运行。因为我们虚构的机器过于简单，以至于它只能根据一个数字是不是零而做选择。所以它利用“对比”这个内存地址来计算 `计数 - 11` 的值来决定下一步。之后的两行先把“计数”加入“总数”值中，之后在把“计数”加1，直到“计数”是11为止。

写成 JavaSript 就是：

```
let total = 0, count = 1;
while (count <= 10) {
  total += count;
  count += 1;
}
console.log(total);
// → 55
```

{{index "while loop", loop, [braces, block]}}

这个版本有几大好处。最重要的一点，我们不再需要告诉该程序如何跳来跳去。`while`帮我们完成了。它会一直运行代码块（大括号围绕的代码）中的指令直到前提失效为止。这个前提`count <= 10`就是“_计数_ 小于或等于10”。我们也不再需要创建一个临时的值去和零相比，毕竟这是个无聊的知识点。而编程语言的一大优势就是它可以自动帮我们解决这些无聊的知识点。

{{index "console.log"}}

程序的结尾，在`while`结束以后，`console.log`操作用来返回结果。

{{index "sum function", "range function", abstraction, function}}

最后，如果我们有`range`和`sum`这两个便捷的操作。`range`会创建一个固定范围内的数字((序列))，`sum`则计算一个((序列))的合。这个程序可以简化成：

```{startCode: true}
console.log(sum(range(1, 10)));
// → 55
```

{{index readability}}

这个故事告诉我们，同一个程序有多种表达方式。最开始的程序犹如天书，而最后一个程序就好比一句话：记录（`log`)从1到10的序列(`range`)总和(`sum`)。（[后面](data)我们会讲到如何创建类似`sum`和`range`的操作。）

{{index ["programming language", "power of"], composability}}

一个好的编程语言应该把复杂的电脑操作简单化。它会删除细节，提供一系列方便的积木（比如`while`、`console.log`），允许你自创其他积木（比如`sum`、`range`），而且还会轻松拼搭建起这些积木。

## What is JavaScript?

{{index history, Netscape, browser, "web application", JavaScript, [JavaScript, "history of"], "World Wide Web"}}

{{indexsee WWW, "World Wide Web"}}

{{indexsee Web, "World Wide Web"}}

Netscape Navigator 浏览器于1995年推出了 JavaScript，以便在页面上添加程序。此后所有主要图形网络浏览器都支持该语言。它创建了现代网络程序——用户可以直接和应用互动，而不再需要为每一个操作重新加载页面。更传统的网站也会用 JavaScript 来提供各种形式的互动。

{{index Java, naming}}

一定要记住 JavaScript 和另一个叫 Java 的编程语言没有半点关系。名字的相似度只是为了更好的市场行销。JavaScript 刚面世时，Java 语言正因为大量市场销售而备受欢迎。有些人觉得搭个顺风车，可以事半功倍。这名字就这么保留了下来。

{{index ECMAScript, compatibility}}

自从被外界采用后，就有一个((标准))文档用来描述 JavaScript 的运行方式，以便于所有声称支持 JavaScript 的应用可以达成统一。由 Ecma 国际组织标准化的这个文档叫做 ECMAScript standard。实际上，ECMAScript 和 JavaScript 可互换使用——它们都指同一个语言。

{{index [JavaScript, "weaknesses of"], debugging}}

有些人会例举许多 JavaScript 中_糟糕_的点，而且大部分是事实。在我第一次被要求写 JavaScript 时，我立即鄙视了这个语言。它几乎接受了我打出的所有代码，只是编译结果和我想象的大相径庭。当然，这绝大程度上是因为我当时并不知道自己在做什么。可是，隐藏的真正的问题是：JavaScript 的接受范围广义的可怕。JavaScript 的这种设计初衷是让新手更容易编程。但事实上，它复杂化了代码调试，因为系统不会告诉你问题出在哪里。

{{index [JavaScript, "flexibility of"], flexibility}}

当然，这种灵活性也有它的优势。相比其他更为严谨的语言，它提供了更多的技术空间。后面你会看到(比如[第十章](modules))，它甚至可以用来克服部分自身的缺点。在系统的((学习))和运用 JavaScript 后，我发现我其实_喜欢_它。

{{index future, [JavaScript, "versions of"], ECMAScript, "ECMAScript 6"}}

现有许多版本的 JavaScript。EMCAScript 第3版在 JavaScript 统治时期，大约在2000-2010年，被广泛支持。这期间，被寄予厚望的第4版正在开发中，其中包括了大量的改善和扩展。事实证明，如此大规模的完善一个被广泛运用的语言是相当困难的。在2008年，第4版宣告流产后，仅有少许无争议改善的第5版于2009年问世了。第6版发表于2015年，是一个包括了部分第4版的重要更新。此后的每一年都会有一些小更新。

语言的不断更新意味着浏览器必须与时俱进。如果你用一个老旧的浏览器，很多新的特性可能都不被支持。语言构架师们异常小心谨慎，避免做任何可能破坏现有程序的更改，所以新的浏览器还支持老旧的程序。本书用的是2017版的 JavaScript。

{{index [JavaScript, "uses of"]}}

网页浏览器不是 JavaScript 的唯一平台。有些数据库，比如 MongoDB 和 CouchDB，用 JavaScript 作为它们的脚本和查询语言。部分桌面和服务器编程平台，最普遍的是((Node.js))（[第二十章](node)内容），提供浏览器外可编译 JavaScript 的环境。

## Code, and what to do with it

{{index "reading code", "writing code"}}

_代码_是组成程序的文本。本书中大部分的章节都有大量代码。我坚信读写((代码))与((学习))编程密不可分。对于例题，请不要一目十行——认真阅读且理解它们。起初也许难以理解，但要相信功夫不负有心人。对于((练习题))也是一样。在你真正写出解题前，不要以为你已经学会了。

{{index interpretation}}

我建议你在一个真正的 JavaScript 编译器中做练习题。这样，你可以立刻检验答案的准确性。但愿你会试图((尝试))习题以外的挑战。

{{if interactive

在使用浏览器阅读本书时，你可以点击修改（和运行）所有例题。

if}}

{{if book

{{index download, sandbox, "running code"}}

通过网页[_https://eloquentjavascript.net_](https://eloquentjavascript.net/)是运行和实践本书中代码的最简单方法。你可以点击修改（和运行）任何例题并看到它输出的结果。[_https://eloquentjavascript.net/code_](https://eloquentjavascript.net/code)不仅给每到练习题都提供了起始码，还有参考答案。

if}}

{{index "developer tools", "JavaScript console"}}

如果你想在其他网页运行本书中的代码，你需要一些额外的步骤。大部分代码是独立的，所以可以在所有 JavaScript 环境下运行。但后几章的代码需要具体的运行环境（浏览器或者 Node.js），且只能在那些环境下运行。此外，因为很多章节的程序较大，所以部分代码相互依赖或者需要外来文件。网页的[沙盒模式](https://eloquentjavascript.net/code)提供的 zip 文件包含了所需的数据和脚本文件。

## Overview of this book

本书大约分为三部分。前12章探讨 JavaScript。之后的七章是关于网络((浏览器))和如何用 JavaScript 去编写它们。最后两章用来讲 ((Node.js))，另一个可以编写 JavaScript 的环境。

全书共有五个_项目章节_，提供更大的示例程序，从而让你体验更真实的编程。按出场顺序，我们会一起搭建一个[送货机器人](robot)，一个[编程语言](language)，一个[平台游戏](game)，一个[像素画程序]](paint)，和一个[动态网页](skillsharing).

本书的前四章介绍了 JavaScript 的基本结构。包括了[控制结构](program_structure) (比如前面看到的`while`)，[函数](functions) (编写自己的积木)，和[数据结构](data)。结束后，你将会编写简单的程序。[第五章](higher_order)和[第六章](object)讲述了如何用函数和对象编写更抽象的代码和更复杂的指令。

[第一个项目章节](robot)结束后，我们将继续探索 JavaScript 的[错误处理和修复](error)，[正则表达式](regexp) (处理文本的重要工具), [模块化](modules) (防止复杂性的另一道防线)，和[异步编程](async) (处理需要时间的事件)。[第二个项目章节](language)为本书的第一部分画上句号。

第二部分，从[第十三章](browser)至[第十九章](paint)，描述了JavaScript 可以在浏览器中访问的工具。你会学会如何在显示器上展现东西([第十四章](dom)和[第十七章](canvas))，响应用户输入 ([第十五章](event))，以及通过网络交流([第十八章](http))。本部分也有两个项目章节。

之后，[第二十章](node)讲述了 Node.js，和[第二十一章](skillsharing)会通过 Node.js 创建一个小网站。

## Typographic conventions

{{index "factorial function"}}

本书中，`等距`的字体代表程序的一部分。它们也许是一个独立的程序片段，或者是取自附近程序的一个碎片。程序（你已经见过许多）会用下面的写法表达：

```
function factorial(n) {
  if (n == 0) {
    return 1;
  } else {
    return factorial(n - 1) * n;
  }
}
```

{{index "console.log"}}

有时，为了表示一个程序的结果，该结果会写在下面。前面会有两条斜线和一个箭头。

```
console.log(factorial(8));
// → 40320
```

加油！
