# Program Structure

{{quote {author: "_why", title: "Why's (Poignant) Guide to Ruby", chapter: true}

我炙热的心在我那吹弹可破的皮肤下剧烈跳动着，只有 10cc 的 JavaScript 才能让我冷静下来。（血液毒素对我无效）。天啊，这东西可以让人瞬间冷静。

quote}}

{{index why, "Poignant Guide"}}

{{figure {url: "img/chapter_picture_2.jpg", alt: "Picture of tentacles holding objects", chapter: framed}}}

本章，我们即将接触真正可被称之为_编程_的东西。我们将认识更多 JavaScript 中的指令，从而谱写出自己的诗篇。

## Expressions and statements

{{index grammar, [syntax, expression], [code, "structure of"], grammar, [JavaScript, syntax]}}

[上一章](values)，我们认识了值，且可以通过预算符号得到新的值。这种创建值的方法是 JavaScript 的重要工序。不过这道工序需要在更大的结构下才有用。这就是我们下面的内容。

{{index "literal expression", [parentheses, expression]}}

可以产生值的代码片段叫做_((表达式))_。实际上，每一个值（比如`22`或者`"psychoanalysis"`）都是一个表达式。在括号内的表达式，和由((二元运算符))或者((一元运算符))组成的表达式也都是表达式。

{{index [nesting, "of expressions"], "human language"}}

这证明了基于语言界面的部分优点。表达式可以包含其他表达式，正如人类语言中的句子也是由千万个更简短的句子组成。由此我们可以创建任何复杂计算的表达式。

{{index statement, semicolon, program}}

如果一个表达式对应一个句子片段，那么一个 JavaScript _语句_就对应一个完整的句子。一个程序则是一系列的语句。

{{index [syntax, statement]}}

最简单的语句是一个以分号结尾的表达式。这是一个程序：

```
1;
!false;
```

虽然这是个无用的程序。一个((表达式))可以只是一个可被其他代码使用的值。因为一个((语句))是独立的，因此它只有在改变世界的时候才有意义。它可以是将某样东西显示在屏幕上，或者改变机器内部的状态从而影响到后面的语句。这些改变又被称作_副作用_。上面例子中的语句在产生`1`和`true`后立刻将它们抛弃了。然而并没有在世界上留下任何印象。所以在运行上面程序的时候，什么可视性改变都没有。

{{index "programming style", "automatic semicolon insertion", semicolon}}

有些时候，在 JavaScript 中，你可以省略掉结尾的分号。其他时候，这个分号必须存在，否则下一((行))的代码会被当成本行语句中的一部分。鉴于可以省略分号的规定既复杂又易出错，所以本书中，每一个语句都会以分号结尾。我建议你也一样，至少在你学会没有分号的微妙之前。

## Bindings

{{indexsee variable, binding}}
{{index [syntax, statement], [binding, definition], "side effect", [memory, organization], [state, in binding]}}

一个程序如何储存它的内部状态？它如何记住某些事情？我们见证了如何从老值中得取新值，但是这并没有改变原有的老值，而且新值如果没有立刻使用就会消失。为了获取和保存值，JavaScript 提供了一个叫做_绑定_或者_变量_的东西。

```
let caught = 5 * 5;
```

{{index "let keyword"}}

这又是一种((语句))。这个特殊的(_((关键词))_)`let`表示这个句子将定义一个绑定。它后面跟着变量名。如果我们想立刻给它一个值，就在变量名后加上`=`运算符和一个表达式。

上面的例子创建了一个叫做`caught`的变量，用来储存5和5的乘积。

在变量被定义后，它的名字就可以在((表达式))中使用了。这种表达式的值就是该变量当前的值。比如：

```
let ten = 10;
console.log(ten * ten);
// → 100
```

{{index "= operator", assignment, [binding, assignment]}}

