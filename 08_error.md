{{meta {load_files: ["code/chapter/08_error.js"]}}}

# Bugs and Errors

{{quote {author: "Brian Kernighan and P.J. Plauger", title: "The Elements of Programming Style", chapter: true}

调试比写代码难两倍。所以，如果你的代码写的尽可能聪明，那么根据定义，你的聪明才智不够用来调试该代码。

quote}}

{{figure {url: "img/chapter_picture_8.jpg", alt: "Picture of a collection of bugs", chapter: framed}}}

{{index "Kernighan, Brian", "Plauger, P.J.", debugging, "error handling"}}

电脑程序中的错误通常被叫做 _((bug))s_。程序员们喜欢将他们想象成小型生物碰巧爬入我们的成品中。实际上，当然是我们自己把他们放到那的。

如果一个程序是清晰明确的思路。你大致可以把 bugs 想象成那些让思路模糊的，以及把思路转换成代码中出现的翻译错误。前者通常比后者更难发觉。

## Language

{{index parsing, analysis}}

很多错误会被电脑自动指出，如果它知道我们想做什么。可惜 JavaScript 的随意性是个阻碍。它对变量和属性的定义过于含糊不清，以至于它很难在运行前发现((错字))。即使如此，它允许你做一些明显无稽之谈的运算，比如 `true * "monkey"`。

{{index [syntax, error], [property, access]}}

有些事情 JavaScript 会抱怨。电脑会立刻抱怨，如果你写了一个不追随该语言((语法))的程序。还有比如调用一个不是函数的东西，或者查看((未定义))值的属性都会在程序试图执行该命令时产生错误报告。

{{index NaN, error}}

但通常，你的不切实际的运算只会返回 `NaN`（不是数字）或者一个未定义的值，而且程序会继续运行下去，以为它在做件有意义的事。这个错误只会在后期表现出来，在这个无意义的值运行过多个函数后。它甚至可能不会产生任何错误，而是让程序返回一个错误的输出。找寻这类问题的源泉会很困难。

找寻程序中的错误的过程叫做_((调试))_。

## Strict mode

{{index "strict mode", [syntax, error], function}}

{{indexsee "use strict", "strict mode"}}

JavaScript 可以通过_严谨模式_而变得_略微_严格。只要在文件或函数块的最上方放入字符串 `"use strict"` 即可。比如：

```{test: "error \"ReferenceError: counter is not defined\""}
function canYouSpotTheProblem() {
  "use strict";
  for (counter = 0; counter < 10; counter++) {
    console.log("Happy happy");
  }
}

canYouSpotTheProblem();
// → ReferenceError: counter is not defined
```

{{index "let keyword", [binding, global]}}

通常，如果你在绑定变量时，忘记了 `let`，如例题中的 `counter`，JavaScript 会默默创建并使用一个全局变量。在严谨模式中，它会报告一个((错误))。这非常有帮助。需要注意的是，如果该问题变量已经以全局变量模式存在的话，这一点就会失效。这时，该循环会默默改写全局变量的值。

{{index "this binding", "global object", undefined, "strict mode"}}

严谨模式中的另一个变化是函数中的 `this` 值为 `undefined` 除非它以((方法))模式被调用。在非严谨模式下，`this` 指全局范围对象，也就是一个属性为全局变量的对象。所以你如果在严谨模式下不小心调用了一个方法或构造器，JavaScript 会在它试图读取 `this` 值时立刻返回错误，而不是把它记录在全局范围内。

比如下面的代码中，没有关键字 `new` 的((构造器))被调用后，它的 `this` _不_会引用一个新构造的对象：

```
function Person(name) { this.name = name; }
let ferdinand = Person("Ferdinand"); // oops
console.log(name);
// → Ferdinand
```

{{index error}}

上面对 `Person` 的无厘头调用成功了，但是返回的是一个未定义的值，而且创建了一个全局变量 `name`。在严谨模式中，将会是不同的结果：

```{test: "error \"TypeError: Cannot set property 'name' of undefined\""}
"use strict";
function Person(name) { this.name = name; }
let ferdinand = Person("Ferdinand"); // forgot new
// → TypeError: Cannot set property 'name' of undefined
```

