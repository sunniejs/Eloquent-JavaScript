{{meta {load_files: ["code/packages_chapter_10.js", "code/chapter/07_robot.js"]}}}

# 模块

{{quote {author: "Tef", title: "Programming is Terrible", chapter: true}

写容易删除的代码，而不是容易扩展的代码。

quote}}

{{index "Yuan-Ma", "Book of Programming"}}

{{figure {url: "img/chapter_picture_10.jpg", alt: "Picture of a building built from modular pieces", chapter: framed}}}

{{index organization, [code, "structure of"]}}

一个理想中的程序有着非常清晰的结构。它的工作方式易于解释，且每一部分都扮演明确的角色。

{{index "organic growth"}}

一个典型的真实程序会有机地生长。新的功能随着新需求的增加而增加。结构化 —— 和保持该程序的结构 —— 则是额外的工作。这样的工作只会在未来得到回报，也就是 _下_ 次某个人在这个程序上工作的时候。因此，一个诱人的做法是忽略这样的工作而让程序的部分代码变得纠缠不清。

{{index readability, reuse, isolation}}

这会产生两个实际的问题。首先，要想了解这样一个系统是困难的。如果所有的代码都可以相互影响，那将很难去单独地看任何一段代码。这会迫使你对整个系统有一个整体的了解。其次，如果你想在另一个情况下使用该程序的任何一个功能的话，重写它可能比试图解开与其纠缠在一起的相关代码更容易。

"((大泥球))" 这个短语就通常用来形容这样庞大而没有结构的程序。所有代码都粘在一起，当你想要挑出其中一部分时，整个泥球都溃散了，而且还弄的你满手是泥。

## 模块

{{index dependency, [interface, module]}}

_模块_ 是避免这些问题的尝试。一个((模块))是一段程序，这段程序标注了它依靠哪些额外的代码，以及它本身为其他模块提供了什么功能(它的 _接口_)。 

{{index "big ball of mud"}}