在一个变量等于一个值的时候，这并不意味着该变量永远和这个值绑定在一起。`=`这个运算符可在任意时候将该变量重新绑定给另一个值，从而取消原有的绑定关系。

```
let mood = "light";
console.log(mood);
// → light
mood = "dark";
console.log(mood);
// → dark
```

{{index [binding, "model of"], "tentacle (analogy)"}}

你应该把变量想像成触角，而不是盒子。它们不_包含_值，它们_抓住_值：两个变量可以指向同一个值。一个程序只能访问它能引用到的值。如果你需要记住某件事，你可以创建一个新的触角，或者改变已有的触角去抓住它。

我们在看一个例子。为了记住 Luigi 欠你的钱，你创建了一个变量。在他还给你¥35后，你改变了这个变量的值。

```
let luigisDebt = 140;
luigisDebt = luigisDebt - 35;
console.log(luigisDebt);
// → 105
```

{{index undefined}}

如果你创建了一个没给定义值的变量，这个触角会因为没有东西可抓而置放在空中。这时，他的值是`undefined`。

{{index "let keyword"}}

一个`let`语句可以同时创建许多变量。它们之间必须以逗号区分开来。

```
let one = 1, two = 2;
console.log(one + two);
// → 3
```

与`let`相仿，`var`和`const`也可以用来创建变量。

```
var name = "Ayda";
const greeting = "Hello ";
console.log(greeting + name);
// → Hello Ayda
```

{{index "var keyword"}}

首先，`var`（英文“变量” variable 的缩写）是 JavaScript 在2015年以前创建变量的方法。[下一章](functions)中，我会细讲它和`let`的区别。现在，你只需记得它们大致相同。但是，本书中，我们尽可能避免使用`var`，因为它有些令人困惑的属性。

{{index "const keyword", naming}}

`const`是英文_((常量))_ constant 的拼写。它会创建一个常量，也就是说改值不可以被改变。用名字绑定一个值，以便你以后可以轻松引用。

## Binding names

{{index "underscore character", "dollar sign", [binding, naming]}}

绑定的名字可以是任何单词。数字可以用在绑定的名字中：比如`catch22`。但是名字不可以由数字开头。绑定的名字可以包含`$`或者`_`，但是不可以用其他标点或特殊符号。

{{index [syntax, identifier], "implements (reserved word)", "interface (reserved word)", "package (reserved word)", "private (reserved word)", "protected (reserved word)", "public (reserved word)", "static (reserved word)", "void operator", "yield (reserved word)", "enum (reserved word)", "reserved word", [binding, naming]}}

_((关键词))_，比如`let`，是有特殊意义的单词。它们不可以用来绑定名字。此外还有些被((未来))的 JavaScript “保留使用”的词，也不可以用来绑定名字。关于关键词和被保留词的名单很长：

```{lang: "text/plain"}
break case catch class const continue debugger default
delete do else enum export extends false finally for
function if implements import interface in instanceof let
new package private protected public return static super
switch this throw true try typeof var void while with yield
```

{{index [syntax, error]}}

不要担心背诵。如果在绑定一个名字时，出现了意外的语法错误，检查下你是否试图绑定这个清单中的词。

## The environment

{{index "standard environment", [browser, environment]}}

_((环境))_是在特定时间内存在的绑定及其值的集合。在程序开始时，该环境并不是空的。它永远包括语言((标准))的绑定。更多的时候，它还包括可与周边系统互动的绑定。必须，在浏览器中，有可以和当前网页互动、读取((鼠标))和((键盘))的函数。

## Functions

{{indexsee "application (of functions)", [function, application]}}
{{indexsee "invoking (of functions)", [function, application]}}
{{indexsee "calling (of functions)", [function, application]}}
{{index output, function, [function, application], [browser, environment]}}

默认环境中的许多值都是_((函数)))_。函数是被一个值包裹住的程序。该值可被用来运行这个被包住的函数。比如，在浏览器中，被绑定的`prompt`会显示一个求用户输入的小((对话窗))的函数。它的使用方法如下：

