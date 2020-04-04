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

JavaScript was introduced in 1995 as a way to add programs to web
pages in the Netscape Navigator browser. The language has since been
adopted by all other major graphical web browsers. It has made modern
web applications possible—applications with which you can interact
directly without doing a page reload for every action. JavaScript is also
used in more traditional websites to provide various forms of
interactivity and cleverness.

{{index Java, naming}}

It is important to note that JavaScript has almost nothing to do with
the programming language named Java. The similar name was inspired by
marketing considerations rather than good judgment. When JavaScript
was being introduced, the Java language was being heavily marketed and
was gaining popularity. Someone thought it was a good idea to try to
ride along on this success. Now we are stuck with the name.

{{index ECMAScript, compatibility}}

After its adoption outside of Netscape, a ((standard)) document was
written to describe the way the JavaScript language should work so
that the various pieces of software that claimed to support JavaScript
were actually talking about the same language. This is called the
ECMAScript standard, after the Ecma International organization that
did the standardization. In practice, the terms ECMAScript and
JavaScript can be used interchangeably—they are two names for the same
language.

{{index [JavaScript, "weaknesses of"], debugging}}

There are those who will say _terrible_ things about JavaScript. Many
of these things are true. When I was required to write something in
JavaScript for the first time, I quickly came to despise it. It would
accept almost anything I typed but interpret it in a way that was
completely different from what I meant. This had a lot to do with the
fact that I did not have a clue what I was doing, of course, but there
is a real issue here: JavaScript is ridiculously liberal in what it
allows. The idea behind this design was that it would make programming
in JavaScript easier for beginners. In actuality, it mostly makes
finding problems in your programs harder because the system will not
point them out to you.

{{index [JavaScript, "flexibility of"], flexibility}}

This flexibility also has its advantages, though. It leaves space for
a lot of techniques that are impossible in more rigid languages, and
as you will see (for example in [Chapter ?](modules)), it can be used
to overcome some of JavaScript's shortcomings. After ((learning)) the
language properly and working with it for a while, I have learned to
actually _like_ JavaScript.

{{index future, [JavaScript, "versions of"], ECMAScript, "ECMAScript 6"}}

There have been several versions of JavaScript. ECMAScript version 3
was the widely supported version in the time of JavaScript's ascent to
dominance, roughly between 2000 and 2010. During this time, work was
underway on an ambitious version 4, which planned a number of radical
improvements and extensions to the language. Changing a living, widely
used language in such a radical way turned out to be politically
difficult, and work on the version 4 was abandoned in 2008, leading to
a much less ambitious version 5, which made only some uncontroversial
improvements, coming out in 2009. Then in 2015 version 6 came out, a
major update that included some of the ideas planned for version 4.
Since then we've had new, small updates every year.

The fact that the language is evolving means that browsers have to
constantly keep up, and if you're using an older browser, it may not
support every feature. The language designers are careful to not make
any changes that could break existing programs, so new browsers can
still run old programs. In this book, I'm using the 2017 version of
JavaScript.

{{index [JavaScript, "uses of"]}}

Web browsers are not the only platforms on which JavaScript is used.
Some databases, such as MongoDB and CouchDB, use JavaScript as their
scripting and query language. Several platforms for desktop and server
programming, most notably the ((Node.js)) project (the subject of
[Chapter ?](node)), provide an environment for programming JavaScript
outside of the browser.

## Code, and what to do with it

{{index "reading code", "writing code"}}

_Code_ is the text that makes up programs. Most chapters in this book
contain quite a lot of code. I believe reading code and writing ((code))
are indispensable parts of ((learning)) to program. Try to not just
glance over the examples—read them attentively and understand them.
This may be slow and confusing at first, but I promise that you'll
quickly get the hang of it. The same goes for the ((exercises)). Don't
assume you understand them until you've actually written a working
solution.

{{index interpretation}}

I recommend you try your solutions to exercises in an actual
JavaScript interpreter. That way, you'll get immediate feedback on
whether what you are doing is working, and, I hope, you'll be tempted
to ((experiment)) and go beyond the exercises.

{{if interactive

When reading this book in your browser, you can edit (and run) all
example programs by clicking them.

if}}

{{if book

{{index download, sandbox, "running code"}}

The easiest way to run the example code in the book, and to experiment
with it, is to look it up in the online version of the book at
[_https://eloquentjavascript.net_](https://eloquentjavascript.net/). There,
you can click any code example to edit and run it and to see the
output it produces. To work on the exercises, go to
[_https://eloquentjavascript.net/code_](https://eloquentjavascript.net/code),
which provides starting code for each coding exercise and allows you
to look at the solutions.

if}}

{{index "developer tools", "JavaScript console"}}

If you want to run the programs defined in this book outside of the
book's website, some care will be required. Many examples stand on their
own and should work in any JavaScript environment. But code in later
chapters is often written for a specific environment (the browser or
Node.js) and can run only there. In addition, many chapters define
bigger programs, and the pieces of code that appear in them depend on
each other or on external files. The
[sandbox](https://eloquentjavascript.net/code) on the website provides
links to Zip files containing all the scripts and data files
necessary to run the code for a given chapter.

## Overview of this book

本书大约分为三部分。前12章探讨 JavaScript。之后的七章是关于网络((浏览器))和如何用 JavaScript 去编写它们。最后两章用来讲 ((Node.js))，另一个可以编写 JavaScript 的环境。

Throughout the book, there are five _project chapters_, which describe
larger example programs to give you a taste of actual programming. In
order of appearance, we will work through building a [delivery
robot](robot), a [programming language](language), a [platform
game](game), a [pixel paint program](paint), and a [dynamic
website](skillsharing).

The language part of the book starts with four chapters that introduce
the basic structure of the JavaScript language. They introduce
[control structures](program_structure) (such as the `while` word you
saw in this introduction), [functions](functions) (writing your own
building blocks), and [data structures](data). After these, you will
be able to write basic programs. Next, Chapters [?](higher_order) and
[?](object) introduce techniques to use functions and objects to write
more _abstract_ code and keep complexity under control.

After a [first project chapter](robot), the language part of the book
continues with chapters on [error handling and bug fixing](error),
[regular expressions](regexp) (an important tool for working with
text), [modularity](modules) (another defense against complexity), and
[asynchronous programming](async) (dealing with events that take
time). The [second project chapter](language) concludes the first part
of the book.

The second part, Chapters [?](browser) to [?](paint), describes the
tools that browser JavaScript has access to. You'll learn to display
things on the screen (Chapters [?](dom) and [?](canvas)), respond to
user input ([Chapter ?](event)), and communicate over the network
([Chapter ?](http)). There are again two project chapters in this
part.

After that, [Chapter ?](node) describes Node.js, and [Chapter
?](skillsharing) builds a small website using that tool.

{{if commercial

Finally, [Chapter ?](fast) describes some of the considerations that
come up when optimizing JavaScript programs for speed.

if}}

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