模块接口与对象接口有很多类似之处，就像我们在 [第六章](object#interface) 所看到的那样。它们使部分模块可以被外界使用，而其余的部分保持私密。通过限制模块之间交互的渠道，系统变得更像((乐高))，各个部分通过有着明确定义的连接器来交互，而不像泥土那样混杂在一起。

{{index dependency}}

模块之间的关系被称为 _依赖_。当一个模块需要来自另外的模块的一段代码时，我们就说该模块依赖另外的那个模块。当这个事实在模块中被明确写出来，它就可以被用来搞清楚哪些另外的模块的存在得以使当下这个模块顺利运行，且这些依赖项会被自动加载。

要以这种方式来分离模块，每个模块都需要自己私有的((作用域))。

单纯地将你的 JavaScript 代码放入不同的((文件))并不能满足这些条件。这些文件仍旧共享一个全局命名空间，它们可能会有意或无意地干扰彼此的绑定。而且依赖结构仍不清晰。我们将在本章后面的内容中看到如何能做得更好。

{{index design}}

为一个程序设计合适的模块结构可能并不容易。在你仍然在探索问题的阶段，你会尝试不同的东西看哪些有效，这时你可能不太想考虑模块的结构，因为它会分散你不少注意力。当你感觉你的程序稳了的时候，这时是退一步开始组织模块结构的好时机。

## 包

{{index bug, dependency, structure, reuse}}

用分开的代码片段来构建程序、以及真正能够独立运行这些片段的优点之一在于，你或许可以在不同的程序中应用相同的代码片段。

{{index "parseINI function"}}

但是这该如何设置呢？比方说我要在另一个程序中使用 [第九章](regexp#ini) 里的 `parseINI` 函数，如果我很清楚这个函数依赖什么(在这个情况下，什么也不依赖)，那便可以把所有必要的代码都复制到我的新项目中并且使用它。然而，一旦我在那段代码中发现一个错误之处，我可能只会在当下正在开发的程序中纠正这个错误，而忘记在别的程序中也把它给改过来。

{{index duplication, "copy-paste programming"}}

一旦你开始复制代码，你很快会发现自己将时间和精力浪费在到处移动副本并让它们保持与时俱进这两件事情上。

而这时就显出 _((包))_ 的用武之地了。包是可以被分发(复制及安装)的一小段代码。它可能包含一个或多个模块，还包括它依赖哪些其他的包的信息。一个包通常会附有解释其功能的文档，以便让那些没有写它的人也可以使用
它。

当在包里发现一个问题或添加新特性时，包会被更新。然后依赖于这个包的程序(可能也是包)可以升级到最新((版本))。

{{id modules_npm}}

{{index installation, upgrading, "package manager", download, reuse}}

以这种方式工作需要有((基础设施))，即我们需要一个可以存放和找寻包的地方，以及一种便于安装和升级它们的方式。在 JavaScript 的世界里，((NPM))([_https://npmjs.org_](https://npmjs.org)) 提供了这个基础设施。

NPM 是两项东西：一项是提供下载(及上传)包的在线服务，另一项则是帮助你安装和管理包的程序(与 Node.js 捆绑在一起)。 

{{index "ini package"}}

在撰写本文时，在 NPM 上有超过 50 万个不同的包可供使用。我应该说明，其中很大一部分是垃圾，然而几乎所有有用的、公开可用的包在那里都可以被找到。举个例子，与我们在 [第九章](regexp) 里所写的程序类似的 INI 解析器可以在名为 `ini` 的软件包里找到。 

{{index "command line"}}

[第二十章](node) 的内容会介绍如何用 `npm` 命令行程序在本地安装这样的包。

拥有可供下载的高质量的包是相当有价值的。这意味着我们通常可以避免重新发明一个已经被 100 个人写过的程序，而只需要按几个键就可以得到一个可靠的、几经测试的程序的实现。

{{index maintenance}}

复制软件是廉价的，所以一旦有人编写了该软件，将其分发给其他人的过程是有效率的。但是一开始写它的时候 _确实是_ 工作，而去回应那些在代码中发现问题、或想提出新增特性的人，则意味着更多的工作。

在默认情况下，你拥有你所写的代码的((版权))，其他人只有在你允准的情况下才能使用这些代码。然而因为有的人就是乐于助人，还因为发布优秀的软件可以帮助你在程序员的圈子里传出一点小小的名气，很多包都是在明确允许他人使用的 ((许可证)) 名下发布的。

((NPM)) 上的大多数代码都是通过这种方式获得许可的。有的包的许可证要求你把在那个包的基础之上开发的代码也发表在同一许可证下。别的许可证就没那么苛刻，只要求你在分发代码时标明其许可证。JavaScript 社区大多数使用后一种许可证。在使用其他人的包的时候，请确保你了解它们的许可证。

## 即兴设计的模块

直到 2015 年，JavaScript 语言才开始拥有内置的模块系统。然而人们使用 JavaScript 来构建大型系统已经十余年了，他们 _曾经需要_ ((模块))。

{{index [function, scope], [interface, module], [object, as module]}}

因此，他们在语言上设计了自己的((模块系统))。你可以使用 JavaScript 函数来创建本地作用域和对象来表示模块接口。

{{index "Date class", "weekDay module"}}

这是一个在日期名和日期数之间切换的模块(像 `Date` 的 `getDay` 方法所返回的)。它的接口包含 `weekDay.name` 和 `weekDay.number`，并且它将本地绑定 `names` 隐藏在马上被调用的函数表达式的作用域中。

```
const weekDay = function() {
  const names = ["Sunday", "Monday", "Tuesday", "Wednesday",
                 "Thursday", "Friday", "Saturday"];
  return {
    name(number) { return names[number]; },
    number(name) { return names.indexOf(name); }
  };
}();

console.log(weekDay.name(weekDay.number("Sunday")));
// → Sunday 礼拜天
```

{{index dependency, [interface, module]}}

这种风格的模块在某种程度上提供了((隔离))，但它没有声明依赖项。相反地，它只是将其接口放入((全局作用域))，且期望其依赖项(如果有的话)会执行相同的操作。在很长一段时间里，这是网页编程的主流做法，然而现在基本已经过时了。

如果我们想让依赖关系成为代码的一部分，我们会需要对依赖项的加载进行控制。做到这一点需要将字符串作为代码来执行，JavaScript 足以胜任。 

{{id eval}}

## 视数据为代码

{{index evaluation, interpretation}}

有好几种方法可以获取数据(一段字符串代码)并将其作为当前程序的一部分来运行。

{{index isolation, eval}}

最显而易见的方法是使用特殊运算符 `eval`，它会在 _当前_ ((作用域))中执行一段字符串。这通常是个馊主意，因为它破坏了作用域常有的某些属性，比如说一个特定名称指的是哪一个绑定本来是容易预测的。

```
const x = 1;
function evalAndReturnX(code) {
  eval(code);
  return x;
}

console.log(evalAndReturnX("var x = 2"));
// → 2      在本地作用域里，x 的值是 2
console.log(x);
// → 1      在全局作用域里，x 的值是 1
```

{{index "Function constructor"}}

将数据解读为代码的一种不那么可怕的方法是使用 `Function` 构造器。它接受两个参数：一段包含以逗号分隔的参数名列表的字符串，以及一段包含函数主体的字符串。它将代码包装在一个函数值中，从而拥有自己的作用域，便不会对其他的作用域做出奇怪的事情。

```
let plusOne = Function("n", "return n + 1;");
console.log(plusOne(4));
// → 5
```

这正是我们需要的模块系统，我们可以将模块的代码包装在一个函数中，并以那个函数的作用域作为模块((作用域))。

## CommonJS

{{id commonjs}}

{{index "CommonJS modules"}}

被使用最多的非官方 JavaScript 模块的方法是 _CommonJS 模块_。((Node.js)) 是 ((NPM)) 上大多数包都在使用的系统，它也采用 CommonJS 模块。 

{{index "require function", [interface, module]}}

CommonJS 模块主要的概念是一个名为 `require` 的函数。当你用依赖项的模块名称调用这个函数时，它会确保该模块被加载并返回其接口。

{{index "exports object"}}

因为加载器将模块代码包装在一个函数里，这些模块会自动获取它们自己的本地作用域。它们只需要调用 `require` 来访问依赖项，并且把它们的接口放在与 `exports` 绑定的对象中。

{{index "formatDate module", "Date class", "ordinal package", "date-names package"}}

以下的示例模块提供了一个格式化日期的函数。它用了两个来自 NPM 的((包)) —— 将数字转换成类似 `"1st"` 和 
`"2nd"` 的 `ordinal`，以及获取工作日和月份的英文名称的 `date-names`。该模块只导出一个名为 `formatDate` 的函数，该函数接受一个 `Date` 对象以及一个((模板))字符串。

模板字符串可能含有表示格式的代码，例如 `YYYY` 表示全年，`Do` 表示月份中的第几天。你可以提供一个类似 `"MMMM Do YYYY"` 的字符串来得到 "November 22nd 2017" 这样的输出。

```
const ordinal = require("ordinal");
const {days, months} = require("date-names");

exports.formatDate = function(date, format) {
  return format.replace(/YYYY|M(MMM)?|Do?|dddd/g, tag => {
    if (tag == "YYYY") return date.getFullYear();
    if (tag == "M") return date.getMonth();
    if (tag == "MMMM") return months[date.getMonth()];
    if (tag == "D") return date.getDate();
    if (tag == "Do") return ordinal(date.getDate());
    if (tag == "dddd") return days[date.getDay()];
  });
};
```

{{index "destructuring binding"}}

`ordinal` 的接口是一个函数，而 `date-names` 导出包含多个事物的对象 —— `days` 和 `months` 是含有名称的数组。解构在为导入的接口创建绑定时非常方便。

该模块把它的接口函数添加到 `exports`，从而让依赖它的模块可以访问它。我们可以这样使用该模块：

```
const {formatDate} = require("./format-date");

console.log(formatDate(new Date(2017, 9, 13),
                       "dddd the Do"));
// → Friday the 13th    周五，本月13号
```

{{index "require function", "CommonJS modules", "readFile function"}}

{{id require}}

我们可以用最简便的形式来定义 `require`，如下所示：

```{test: wrap, sandbox: require}
require.cache = Object.create(null);

function require(name) {
  if (!(name in require.cache)) {
    let code = readFile(name);
    let module = {exports: {}};
    require.cache[name] = module;
    let wrapper = Function("require, exports, module", code);
    wrapper(require, module.exports, module);
  }
  return require.cache[name].exports;
}
```

{{index [file, access]}}

在这段代码中，`readFile` 是一个虚构的函数，它能读取文件并将其内容以字符串的形式返回。标准的 JavaScript 并没有提供这样的功能 —— 但类似浏览器或 Node.js 这些不同的 JavaScript 环境都提供了它们自己的文件访问方式。这个示例只是假定 `readFile` 存在。

{{index cache, "Function constructor"}}

为了避免多次加载同一模块，`require` 会保留一个已加载的模块的存储(缓存)。在被调用时，它首先会查看请求的模块是否已被加载，如尚未加载才会进行加载。这涉及了读取该模块的代码，将其包装在函数中，并且调用它。

{{index "ordinal package", "exports object", "module object", [interface, module]}}

我们之前看到的 `ordinal` 包的接口是一个函数，而不是一个对象。CommonJS 模块的一个妙用在于，虽然模块系统会为你创建一个空的接口对象(与 `exports` 绑定)，你可以覆盖 `module.exports` 从而用任何值替代它。这是由许多模块完成的，它们导出单个值，而不是一个接口对象。

通过将 `require`，`exports` 和 `module` 定义为生成的包装函数(并在调用它时传递适当的值)的((参数))，加载器会确保这些绑定在模块的((作用域))中可以被使用。

{{index resolution, "relative path"}}

在不同的系统中，传递给 `require` 的字符串被解读为一个真实的文件名或网络地址的方式也会有所不同。当字符串的开头为 `"./"` 或 `"../"` 的时候，这通常会被解读为在当前模块的文件名的相对路径上。所以 `"./format-date"` 意味着名为 `format-date.js` 的文件和当前模块处于同一个目录。

当名称不在相对路径时，Node.js 会根据该名称寻找已安装的包。在本章的示例代码中，我们将此类名称解读为指代 NPM 包。我们在 [第二十章](node) 会更详细地介绍如何安装及使用 NPM 模块。

{{id modules_ini}}

{{index "ini package"}}

现在，与其编写我们自己的 INI 文件解析器，我们可以用 ((NPM)) 上的包。

```
const {parse} = require("ini");

console.log(parse("x = 10\ny = 20"));
// → {x: "10", y: "20"}
```

## ECMAScript 模块

((CommonJS 模块))运作良好，而且与 NPM 的组合已经使得 JavaScript 社区开始大规模地共享代码。

{{index "exports object", linter}}

可是它们仍旧有点((治标不治本))，其导入导出的((写法))有点尴尬 —— 比如说，你添加到 `exports` 的内容在本地((作用域))中无法使用。而且 `require` 是一个普通的函数调用，它接受任何类型的参数，而不仅仅是字符串文字，因此想要在不运行模块的代码的情况下确定其依赖项是困难的。

{{index "import keyword", dependency, "ES modules"}}

{{id es}}

这也就是为什么 JavaScript 标准自 2015 年起介绍了自己独特的模块系统。它通常被称为 _((ES 模块))_，_ES_ 表示 ((ECMAScript))。关于依赖项和接口的主要概念保持不变，但是细微之处有所不同。一方面，导入导出的写法现已集成于语言中。与其调用一个函数来访问依赖项，你得使用专门的 `import` 关键词。

```
import ordinal from "ordinal";
import {days, months} from "date-names";

export function formatDate(date, format) { /* ... */ }
```

{{index "export keyword", "formatDate module"}}

与之类似的是，`export` 关键词被用于导出内容。它有可能出现在函数、类或者绑定定义(`let`，
`const` 或 `var`)的前面。 

{{index [binding, exported]}}

一个 ES 模块的接口并不是单一的值，而是一组已命名的绑定。上述模块将 `formatDate` 绑定到一个函数。当你从另一个模块进行导入时，你导入的是这个 _绑定_，而不是它的值，这意味着一个导出模块可以随时改变绑定的值，而导入它的模块则会得到新的值。

{{index "default export"}}

当名为 `default` (译者注：默认情况)的绑定出现时，它会被视为该模块的主要导出值。如果你像在示例中那样导入 `ordinal` 模块，在绑定名的周围没有围上大括号的话，你将会得到它的 `default` 绑定。除了 `default` 导出之外，这样的模块仍可以同时导出其他有着不同名字的绑定。

要想创建一个默认的导出，请在表达式、函数声明或类声明的前面写上 `export default`。

```
export default ["Winter", "Spring", "Summer", "Autumn"];
```

可以使用单词 `as` 对导入的绑定进行重命名。

```
import {days as dayNames} from "date-names";

console.log(dayNames.length);
// → 7    一周的天数
```

另一个重要的区别在于， ES 模块的导入会在模块的脚本开始运行之前发生。这意味着 `import` 声明可能不会出现在函数或代码块里，而且依赖项的名字必须是引号包裹的字符串，而不是任意的表达式。

在撰写本文时，JavaScript 社区正处于采用这种模块风格的过程中。然而，这是一个缓慢的过程。从这个格式被制定到浏览器和 Node.js 开始为其提供支持，这中间过了好几年。尽管它们现在大部分已经支持 ES 模块，它们的支持仍然存在问题，而且如何通过 ((NPM)) 对这样的模块进行分发的讨论仍在进行当中。

很多项目使用 ES 模块编写，之后在发布时被自动转化成其他的格式。我们正处于这两种模块系统被并排使用的过渡时期，在这两者的任何一种模块系统下都能够读写代码是大有裨益的。

## 开发及打包

{{index compilation, "type checking"}}

事实上，许多 JavaScript 项目从技术上来说甚至都不是用 JavaScript 写的。有很多拓展被广泛使用，比如说 [第八章](error#typing)提到的检查类型的((方言))。早在计划阶段的语言拓展被添加到运行 JavaScript 的平台上之前，人们就经常开始使用它们了。

为了做到这一点，他们对代码进行 _编译_，将其从他们选择的 JavaScript 方言翻译成原生 JavaScript —— 或者甚至是翻译成旧版的 JavaScript —— 以便旧的((浏览器))可以运行它。

{{index latency, performance, [file, access], [network, speed]}}

在((网页))中包括一个有着 200 个不同文件的模块化程序会带来麻烦。如果在网络中获取一个文件需要 50 毫秒，那么加载整个程序就需要 10 秒，如果你可以同时加载多个文件的话可能需要一半的时间。这样一来，很多时间被浪费掉了。由于获取单个大文件往往比获取很多小文件要更快，在把程序发布到网上之前，网页程序员已经开始使用把他们(下很多苦功夫给模块化)的程序回卷成单个大文件的工具。这样的工具被称为 _((打包器))_。

{{index "file size"}}

我们还可以更进一步，除了文件数量之外，文件的 _大小_ 也会决定它们在网络中被传输的速度。因此，JavaScript 社区发明了 _((精简器))_。这些工具可以接受一个 JavaScript 程序，然后自动删除注释和空格、重命名绑定，并且将代码段替换成占用更少空间的等效代码，从而让该程序变得精简。

{{index pipeline, tool}}

所以说，你在一个 NPM 上的包里看到的代码，或者说在一个网页上运行的代码可能已经经过了 _多重_ 转化阶段 —— 从现代 JavaScript 转换成旧版 JavaScript、从 ES 模块格式转换为 CommonJS格式、打包、精简，这都是很正常的。在这本书中，我们并不会详细介绍这些工具，因为它们往往比较无趣而且更迭得很快。只需要注意你运行的 JavaScript 代码通常不是它们被编写时的模样。

## 模块设计

{{index [module, design], [interface, module], [code, "structure of"]}}

构建程序是编程里比较微妙的方面之一。任何不是微不足道的功能的代码都可以通过多种方式被建立。

好的程序设计是主观的 —— 有利弊的权衡和个人喜好的不同。学习结构优美的设计最好的方法是阅读和开发大量的程序，并观察哪些行得通、哪些行不通。不要想当然地认为混乱不堪的状态才是常态，只要你肯投入更多的思考，几乎所有内容的结构都可以变得更好。

{{index [interface, module]}}

模块设计的其中一个方面是易于使用。如果你在设计一个计划被多人使用的程序 —— 甚至是三个月后已经将细节忘得一干二净的自己 —— 如果你的接口简洁而且符合常理，那将会很有用。

{{index "ini package", JSON}}

这可能意味着要遵循既定的传统。`ini` 包就是一个很好的例子。这个模块通过提供 `parse` 和 `stringify` (为了写一个 INI 文件)函数来模仿标准的 `JSON` 对象，并且像 `JSON` 一样在字符串和普通对象之间进行转换。所以它的接口小巧而熟稔，当你使用过一次之后，很可能会记住如何使用它。

{{index "side effect", "hard disk", composability}}

即使没有标准的函数或被广泛使用的包供你模仿，你可以通过使用简单的((数据结构))和只专注于一件事情来让你的模块变得符合常理。例如，很多 NPM 上的 INI 文件的解析模块提供了可以直接对硬盘上的文件进行读取和解析的函数。这意味着要想在浏览器中使用这样的模块是不可能的，因为我们在浏览器中并不能直接访问文件系统，这个功能会增加其复杂程度，且本该通过将一些读取文件的函数与模块 _谱写_ 来实现。

{{index "pure function"}}

这就提到了模块设计的另一个有益的方面  —— 容易与其他代码被共同谱写。与更庞大的、可以执行复杂动作且带有副作用的模块相比，专注于计算数值的模块能够适用于更广泛的程序。一个只能从磁盘读取文件的 INI 文件阅读器在某一种情形下是毫无用处的，那就是当文件的内容来源并不是磁盘的时候。

{{index "object-oriented programming"}}

与之相关的是，有的时候有状态的对象是有用的，甚至是必要的。但是，如果可以用函数来完成任务的话那就使用函数。有些 NPM 上的 INI 文件阅读器提供了一种接口风格，它要求你首先创建一个对象，然后将文件载入这个对象中，最后使用专用的方法来获取结果。这种操作在面向对象的传统中是常见的，并且很糟糕。你必须完成将你的对象经过好些个不同状态的仪式，而不能建立单个函数调用之后直接继续。况且，由于数据现在被包装在一个专用的对象类型里，所有与之交互的代码都必须知晓那种类型，从而制造了不必要的相互依存关系。

通常对新的数据结构的定义是不可避免的 —— 语言标准只提供了一小撮基本的数据结构，而很多类型的数据要比数组或映射更加复杂。但是当数组足以胜任的情况下，就使用数组。

一个略微更复杂的数据结构的例子是来自 [第七章](robot) 的图。在 JavaScript 中，没有一种明显的代表图的方式。在那一章里，我们采用了一个对象，它的属性含有字符串数组 —— 从该节点可以访问的其他节点。 

在 ((NPM)) 上有几个不同的寻路包，但其中没有一个使用了这样的图格式。它们通常允许图边拥有权重，即与之相关的成本或者距离，这在我们的表象中是不可能的。

{{index "Dijkstra, Edsger", pathfinding, "Dijkstra's algorithm", "dijkstrajs package"}}

例如，有一个名为 `dijkstrajs` 的包。有一个与我们的 `findRoute` 函数非常类似的、著名的寻路方法叫做 _Dijkstra 算法_， 该算法是以第一个将其写出来的 Edsger Dijkstra 命名的。`js` 后缀通常被添加到包的名称中，用于说明该包是以 JavaScript 写成的。这个 `dijkstrajs` 包采用了与我们的相似的图格式，但它没有用数组，而是用对象，对象的属性值为数字 —— 边的权重。

因此，如果我们想用这个包，就需要确保我们的图以它所预期的格式被存储。所有的边的权重都是一样的，因为我们简化后的模型将每一条道路的成本视为相同(一趟)。

```
const {find_path} = require("dijkstrajs");

let graph = {};
for (let node of Object.keys(roadGraph)) {
  let edges = graph[node] = {};
  for (let dest of roadGraph[node]) {
    edges[dest] = 1;
  }
}

console.log(find_path(graph, "Post Office", "Cabin"));
// → ["Post Office", "Alice's House", "Cabin"]    邮局，爱丽丝的家， 小屋
```

对谱写来说这可能会是个阻碍 —— 当不同的包使用不同的数据结构来描述相似的事物，整合它们是困难的。因此，你设计时若要考虑谱写性，那就找出别人都在用什么((数据结构))，然后尽量照着做。

## 摘要

模块通过将代码分隔成带有明确的接口以及依赖项的部分来为更大的问题提供结构。接口是一个模块中可以被别的模块看见的部分，而依赖项是它所使用的其他模块。

由于 JavaScript 长久以来没有提供模块系统，CommonJS 系统便建立在其之上。然后在某一时刻它 _终于_ 有了一个内置的系统，以至于现在它与 CommonJS 系统不安地共存着。

包是一段可以被单独分发的代码。NPM 是一个 JavaScript 包的存储库。通过它你可以下载各式各样有用(和无用)的包。

## 练习题

### 模块化机器人

{{index "modular robot (exercise)", module, robot, NPM}}

{{id modular_robot}}

以下绑定是由 [第七章](robot) 的项目创建的： 

```{lang: "text/plain"}
roads
buildGraph
roadGraph
VillageState
runRobot
randomPick
randomRobot
mailRoute
routeRobot
findRoute
goalOrientedRobot
```

如果你想要把这个项目写成一个模块化的程序，你会建立什么模块？哪些模块会依赖其他的模块，而它们的接口又会是什么样的？

哪些部分比较有可能在 NPM 上找到现成的代码？你喜欢使用 NPM 上的包还是喜欢自己动手写？

{{hint

{{index "modular robot (exercise)"}}

以下是我会写出的做法(再次声明，在设计模块时并没有唯一 _正确_ 的方式)：

{{index "dijkstrajs package"}}

用于建立道路的图的代码位于 `graph` 模块中。由于我宁愿用 NPM 上的 `dijkstrajs` 而不用我们自己的寻路代码，我们将以此建立 `dijkstrajs` 所预期的图数据类型。这个模块会导出一个名为 `buildGraph` 的函数。我会让 `buildGraph` 接受一个由两个元素的数组构成的数组，而不是含有连字符的字符串，以减少该模块对输入格式的依赖。

`roads` 模块包含原始的道路数据(`roads` 数组)，以及 `roadGraph` 绑定。这个模块会依赖于 `./graph`，并且导出道路图。

{{index "random-item package"}}

`VillageState` 类位于 `state` 模块中。它会依赖于 `./roads` 模块，因为它需要能确认给定的道路是存在的。它还需要 `randomPick`。由于那只是个三行的函数，我们可以直接把它作为一个内部帮助函数放入 `state` 模块中。但是 `randomRobot` 也会需要 `randomPick`， 所以我们要么再次复制它，或是将其放入单独的模块。由于这个函数正好在 NPM 上以 `random-item` 的包的形式存在，一个好的解决方案是让上述的两个模块都依赖于这个包。我们可以把 `runRobot` 函数也加到这个模块中，因为它比较小而且与状态管理紧密相关。该模块会导出 `VillageState` 类和 `runRobot` 函数。

最后，机器人以及它们所依赖的诸如 `mailRoute` 的值可以被放入 `example-robots` 模块中，该模块依赖于 `./roads`，并且导出机器人函数。为了让 `goalOrientedRobot` 可以完成寻找路线的任务，该模块也依赖于 `dijkstrajs`。

通过将一些工作分流到 ((NPM)) 模块，代码会显得简洁一些。每个单独的模块都做比较简单的事情，而且自身都具有可读性。将代码分化成模块往往也会催生出对程序的设计的进一步改善。在这个情况下，让 `VillageState` 和机器人依赖于特定的道路图好像有点奇怪。将图变为状态构造器的参数，以及让机器人从状态对象里读取图可能是个更好的主意 —— 这样减少了依赖关系(这总是好的)，同时也让不同地图的运行模拟成为可能(简直更好了)。

在我们可以自己写的情况下使用 NPM 包来完成任务真的好吗？原则上来说，是的 —— 对于像寻路函数这样不简单的任务来说，你自己写的话很有可能会犯错误，并且浪费时间。对于诸如 `random-item` 这样微型的函数，自己写足够简单。但是每当你需要它们的时候都手写出来的话，那的确会让你的模块变得乱糟糟的。

然而，你可别低估了 _找_ 到一个合适的 NPM 包所含的工作量有多少。即使你找到了一个包，它有可能效果不如意，或者缺少一些你需要的特性。除此之外，依赖 NPM 包意味着你需要确保它们被安装，你必须将它们分发到你的程序中，并且可能要周期性地给它们升级。

所以我再次声明，这既有好处也有坏处，你可以根据包对你的帮助程度的多少来进行抉择。

hint}}

### 道路模块

{{index "roads module (exercise)"}}

基于 [第七章](robot) 中的示例，写一个 ((CommonJS 模块))。该模块需要包含道路的数组，并导出将道路表示为 `roadGraph` 的图数据结构。它需要依赖 `./graph` 模块，该模块可导出一个用于建造图的 `buildGraph` 函数。这个函数预期一个由包含两个元素的数组构成的数组(道路的起点和终点)。

{{if interactive

```{test: no}
// 添加依赖项和导出项

const roads = [
  "Alice's House-Bob's House",   "Alice's House-Cabin",
  "Alice's House-Post Office",   "Bob's House-Town Hall",
  "Daria's House-Ernie's House", "Daria's House-Town Hall",
  "Ernie's House-Grete's House", "Grete's House-Farm",
  "Grete's House-Shop",          "Marketplace-Farm",
  "Marketplace-Post Office",     "Marketplace-Shop",
  "Marketplace-Town Hall",       "Shop-Town Hall"
];
```

if}}

{{hint

{{index "roads module (exercise)", "destructuring binding", "exports object"}}

由于这是个 ((CommonJS 模块))，你必须得用 `require` 来导入图模块。这被描述为导出一个 `buildGraph` 函数，你可以在其接口对象中用一个解构的 `const` 声明来将这个函数挑出来。

为了导出 `roadGraph`， 你得往 `exports` 对象里添加一个属性。由于 `buildGraph` 接受一个并不精确匹配 `roads` 的数据结构，道路字符串的分离必须在你的模块中进行。

hint}}

### 循环依赖

{{index dependency, "circular dependency", "require function"}}

循环依赖指的是这样一种情况：模块 A 依赖模块 B，而模块 B 直接或间接地依赖模块 A。很多模块系统直接禁止这种操作，因为无论你以哪种顺序对这些模块进行加载，你都无法保证每一个模块的依赖项在运行之前会被加载完成。 

((CommonJS 模块))允许有限形式的循环依赖。只要模块没有替代它们默认的 `exports` 对象，以及它们没有在完成加载之前访问彼此的接口，那么循环依赖是可行的。

[在本章早些时候出现的](modules#require) `require` 函数对这种依赖周期就提供支持。你能不能看出它如何处理周期？如果一个周期中的模块 _的确_ 代替了它默认的 `exports` 对象，会发生什么问题？

{{hint

{{index overriding, "circular dependency", "exports object"}}

诀窍在于，`require` 在它开始加载模块 _之前_ 就将模块放入其缓存中了。这样一来，如果正在运行的时候有任何 `require` 的调用试图加载它，那么该模块是已知的，当前的接口会被返回，而不是开始再次加载该模块(最终会导致堆栈溢出)。

如果一个模块覆盖了它的 `module.exports` 值，在它完成加载之前，所有收到它接口的值的其他模块会保留一个默认的接口对象(很可能是空的)，而不是预期的接口值。

hint}}