```
prompt("Enter passcode");
```

{{figure {url: "img/prompt.png", alt: "A prompt dialog", width: "8cm"}}}

{{index parameter, [function, application], [parentheses, arguments]}}

执行一个函数又被称为_调用_或_应用_。你可以将括号放在函数表达式后面从而调用一个函数。一般情况下，你会直接用函数本身的名字。括号内的值是提供给函数内的程序的。如上面的例子，`prompt`函数用我们提供给它的字符串作为对话窗的文字。提供给函数的值叫做_((参数))_。不同的函数会有不同类型和数量的参数。

`prompt`函数在现代网络编程中用的不多，因为我们无法改变对话窗的样子。但是作为一个实验，它还是很有用的。

## The console.log function

{{index "JavaScript console", "developer tools", "Node.js", "console.log", output, [browser, environment]}}

至今为止的例子中，我用了`console.log`输出值。大部分 JavaScript 系统（包括所有现代网络浏览器和 Node.js）提供一个`console.log`函数把它的参数写到_某_文字输出设备。在浏览器中，输出的文字会显示在((JavaScript console))。在默认情况下，这是被浏览器隐藏的部分。大部分浏览器中，你可以按 F12，或在 Mac 里，[command]{keyname}-[option]{keyname}-I，将它打开。如果以上方法不工作的话，在菜单中搜索类似开发者工具的选项。

{{if interactive

在运行本书中的例题（或你自己的代码）时，`console.log`的输出会显示在例题后面，而不是浏览器自带的 JavaScript console 里。

```
let x = 30;
console.log("x 的值是", x);
// → x 的值是 30
```

if}}

{{index [object, property], [property, access]}}