我们立刻被告知出事了。这很有帮助。

幸运的是，通过 `class` 创建的构造器在没有 `new` 的调用下，一定会报告错误。这也减少了非严谨模式下的问题。

{{index parameter, [binding, naming], "with statement"}}

严谨模式还做一些事。它不允许给一个函数多个相同名字的参数，并完全移除了一些有问题的语言特征（比如 `with` 语句，因为它太过荒谬，都不在本书的探讨范围内）。

{{index debugging}}

总而言之，在程序的最上方添加 `"use strict"` 会帮你发现一些问题，且少有害处。

## Types

有些语言想在运行前知道所有变量及表达式的类型。他们会立刻告诉你如果一个类型的用法不一致。JavaScript 只有在运行时才考虑类型，而且还会试图默默转换值的类型，所以它提供不了任何帮助。

尽管如此，类型为讨论程序提供了一个有用的框架。很多错误的产生都因为困惑于一个程序的输入或输出值的类型。如果你将这个信息记录下来了，那么你就不太容易被困惑。

你可以添加一些注释。比如下面例子中对上一章中对函数 `goalOrientedRobot` 的批注：

```
// (VillageState, Array) → {direction: string, memory: Array}
function goalOrientedRobot(state, memory) {
  // ...
}
```

注释 JavaScript 程序类型的方法有很多。

关于类型有一点：它们需要介绍自己的复杂性，从而有效的描述足够的代码。你认为函数 `randomPick` 的类型应该是什么，从而返回一个数组中的随机元素？你需要介绍一个_((类型变量))_，_T_，可以代表任何类型。这样你就可以给 `randomPick` 一个类似 `([T]) → T` 的类型（从一个数组 *T* 的函数到一个 *T*）。

{{index "type checking", TypeScript}}

{{id typing}}

