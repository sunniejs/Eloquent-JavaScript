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

Each line of the previous program contains a single instruction. It
could be written in English like this:

 1. Store the number 0 in memory location 0.
 2. Store the number 1 in memory location 1.
 3. Store the value of memory location 1 in memory location 2.
 4. Subtract the number 11 from the value in memory location 2.
 5. If the value in memory location 2 is the number 0,
    continue with instruction 9.
 6. Add the value of memory location 1 to memory location 0.
 7. Add the number 1 to the value of memory location 1.
 8. Continue with instruction 3.
 9. Output the value of memory location 0.

{{index readability, naming, binding}}

Although that is already more readable than the soup of bits, it is
still rather obscure. Using names instead of numbers for the
instructions and memory locations helps.

```{lang: "text/plain"}
 Set “total” to 0.
 Set “count” to 1.
[loop]
 Set “compare” to “count”.
 Subtract 11 from “compare”.
 If “compare” is zero, continue at [end].
 Add “count” to “total”.
 Add 1 to “count”.
 Continue at [loop].
[end]
 Output “total”.
```

{{index loop, jump, "summing example"}}

Can you see how the program works at this point? The first two lines
give two memory locations their starting values: `total` will be used
to build up the result of the computation, and `count` will keep track
of the number that we are currently looking at. The lines using
`compare` are probably the weirdest ones. The program wants to see
whether `count` is equal to 11 to decide whether it can stop
running. Because our hypothetical machine is rather primitive, it can
only test whether a number is zero and make a decision based
on that. So it uses the memory location labeled `compare` to compute
the value of `count - 11` and makes a decision based on that value.
The next two lines add the value of `count` to the result and
increment `count` by 1 every time the program has decided that `count`
is not 11 yet.

Here is the same program in JavaScript:

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

This version gives us a few more improvements. Most important, there
is no need to specify the way we want the program to jump back and
forth anymore. The `while` construct takes care of that. It continues
executing the block (wrapped in braces) below it as long as the
condition it was given holds. That condition is `count <= 10`, which
means “_count_ is less than or equal to 10”. We no longer have to
create a temporary value and compare that to zero, which was just an
uninteresting detail. Part of the power of programming languages is
that they can take care of uninteresting details for us.

{{index "console.log"}}

At the end of the program, after the `while` construct has finished,
the `console.log` operation is used to write out the result.

{{index "sum function", "range function", abstraction, function}}

Finally, here is what the program could look like if we happened to
have the convenient operations `range` and `sum` available, which
respectively create a ((collection)) of numbers within a range and
compute the sum of a collection of numbers:

```{startCode: true}
console.log(sum(range(1, 10)));
// → 55
```

{{index readability}}

The moral of this story is that the same program can be expressed in
both long and short, unreadable and readable ways. The first version of the
program was extremely obscure, whereas this last one is almost
English: `log` the `sum` of the `range` of numbers from 1 to 10. (We
will see in [later chapters](data) how to define operations like `sum`
and `range`.)

{{index ["programming language", "power of"], composability}}

A good programming language helps the programmer by allowing them to
talk about the actions that the computer has to perform on a higher
level. It helps omit details, provides convenient building blocks
(such as `while` and `console.log`), allows you to define your own
building blocks (such as `sum` and `range`), and makes those blocks
easy to compose.

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

This book contains roughly three parts. The first 12 chapters discuss
the JavaScript language. The next seven chapters are about web
((browsers)) and the way JavaScript is used to program them. Finally,
two chapters are devoted to ((Node.js)), another environment to
program JavaScript in.

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

In this book, text written in a `monospaced` font will represent
elements of programs—sometimes they are self-sufficient fragments, and
sometimes they just refer to part of a nearby program. Programs (of
which you have already seen a few) are written as follows:

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

Sometimes, to show the output that a program produces, the
expected output is written after it, with two slashes and an arrow in
front.

```
console.log(factorial(8));
// → 40320
```

Good luck!