尽管绑定的名字不可以包含((点))，`console.log`却有一个。这是因为`console.log`不是一个简单的绑定名字。它其实是一个表达式，读取名为`console`值的`log`属性。[第四章](data#properties)中我们会更具体的介绍这个。

{{id return_values}}
## Return values

{{index [comparison, "of numbers"], "return value", "Math.max function", maximum}}

显示一个对话窗或者在屏幕上写出一段文字是一个_((副作用))_。很多函数有用，因为它们产生的副作用。能产生值的函数不需要副作用也很有用。比如，`Math.max`函数接受若干参数，之后返回最大的那个。

```
console.log(Math.max(2, 4));
// → 4
```

{{index [function, application], minimum, "Math.min function"}}

当一个函数得到一个值时，这叫做_返回_那个值。在 JavaScript 中，任何返回值的东西都叫((表达式))，所以函数可以用来组成更大的表达式。下面的例子中，`Math.min`，与`Math.max`相反的函数，就用在了一个加法表达式中：

```
console.log(Math.min(2, 4) + 100);
// → 102
```

[下一章](functions)会讲如何写属于你的函数。

## Control flow

{{index "execution order", program, "control flow"}}

当你的程序中有许多((语句))时，它们会从上到下依次运行。下面的例子有两个语句。第一个向用户要一个数字。第二个，在第一个运行结束后，显示那个数字的((平方))。

```
let theNumber = Number(prompt("选一个数字"));
console.log("你的数字的平方是" + theNumber * theNumber);
```

{{index [number, "conversion to"], "type coercion", "Number function", "String function", "Boolean function", [Boolean, "conversion to"]}}

函数`Number`把一个值转变成数字。我们需要这个转换，因为`prompt`返回的是字符串，但是我们需要的是数字。此外，姐妹函数`String`和`Boolean`会把值转换成相对应的类型。

这是一个相对简单的直线控制流的示意图：

{{figure {url: "img/controlflow-straight.svg", alt: "Trivial control flow", width: "4cm"}}}

## Conditional execution

{{index Boolean, ["control flow", conditional]}}

不是所以的程序都是一条笔直的大路。有时，我们可能需要创建些分支的路径，让程序通过当前状况自行选择正确的路线。这就是_((条件执行))_。

{{figure {url: "img/controlflow-if.svg", alt: "Conditional control flow",width: "4cm"}}}

{{index [syntax, statement], "Number function", "if keyword"}}

在 JavaScript 中，关键词`if`用来创建条件执行。简单来说，我们想让某些代码在特有前提下运行。比如，只有在用户输入数字时才显示该数字的平方：

```{test: wrap}
let theNumber = Number(prompt("选一个数字"));
if (!Number.isNaN(theNumber)) {
  console.log("你的数字的平方是" +
              theNumber * theNumber);
}
```

根据改写后的代码，如果你输入的是 "parrot"，你将不会得到任何结果。

{{index [parentheses, statement]}}

关键词`if`根据一个布尔值表达式决定运行或跳过一个语句。这个决定性的表达式写在关键词后面，被括号围着。后面跟着需要运行的语句。

{{index "Number.isNaN function"}}

`Number.isNaN`函数是一个标准的 JavaScript 函数，它只有在参数是`NaN`时才会返回`true`。而`Number`函数恰好返回`NaN`，如果它的参数无法代表一个数字。所以，这个条件就是“除非`theNumber`不是一个数字，做这个”。

{{index grouping, "{} (block)", [braces, "block"]}}

这个例子里，`if`后的语句被大括号（`{`和`}`）围着。大括号可以将任意数量的语句组成一个语句，叫_((块语句))_。这个例子中，因为大括号内只有一个语句，所以你也可以将大括号省略掉。不过为了避免思考什么时候需要大括号，大部分 JavaScript 程序员们选择一直用大括号。本书中我们也会如此，除了个别几个一行语句外。

```
if (1 + 1 == 2) console.log("It's true");
// → It's true
```

{{index "else keyword"}}

很多时候，不是只有在一个条件为真时才会运行代码，我们也要处理其他情况。图中的第二个箭头表示这条备用路线。你可以用关键词`else`，和`if`一起，组件两个独立的，可替代的执行路线。

```{test: wrap}
let theNumber = Number(prompt("选一个数字"));
if (!Number.isNaN(theNumber)) {
  console.log("Y你的数字的平方是" +
              theNumber * theNumber);
} else {
  console.log("你为什么没有给我一个数字？");
}
```

{{index ["if keyword", chaining]}}

如果你有多于两个选项，你可以选择用多个`if`/`else`组合。比如：

```
let num = Number(prompt("选一个数字"));

if (num < 10) {
  console.log("小");
} else if (num < 100) {
  console.log("中");
} else {
  console.log("大");
}
```

这个程序会先确认`num`是否比10小。如果是，它会选择第一条路，显示`"小"`后结束运行。如果不是，它会选择`else`那条路，其中包括了第二个`if`。如果第二个条件（`< 100`)是真的，那么这个数字在10和100中间，`"中"`就会显示。如果不是，第二个也是最后一个`else`将会被执行。

这个程序的图形代表如图所示：

{{figure {url: "img/controlflow-nested-if.svg", alt: "Nested if control flow", width: "4cm"}}}

{{id loops}}
## while and do loops

如果有一个程序输出所有0-12中的((偶数))。一种写法如下所示：

```
console.log(0);
console.log(2);
console.log(4);
console.log(6);
console.log(8);
console.log(10);
console.log(12);
```

{{index ["control flow", loop]}}

上面的程序虽然工作，但写程序的意义是让工作_简单化_，而不是复杂化。如果我们需要1000以内的所有偶数，上面的方法就不现实了。我们需要一个可以重复运行一段代码的方法。这种控制流叫做_((循环))_。

{{figure {url: "img/controlflow-loop.svg", alt: "Loop control flow",width: "4cm"}}}

{{index [syntax, statement], "counter variable"}}