当已知一个程序的类型时，电脑就可以帮忙_检查_它们，并在程序运行前指出错误。有很多 JavaScript 的方言把类型加入到其语言中，并检查它们。最流行的一个叫做 [TypeScript](https://www.typescriptlang.org/)。你如果有兴趣让你的程序更严谨，我建议你试试它。

本书将继续使用原始的、危险的、无类型定义的 JavaScript。

## Testing

{{index "test suite", "run-time error", automation, testing}}

如果语言本书不会帮我们找寻我们的错误，我们只能走那条艰难的路了：运行程序，并检测它是否运行正确。

如此反复人工运行是个坏主意。这不仅讨嫌，而且还可能无效。因为每次更改后，它都需要花费太多时间去详尽的测试所有内容。

电脑对待重复的任务却很有一套。测试是一个理想中的重复任务。自动测试是编写一个程序来测试另一个程序的过程。写测试比手动测试需要略多的活，但一旦写完，你就拥有了一个超能力：几秒内你就可确保你的程序在所以已写的测试内都工作。当你破坏了某段代码时，你会通过这写测试立刻发现，而不是以后运行时的随机触发。

{{index "toUpperCase method"}}

测试通常采用带有少量标签的程序形式来验证代码中的某些方面。比如，一组给方法 `toUpperCase` 的测试（标准的，可能已经被其他人测试过了）可能如下：

```
function test(label, body) {
  if (!body()) console.log(`错误的: ${label}`);
}

test("把拉丁文字改成大写", () => {
  return "hello".toUpperCase() == "HELLO";
});
test("把希腊文字改成大写", () => {
  return "Χαίρετε".toUpperCase() == "ΧΑΊΡΕΤΕ";
});
test("不改变没有大小写区分的文字", () => {
  return "مرحبا".toUpperCase() == "مرحبا";
});
```

{{index "domain-specific language"}}

写类似这种测试时，通常会出现很多反复的、尴尬的代码。好在有些软件就是帮助你创建并运行测试（_((测试套件))_）的。他们提供一个适合表达测试的语言（以函数和方法的形式），并在测试失败时返回有用的信息。这些通常叫做 _((test runners))_。

{{index "persistent data structure"}}

有些代码比其他代码容易测试。通常来讲，于外来对象互动越多的代码越难测试。[上一章](robot)中的编程风格，以独立的不改变的值代替可变的对象，往往容易测试。

## Debugging

{{index debugging}}

当你发现程序有错误时，下一步就是找寻问题的_来源_。

有些时候它很明显。((错误))报告会指出一行代码。你如果看问题描述和那行代码，你通常可以发现问题所在。

{{index "run-time error"}}

但这不是所有情况。有些时候，那行产生错误的代码只是第一次无效使用一个已经错误值的地方。你如果解答了前几章中的((习题))，你或许已经遇到过类似的情况。

{{index "decimal number", "binary number"}}

下面的例题程序试图把一个整数编程一个给定进制（二进制、十进制等）的字符串。它反复选出最后的((数字))并除以一个数来清楚该数字。但返回的奇怪输出表明该程序有一个 ((bug))。

```
function numberToString(n, base = 10) {
  let result = "", sign = "";
  if (n < 0) {
    sign = "-";
    n = -n;
  }
  do {
    result = String(n % base) + result;
    n /= base;
  } while (n > 0);
  return sign + result;
}
console.log(numberToString(13, 10));
// → 1.5e-3231.3e-3221.3e-3211.3e-3201.3e-3191.3e-3181.3…
```

{{index analysis}}

如果你见过这个问题，假装你没有。已知我们的程序有问题，我们需要知道为什么。

{{index "trial and error"}}

此时的你必须抵抗随机改变代码来检测问题是否解决的冲动。反之，_思考_。分析下发生了什么，并提出一个((理论))来解释为什么。之后进行额外观察来检测这个理论。或者，你如果没有一个理论的话，进行额外观察来帮助你提出一个理论。

{{index "console.log", output, debugging, logging}}

在程序中放几个有战略性的 `console.log` 是一个可以得到更到有关程序信息的好办法。在这个情况下，我们希望 `n` 先拿值 `13`，`1`，之后 `0`。让我们在循环前输出它的值。

```{lang: null}
13
1.3
0.13
0.013
…
1.5e-323
```

{{index rounding}}

_是的_。13 除以 10 无法得到一个整数。所以我们想要的是 `nn = Math.floor(n / base)` 而不是 `n /= base`。如此这个数字才会正确的向右“移动”。

{{index "JavaScript console", "debugger statement"}}

一个取代 `console.log` 来窥视程序行为的方法是用浏览器的调试器功能。浏览器可以在在指定的一行设置_((断点))_。当程序运行到有断点的那行时，它会暂停。你就可以查看在该时段的变量值了。因为每个浏览器的调试器都不一样，所以我不会详细介绍。查看你的浏览器的((开发者工具))或搜索网页获取更多信息。

另一个设定断点的方法是在程序中加入 `debugger`（仅由该关键字组成）语句。如果你的浏览器的((开发者工具))被打开了，程序就会在抵达该语句时暂停。

## Error propagation

{{index input, output, "run-time error", error, validation}}

遗憾的是，程序员无法预防所有问题。如果你的程序和外界有任何交流，它就有可能得到一个异常的输入；过大的工作量；或者网络连接失败。

{{index "error recovery"}}

如果你只给自己写程序，那么你可以无视这些问题，直到他们出现为止。只要你写的程序被其他人使用，你通常希望这个程序比崩溃做的更好。有些时候，正确的做法是无视错误的输入，并继续运行。其他时候，最好告诉用户出错后，放弃运行。无论如何，该程序都需要在问题面前做些什么。

{{index "promptNumber function", validation}}

比如你有一个函数 `promptNumber` 需要用户输入一个数字，并返回该数字。当用户输入 "orange" 时该程序应该返回什么呢？

{{index null, undefined, "return value", "special return value"}}

一个选择是让它返回一个特殊值。常见选项包括 `null`，`undefined`，或者 -`。

```{test: no}
function promptNumber(question) {
  let result = Number(prompt(question));
  if (Number.isNaN(result)) return null;
  else return result;
}

console.log(promptNumber("你见到了多少树？"));
```

现在任何调用 `promptNumber` 的代码都必须检查读取的是不是一个真正的数。如果不是，必须想办法恢复：可以是重问一次，或者提供一个默认值。它也可以返回一个特殊值给_它的_调用者表示它无法完成任务。

{{index "error handling"}}

许多情况下，通常当((错误))较为普遍且调用者需明确考虑他们时，返回一个特殊值是一个提示错误的好方法。然而有时它也有缺点。首先，如果该函数可以返回所有类型的值该如何？这种函数下，你需要把结果包在一个对象中，以便区分成功和失败。

```
function lastElement(array) {
  if (array.length == 0) {
    return {failed: true};
  } else {
    return {element: array[array.length - 1]};
  }
}
```

{{index "special return value", readability}}

其次，返回特殊值可能会产生尴尬的代码。如果一个程序调用 `promptNumber` 10次，它需要检查 10次返回的值是不是 `null`。如果它遇到 `null` 的反映只是简单的返回 `null`，该函数的调用者也需要检查 `null`。依此类推。

## Exceptions

{{index "error handling"}}

当一个函数不能正常运行，我们_希望_停止当前活动，并立刻跳到一个知道如何处理该问题的地方。这就是_((错误处理))_。

{{index ["control flow", exceptions], "raising (exception)", "throw keyword", "call stack"}}

异常是一种机制，可以让遇到问题的代码_提出_（或_抛出_）一个异常。一个异常可以是任何值。提出一个类似一个函数的超额（super-charged）返回值：它不仅跳出当前函数，也会跳出该函数的调用者，直到第一个引起该错误的调用者。这叫做_((展开堆栈))_。你或许会记得[第三章](functions#stack)中提到的函数调用堆栈。一个异常会依据这个堆栈，抛去它遇到的所有相关调用。

{{index "error handling", [syntax, statement], "catch keyword"}}

如果异常总是发生在堆栈的最底部，那它就没有太大帮助。它只是提供了一个新的膨胀你程序的方法而已。它的力量在于你可以沿途设置一些“障碍物”，在异常沿堆栈返回时_捕获_它。当你捕获到一个异常时，你可以在处理该问题后继续运行程序。

例题如下：

{{id look}}
```
function promptDirection(question) {
  let result = prompt(question);
  if (result.toLowerCase() == "left") return "L";
  if (result.toLowerCase() == "right") return "R";
  throw new Error("无效的方向: " + result);
}

function look() {
  if (promptDirection("哪边？") == "L") {
    return "一个房子";
  } else {
    return "两只熊";
  }
}

try {
  console.log("你看到", look());
} catch (error) {
  console.log("出错啦: " + error);
}
```

{{index "exception handling", block, "throw keyword", "try keyword", "catch keyword"}}

关键词 `throw` 用来提出一个异常。可以通过把一段代码包裹在 `try` 块语句中，其次是关键字 `catch` 来捕获异常。当 `try` 块语句中的代码出现异常时，`catch` 块语句会和它参数中绑定的异常值一起被运行。在 `catch` 块语句结束后，或者 `try` 块语句正常运行结束后，程序照常运行整个 `try/catch` 语句下的代码。

{{index debugging, "call stack", "Error type"}}

该情况下，我们用 `Error` ((构造器))创建我们的异常值。这是一个((标准的)) JavaScript 构造器。它会创造一个带有 `message` 属性的对象。大部分 JavaScript 环境下，这个构造器的实例也收集关于在异常发生时堆栈的信息。也叫做_((堆栈跟踪))_。这些信息储存在 `stack` 属性中，且在调试时会有帮助。它告诉我们该异常发生的函数，以及所有导致该错误的调用函数。

{{index "exception handling"}}

注意例题中的 `look` 函数完全忽略了 `promptDirection` 会出错的可能性。这就是异常的一大优点。处理错误的代码只需在错误发生点和处理点出现。中途的其他函数可以完全无视它。

好吧，大致如此……

## Cleaning up after exceptions

{{index "exception handling", "cleaning up", ["control flow", exceptions]}}

异常的影响是另一种控制流。任何可能产生异常的行为，基本上是所以函数调用和属性访问，都可能让控制突然立刻你的代码。

也就是说，当代码有多种副作用时，即使它的“常规”控制流似乎总会运行，一个异常可能会阻止其中一些运行。

{{index "banking example"}}

下面是一段非常烂的银行代码。

```{includeCode: true}
const accounts = {
  a: 100,
  b: 0,
  c: 20
};

function getAccount() {
  let accountName = prompt("输入用户名");
  if (!accounts.hasOwnProperty(accountName)) {
    throw new Error(`没有该用户: ${accountName}`);
  }
  return accountName;
}

function transfer(from, amount) {
  if (accounts[from] < amount) return;
  accounts[from] -= amount;
  accounts[getAccount()] += amount;
}
```

函数 `transfer` 从一个指定账户转账到另一个，途中询问另一个用户名。如果提供了错误的用户名，`getAccount` 会抛出一个异常。

但 `transfer` _首先_从账户中移除这笔钱，_之后_才在添加到另一个账户前调用 `getAccount`。如果此时程序因为异常而终止，这笔钱会直接消失。

这段代码可以被更好的编写。比如，在移除钱之前调用 `getAccount`。但通常类似的问题会在更微妙的情况下发生。即使一些看似不会出现异常的函数也会在特殊情况下抛出异常，或者在程序员写错代码时。

解决此问题的一个方法是减少副作用。计算新的值，而不是改变已有数据，的编程风格是一个办法。如果一段代码在创建新值时停止运行，不会有人看到那个半完成的值，也就不会产生任何问题。

{{index block, "try keyword", "finally keyword"}}

但这并不总是可行。所以 `try` 语句有另一个特点。它后面可以跟着一个 `finally` 块语句，可代替或在 `catch` 块语句的基础上添加。`finally` 块语句是“无论任何时候，都要在试图运行 `try` 块语句代码后运行该代码。”

```{includeCode: true}
function transfer(from, amount) {
  if (accounts[from] < amount) return;
  let progress = 0;
  try {
    accounts[from] -= amount;
    progress = 1;
    accounts[getAccount()] += amount;
    progress = 2;
  } finally {
    if (progress == 1) {
      accounts[from] += amount;
    }
  }
}
```

此版本的函数跟踪其进程。在结束时，它如果发现它中途终止而导致程序状态不一致时，它可以修复这个损伤。

尽管 `finally` 在 `try` 块语句抛出异常后依旧运行，它并不会干扰该异常。在 `finally` 块语句结束后，堆栈会继续递减。

{{index "exception safety"}}

编写可以在异常发生后仍可靠且正常运行的程序很难。很多人甚至不会花费心思。因为异常通常为特殊情况所保留，这个问题很可能极少发生，以致它从未被发觉。至于这是件好事还是坏事，完全取决于在程序停止运行时所造成的灾害。

## Selective catching

{{index "uncaught exception", "exception handling", "JavaScript console", "developer tools", "call stack", error}}

当一个异常在没被捕获的情况下抵达堆栈的底层时，它会被环境处理。因此结果由环境决定。在浏览器中，该问题的描述通常会显示在 JavaScript console 中（可通过浏览器的工具或者开发者菜单到达）。Node.js，一个将在[第二十章](node)中讨论的无浏览器的 JavaScript 环境，会对数据损坏更加小心谨慎。当一个未处理的异常发生时，它会终止整个过程。

{{index crash, "error handling"}}

对于程序员的错误，让错误通过往往是你能做的最好的事情。一个没处理的异常是一个符合常理的信号问题程序的方法。JavaScript console 此时，在现代浏览器中，会提供一些在堆栈中，当问题发生时函数调用的信息。

{{index "user interface"}}

对于日常使用中，_可能_发生的问题，因为一个未处理的异常而终止是一个可怕的策略。

{{index [function, application], "exception handling", "Error type", [binding, undefined]}}

语言的错误使用，比如引用一个不存在的变量，查询 `null` 的属性，或者调用一个非函数，都会产生异常。这类异常可以被捕获。

{{index "catch keyword"}}

当运行一个 `catch` 语句体时，我们只知道 `try` 块语句的_某行_抛出异常。但我们不知道_什么_导致的，或者它产生了_哪个_异常。

{{index "exception handling"}}

JavaScript（明显的遗漏中）并不提供直接捕获选择异常的支持：你要么捕获所以异常，要么一个都不要捕获。这会让我们_以为_被捕获的异常就是我们在写 `catch` 块语句时设想的那个。

{{index "promptDirection function"}}

但这并不一定。其他((假设))也可能被违反，或者你也许引进了一个产生该异常的 bug。下面是一个试图继续调用 `promptDirection` 直到它得到一个有效答案为止的例题：

```{test: no}
for (;;) {
  try {
    let dir = promtDirection("哪里？"); // ← 拼写错误！
    console.log("你的选择 ", dir);
    break;
  } catch (e) {
    console.log("不是有效的方向。重新尝试。");
  }
}
```

{{index "infinite loop", "for loop", "catch keyword", debugging}}

`for (;;)` 故意创建一个不会自动结束的循环。我们只在得到一个有效方向后才会终结该循环。_但_我们拼错了 `promptDirection`，从而导致一个 "undefined variable" 错误。可是 `catch` 块语句，以为知道问题的起因，而完全无视它的异常值（`e`）。它因此错误的把绑定错误当成输入错误。这不但导致了无限循环，它也“埋葬”了有关拼写问题的错误信息。

基本来讲，不要一网打尽异常，除非是为了把他们“引导至”其他地方。比如，通过网络告诉另一个系统我们的程序崩溃了。即使如此，也要仔细想想你可能如何隐藏了重要信息。

{{index "exception handling"}}

我们想捕获一个_特定_的异常。我们可以在 `check` 块语句里校验所得异常是不是我们想要的，如果不是要重新抛出。但我们该如何识别一个异常？

我们可以把异常的 `message` 属性和我们期待的((错误))信息对比。但这不是写代码的应有方式。我们在使用给人类的信息来决定一个程序的走向。一旦有人改变（或翻译）了这个信息，这个代码就会停止工作。

{{index "Error type", "instanceof operator", "promptDirection function"}}

我们定义一个新的错误类型，并用 `instanceof` 来识别它。

```{includeCode: true}
class InputError extends Error {}

function promptDirection(question) {
  let result = prompt(question);
  if (result.toLowerCase() == "left") return "L";
  if (result.toLowerCase() == "right") return "R";
  throw new InputError("方向无效: " + result);
}
```

{{index "throw keyword", inheritance}}

新的错误类扩展 `Error`。它没有定义自己的构造器，所以继承了 `Error` 的构造器.也就是说它需要一个字符串作为参数。实际上，它其实没有定义任何东西，整个类都是空的。对象 `InputError` 类似 `Error`，除了它有一个不同的我们可以识别的类。

{{index "exception handling"}}

现在这个循环可以更谨慎的捕获异常了。

```{test: no}
for (;;) {
  try {
    let dir = promptDirection("哪里？");
    console.log("你选择 ", dir);
    break;
  } catch (e) {
    if (e instanceof InputError) {
      console.log("方向无效。重试。");
    } else {
      throw e;
    }
  }
}
```

{{index debugging}}

这只会捕获 `InputError` 的实例。任何无关的异常都会通过。如果你重新介绍了拼写错误，未定义的变量就会被正确报告。

## Assertions

{{index "assert function", assertion, debugging}}

_断言_是程序内部的判断，用来确认某件事是它应有的样子。它们用来处理可能在正常情况下出现的情况，但找到程序员的错误。

比如，如果 `firstElement` 是一个永远不能被空数组调用的函数的话，我们可能把它写成：

```
function firstElement(array) {
  if (array.length == 0) {
    throw new Error("firstElement 不能被 [] 调用");
  }
  return array[0];
}
```

{{index validation, "run-time error", crash, assumption}}

现在，与其默默返回 undefined（就是你读取一个不存在的数组属性时的结果），它会在你错误运用时，返回错误提示。如此会避免此类错误被忽略，而且在错误发生时，更容易找到原因。

我不推荐试图为每个可能的错误输入写断言。如此不仅工程浩大，且代码也显得杂乱。你应该把它们预定给容易发生的输入错误（或者你做的错误）。

## Summary

错误和输入错误是人生常态。编程的重要一点是找寻，诊断，和修复 bugs。如果你的程序有一个自动化测试或添加了断言的话，问题会被更容易的发现。

对于程序无法掌控的外界因素所引发的问题应妥善处理。有时，当程序可在本地处理时，返回特殊值是一个很好的跟踪他们的方法。否则，异常会更可取。

引发异常会导致堆栈调用被取消，直到下一个封闭的 `try/catch` 块语句或堆栈的底层为止。异常值将提供给捕获它的 `catch` 块语句。该块语句应该先确定它是预期中的异常后，再对其进行处理。为了帮助解决由异常引起的不可预测的控制流，可以使用“finally”块来确保在块语句结束时_永远_运行一段代码

## Exercises

### Retry

{{index "primitiveMultiply (exercise)", "exception handling", "throw keyword"}}

你有一个函数 `primitiveMultiply` 在 20% 情况下对两个数进行乘法，在剩下的 80% 情况下，会抛出一个类型为 `MultiplicatorUnitFailure` 的异常。写一个函数包裹住这个笨拙的函数，并反复尝试直到一个成果的调用，后返回结果。

{{index "catch keyword"}}

确保你只处理需要处理的异常。

{{if interactive

```{test: no}
class MultiplicatorUnitFailure extends Error {}

function primitiveMultiply(a, b) {
  if (Math.random() < 0.2) {
    return a * b;
  } else {
    throw new MultiplicatorUnitFailure("Klunk");
  }
}

function reliableMultiply(a, b) {
  // 你的代码.
}

console.log(reliableMultiply(8, 8));
// → 64
```
if}}

{{hint

{{index "primitiveMultiply (exercise)", "try keyword", "catch keyword", "throw keyword"}}

调用 `primitiveMultiply` 的代码肯定应该在 `try` 块语句中。相应的 `catch` 块语句应该重新抛出异常，如果它的类型不是 `MultiplicatorUnitFailure`。如果异常类型一致的话，要确保重新调用该函数。

你可以用一个只有在调用成果后才结束的循环来实现重试。可参考本章出现的[`look` 例子](error#look)。或者一个((递归))，需要确保你不会因为堆栈溢出而得到一个字符串的错误提示（这是一个安全的选择）。

hint}}

### The locked box

{{index "locked box (exercise)"}}

考虑下面的（较为人为的）对象：

```
const box = {
  locked: true,
  unlock() { this.locked = false; },
  lock() { this.locked = true;  },
  _content: [],
  get content() {
    if (this.locked) throw new Error("Locked!");
    return this._content;
  }
};
```

{{index "private property", "access control"}}

这是一个有锁的((箱子))。箱子中有一个数组，但只有在箱子被解锁时，你才可以拿到它。禁止直接访问私有的 `_content` 属性。

{{index "finally keyword", "exception handling"}}

写一个名为 `withBoxUnlocked` 函数，它的参数是一个函数值，解锁盒子，运行该函数，并在返回前确保盒子重新锁好。无论参数函数是正常返回还是抛出一个异常。

{{if interactive

```
const box = {
  locked: true,
  unlock() { this.locked = false; },
  lock() { this.locked = true;  },
  _content: [],
  get content() {
    if (this.locked) throw new Error("Locked!");
    return this._content;
  }
};

function withBoxUnlocked(body) {
  // 你的代码.
}

withBoxUnlocked(function() {
  box.content.push("金币");
});

try {
  withBoxUnlocked(function() {
    throw new Error("海盗在地平线上！终止！");
  });
} catch (e) {
  console.log("抛出问题：" + e);
}
console.log(box.locked);
// → true
```

if}}

额外加分，你如果在盒子已解锁时调用 `withBoxUnlocked`，该盒子应保持解锁状态。

{{hint

{{index "locked box (exercise)", "finally keyword", "try keyword"}}

这个练习需要一个 `finally` 块语句。你的函数应该先解锁盒子，再在一个 `try` 中调用参数函数。它后面的 `finally` 块语句应该重新锁上盒子。

为了确保我们没有锁上未被锁定的盒子，在函数开始时检查盒子锁的状态，并只在盒子被锁定时，解锁并重新锁定盒子。

hint}}