循环控制流让我们回到程序中的某个我们到过的点，再在现有的程序状况下，重复运行。如果我们把这个控制流和一个计数的变量组合在一起，我们可以得到下面的程序：

```
let number = 0;
while (number <= 12) {
  console.log(number);
  number = number + 2;
}
// → 0
// → 2
//   … 等等
```

{{index "while loop", Boolean, [parentheses, statement]}}

关键词`while`开始的((语句))是一个循环。`while`后面跟着一个被括号包围的((表达式))，之后是一个类似`if`的语句。循环将一直执行语句，直到表达式的结果被转换成布尔值后为真。

{{index [state, in binding], [binding, as state]}}

这个`数字`变量展现了一个((变量))可以跟进程序进度的方法。每当循环重复一次，`number`都会得到一个比上个值大2的新值。在每次重复前，它会和数字12比较，从而决定该程序是否运行结束。

{{index exponentiation}}

举一个有实用的例子，我们现在可以写一个计算2^10^（2的10次方）的值的程序。我们需要两个变量：一个储存我们的结果，一个记录我们乘以2的次数。循环测试第二个变量是否到达10了，如果没有，继续更新两个变量。

```
let result = 1;
let counter = 0;
while (counter < 10) {
  result = result * 2;
  counter = counter + 1;
}
console.log(result);
// → 1024
```

计数器也可以从`1`开始，检查`<= 10`，但是如[第四章](data#array_indexing)解释的原因，最好习惯从0开始计数。

{{index "loop body", "do loop", ["control flow", loop]}}

一个`do`的循环是和`while`相同的控制结构。除了一点，`do`循环至少执行它的块语句一次，它只有在执行过一次后，才会判断是否需要停止运行。因此，它的条件表达式在块语句的后面。

```
let yourName;
do {
  yourName = prompt("你是谁？");
} while (!yourName);
console.log(yourName);
```

{{index [Boolean, "conversion to"], "! operator"}}

这个程序会强制你输入姓名。它会一直问你，直到得到一个不是空的字符串。`!`运算符会先将值转换成布尔值，在取它的相反值。所有不是`""`的字符串都是`true`。也就是说，这个循环会一直运行到你提供一个不为空的名字。

## Indenting Code

{{index [code, "structure of"], [whitespace, indentation], "programming style"}}

在所有例子中，我都有在大语句中的语句前加空格。这些空格不是必须的，电脑依旧会结束这些程序，即使没有这些空格。就连换((行))也不是必须的。你如果想，完全可以写一行特别长的程序。

在((块语句))中的((缩进))是为了突出代码的结构。代码中，当新块语句出现在另一个块语句中，往往很难发现每个块语句的起点和终点。我喜欢用两个空格给每个新的块语句，当然这个因人而异，有些人喜欢四个，有些人会用((制表符))。重要的是确保每个新的块语句都有同样的缩进。

```
if (false != true) {
  console.log("That makes sense.");
  if (1 < 2) {
    console.log("No surprise there.");
  }
}
```

大部分代码((编译器))[(包括本书中的)]{if interactive}会自动进行缩进。

## for loops

{{index [syntax, statement], "while loop", "counter variable"}}

很多循环遵循`while`例子中展示的格式。首先一个“计数器”变量用来记录循环的进度。之后是`while`循环，一般是一个测试表达式检查计数器是否到达终点。之后，更新计数器，以便记录进度。

{{index "for loop", loop}}

因为这个格式太常见了，JavaScript 和相似的语言提供一个相对简洁易懂的格式，就是`for`循环。

```
for (let number = 0; number <= 12; number = number + 2) {
  console.log(number);
}
// → 0
// → 2
//   … 等等
```

{{index ["control flow", loop], state}}

这个程序和[前面](program_structure#loops)那个输出偶数的程序完全相等。唯一的改变是所有有关循环状态的((语句))被组织起来，放在`for`后面了。

{{index [binding, as state], [parentheses, statement]}}

关键词`for`后面的括号里必须有两个((分号))。第一个分号前的部分_初始化_这个循环，一般用来定义变量。第二个((表达式))用来_检查_这个循环是否要继续。最后一个部分在每次迭代后_更新_这个循环的状态。一般来讲，这比`while`要简洁明了许多。

{{index exponentiation}}

以下是用`for`代替`while`重写的计算2^10^的代码：

```{test: wrap}
let result = 1;
for (let counter = 0; counter < 10; counter = counter + 1) {
  result = result * 2;
}
console.log(result);
// → 1024
```

## Breaking Out of a Loop

{{index [loop, "termination of"], "break keyword"}}

循环条件返回`假`并不是唯一可以终结循环的方法。还有一个特殊的语句，叫做`break`，也有立刻结束围绕它的循环功能。

这个程序运用了`break`语句。它找到第一个大于或等于20，同时可以被7整除的数字。

```
for (let current = 20; ; current = current + 1) {
  if (current % 7 == 0) {
    console.log(current);
    break;
  }
}
// → 21
```

{{index "remainder operator", "% operator"}}

用求余（`%`）符号可以轻而易举的测试一个数字能否被另一个数整除。如果可以，它们的余数会是零。

{{index "for loop"}}

上面例子中的`for`没有检查结束循环的部分。也就是说，这个循环会一直继续下去，除非它包含的`break`语句被执行了。

如果把`break`语句删除掉，或者不小心写了一个永远返回`true`的结束条件，你的程序将陷入_((无限循环))_。一个陷入无限循环的程序永远不会终结，这不是件好事。

{{if interactive

如果你在本书中的例题中陷入一个无限循环，你会在几秒钟后被问到是否要停止脚本。如果这个不工作，你只能关闭该页。有些浏览器会为了恢复而关闭整个浏览器。

if}}

{{index "continue keyword"}}

关键词`continue`从改变循环的进程上讲，和`break`相似，当在循环中遇到`continue`时，控制将跳出主体并继续循环下一个迭代。

## Updating bindings succinctly

{{index assignment, "+= operator", "-= operator", "/= operator", "*= operator", [state, in binding], "side effect"}}

尤其在循环时，一个程序通常需要根据变量原先的值“更新”变量。

```{test: no}
counter = counter + 1;
```

JavaScript 对此提供一个捷径：

```{test: no}
counter += 1;
```

其他运算符也有类似的捷径，比如`result *= 2`是将`result`加倍，或者`counter -= 1`是倒数。

如此我们可以更加简化我们的数数代码：

```
for (let number = 0; number <= 12; number += 2) {
  console.log(number);
}
```

{{index "++ operator", "-- operator"}}

对`counter += 1`和`counter -= 1`而言，还有更简介的写法：`counter++`和`counter--`。

## Dispatching on a value with switch

{{index [syntax, statement], "conditional execution", dispatch, ["if keyword", chaining]}}

下面的代码十分常见：

```{test: no}
if (x == "value1") action1();
else if (x == "value2") action2();
else if (x == "value3") action3();
else defaultAction();
```

{{index "colon character", "switch keyword"}}

还有一个名为`switch`的结构，用来更直接的表达这种调度。可惜，JavaScript 对此的句法（源自 C/Java）略有尴尬——一连串`if`语句可能更好理解。如下所示：

```
switch (prompt("天气如何？")) {
  case "下雨":
    console.log("记得带伞。");
    break;
  case "晴天":
    console.log("轻装出门");
  case "乌云":
    console.log("出门散步");
    break;
  default:
    console.log("未知的天气！");
    break;
}
```

{{index fallthrough, "break keyword", "case keyword", "default keyword"}}

在`switch`块语句中，你可以有任意数量的`case`标签。程序会从符合`switch`值的标签开始执行，或者`default`如果没有符合的标签。它会继续执行，即使遇到了其他的标签，直到遇见一个`break`语句。有些时候，比如例子中的`"sunny"`，标签之间可以分享部分代码（乌云和晴天都建议出门散步）。但要注意——很容易忘记`break`，导致程序会运行你不希望运行的代码。

## Capitalization

{{index capitalization, [binding, naming], [whitespace, syntax]}}

变量名不可以有空格，但多个单词通常可以更清晰的描述所代表的变量。以下差不多是用多个单词组成的变量名的写法：

```{lang: null}
fuzzylittleturtle
fuzzy_little_turtle
FuzzyLittleTurtle
fuzzyLittleTurtle
```

{{index "camel case", "programming style", "underscore character"}}

第一个读起来会很吃力。我比较喜欢看下划线组成的，但是打字比较麻烦。((标准))的 JavaScript 函数，和大部分 JavaScript 程序员，用最后一个写法：除了第一个单词外，每个单词的第一个字母都大写。适应这种小事很容易，而且混合命名式的代码读起来比较乱，所以我们遵守这个((约定))。

{{index "Number function", constructor}}

个别时候，比如`Number`函数，变量的第一个字母也是大写。这是为了标明该函数是一个构造函数。[第六章](object#constructors)将详细介绍构造函数。现在，更重要的是不要被这明显不一致性困扰。

## Comments

{{index readability}}

通常，原代码无法将你的程序想表达的所有信息告知读者，或者它会以读着无法理解的加密模式表达出来。其他时候，你可能想在程序中加入写相关的想法。这就是_((注释))_的目的。

{{index "slash character", "line comment"}}

注释是程序里的一段文字，但全部被电脑无视。JavaScript 有两种写注释的方法。写一行的注释，你可以用两个斜杠字符（`//`）后面跟着注释文字。

```{test: no}
let accountBalance = calculateBalance(account);
// 春天在哪里呀 春天在哪里
accountBalance.adjust();
// 春天在那青翠的山林里
let report = new Report();
// 这里有红花呀 这里有绿草
addToReport(accountBalance, report);
// 还有那会唱歌的小黄鹂
```

{{index "block comment"}}

`//`注释只到该行结束。而被`/*`和`*/`包围的文字会全部被无视掉，不管有多少行。这对于添加大量有关文件或程序的信息块很有帮助。

```
/*
  我在一个老旧的笔记本后面第一次看到这个数字。
  那之后，它经常出现，
  在电话号码中，还有我购买的产品序列号中。
  很明显，我们有缘。
  所以我决定保留它。
*/
const myNumber = 11213;
```

## Summary

你现在直到程序是由语句组成的，这些语句本身可以包含更多语句。语句通常包括表达式，而表达式也可由更小的表达式组成。

逐次将语句排列起来，你就会得到一个从上运行到下的程序。你可以对控制流加入干扰（`if`，`else`，和`switch`）和循环（`while`，`do`，和`for`）语句。

变量可以用来绑定数据，它们对跟随程序状态很有帮助。环境是一套以定义的变量。JavaScript 永远会有一系列有用的标准绑定。

函数是特殊的值，可以用来封装部分程序。你可以通过`函数名(参数1, 参数2)`来调用它们。这是一个表达式的函数调用，可能会产生一个值。

## Exercises

{{index exercises}}

如果不确定如何验证习题的解，可以参考[前言](intro)。

每个习题都以描述问题开始。细读这段描述，尽可能解开这些习题。你如果遇到问题，可以参考[习题后]{if interactive}[书后]{if book}提示。本身不包含完整的答案，但是你可以在网上[_https://eloquentjavascript.net/code_](https://eloquentjavascript.net/code#2)找到它们。你如果想通过习题学到些什么，我建议在你完全解答完后在去参考答案。至少在你努力尝试后。

### Looping a triangle

{{index "triangle (exercise)"}}

写一个((循环))，它会调用`console.log`七次，输出下面的三角形：

```{lang: null}
#
##
###
####
#####
######
#######
```

{{index [string, length]}}

你可以通过在字符串后面添加`.length`得到该字符串的长度。

```
let abc = "abc";
console.log(abc.length);
// → 3
```

{{if interactive

大部分习题自带一小部分代码，你可以通过更改它来解答该题。记住你需要点击代码块去编辑它们。
```
// 你的代码。
```
if}}

{{hint

{{index "triangle (exercise)"}}

你可以先写一个输出数字1至7的代码。你可以编辑本章前面提到`for`循环时提供的[输出偶数例题](program_structure#loops)。

现在考虑将数字换成同等数量的哈希字符串。你可以通过加1（`+= 1`）从1到2。你可以通过加字符（`+= "#"`）从`"#"`到`"##"`。如此，你到答案会和输出数字的代码极为相似。

hint}}

### FizzBuzz

{{index "FizzBuzz (exercise)", loop, "conditional execution"}}

写一个程序，通过`console.log`输出所有从1到100的数字，除了两个例外情况。如果数字能被3整除，用`"Fizz"`代替该数字。如果数字能被5整除（但不能被3整除），返回`"Buzz"`。

当上面的程序工作后，编辑你的程序，让它输出`"FizzBuzz"`，如果这个数字可以同时被3和5整除（依旧返回`"Fizz"`或者`"Buzz"`如果只能被其中一个整除）。

（这其实是一个声称淘汰许多程序员候选人的((面试题))。所以你如果解开它了，你的市场劳动力价格刚刚升值。）

{{if interactive
```
// 你的代码。
```
if}}

{{hint

{{index "FizzBuzz (exercise)", "remainder operator", "% operator"}}

遍历数字很明显是一个循环，而选择输出的信息是条件执行。记得用求余（`%`）符合验证一个数字是否能被另一数字整除（余数为零）。

第一个版本中，每个数字只有三个可能的结果。所以你需要创建一个`if`/`else if`/`else`。

{{index "|| operator", ["if keyword", chaining]}}

第二个版本有一个明显和一个聪明的解。明显的解是再加一个条件“分支”来精确判断给定条件。聪明的解是建一个包含一个或多个单词的输出，打印这个单词或数字，如果没有单词的话。可以善用`||`运算符。

hint}}

### Chessboard

{{index "chessboard (exercise)", loop, [nesting, "of loops"], "newline character"}}

写一个程序，创建一个 8x8 网格的字符串，用换行符分隔行。网格的每个位置要么是一个空格，要么是一个“#”字符。结果应该形成一个国际象棋盘。

把这个字符串放入`console.log`，会得到下面的结果：

```{lang: null}
 # # # #
# # # # 
 # # # #
# # # # 
 # # # #
# # # # 
 # # # #
# # # # 
```

当你的程序生成上面的图案时，定一个变量`size = 8`后，编辑这个程序，使它支持任意`size`，返回给定宽和高的网格。

{{if interactive
```
// 你的代码。
```
if}}

{{hint

{{index "chess board (exercise)"}}

你可以先创建一个空字符串（`""`），之后反复添加字符。换行符是`"\n"`。

{{index [nesting, "of loops"], [braces, "block"]}}

处理((二维))需要一个((循环))在另一个循环里。把大括号放在两个循环的块语句外，好轻松看到它们的起始和终点。尽量正确的对这些块语句进行缩进。循环的顺序必须和创建字符串的顺序一致（逐行，从左到右，从上到下）。所以外围的循环负责换行，里面的循环负责每行的字符。

{{index "counter variable", "remainder operator", "% operator"}}

你需要两个变量来记录程序进程。你需要通过测试这两个变量的和是不是偶数（`% 2`），来判断每个位置应该是空格还是哈希符合。

换行必须通过添加换行符来完成。所以要在执行完内循环，但是在外循环中进行。

hint}}
