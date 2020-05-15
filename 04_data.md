{{meta {load_files: ["code/journal.js", "code/chapter/04_data.js"], zip: "node/html"}}}

# Data Structures: Objects and Arrays

{{quote {author: "Charles Babbage", title: "Passages from the Life of a Philosopher (1864)", chapter: true}

我被问过两次，“Mr. Babbage 如果你对机器提供了错误的数据，它会返回正确的结果么？”我实在无法理解这个问题背后的思路。

quote}}

{{index "Babbage, Charles"}}

{{figure {url: "img/chapter_picture_4.jpg", alt: "Picture of a weresquirrel", chapter: framed}}}

{{index object, "data structure"}}

数字、布尔值、和字符串是((数据))结构中的原子。许多信息类型都需要多种不同的原子。_对象_可以组合多个值，包括对象本身，形成更复杂的结构。

截止到目前，我们写的所有程序都仅限于简单的数据类型。本章将探索基本的数据结构。完成后，你将会书写更有用的程序。

本章会应用新的概念，循序渐进的解锁一个较现实的程序例题。例题中的代码会在原有的基础上逐渐完善。

{{if book

网上的有本书的((沙盒))([_https://eloquentjavascript.net/code_](https://eloquentjavascript.net/code))，提供一个可以运行本书例题的环境。你如果想用其他环境的话，请先从沙盒中下载本章所需的代码。

if}}

## The weresquirrel

{{index "weresquirrel example", lycanthropy}}

时不时的，一般在晚上 8 - 10点，((Jacque))发现他会变成一个有尾巴的毛绒小松鼠。

尽管 Jacque 万幸他不会变成狼人。毕竟小松鼠比狼人安全多了。不过与其担心他不小心吃到他的邻居，他现在需要提防被他邻居的猫吃掉。在他两次赤身从危险的橡树枝上醒来后，他开始每晚锁紧门窗，且在卧室地板上放置许多核桃。

虽然猫和树的问题解决了，Jacques 依旧希望可以彻底解决这个变身的问题。这个没有规律的变身让他怀疑可能是由某个东西引起的。起初他怀疑是因为他离橡树过近，但是远离橡树后并没有解决这个问题。

{{index journal}}

于是他开始改用更科学的方法，每天都精准的记录下一切活动，以及他有没有变身。他希望由此可以缩小引发变身的因素范围。

首先，他需要一个数据结构来储存这些信息。

## Data sets

{{index ["data structure", collection], [memory, organization]}}

首先我们要在电脑内存中找到可以代表大量数据的方法。比如，我们需要一个包含 2，3，5，7，11 的数字((集合))。

{{index string}}

因为字符串没有字数限制，我们可以巧用字符串来储存这些数据`"2 3 5 7 11"`。但是这非常奇怪。你不仅要提取，还需要把它们转换回数字。

{{index [array, creation], "[] (array)"}}

好在 JavaScript 提供一种专门储存一序列值的数据类型。这就是_数组_，是一系列在((中括号))内，被逗号分开的值。

```
let listOfNumbers = [2, 3, 5, 7, 11];
console.log(listOfNumbers[2]);
// → 5
console.log(listOfNumbers[0]);
// → 2
console.log(listOfNumbers[2 - 1]);
// → 3
```

{{index "[] (subscript)", [array, indexing]}}

提取数组中的一个值也是用((中括号))表示。一个表达式后面紧跟着一组中括号，中间包含着另一个表达式。这会读取左边表达式中对应括号中_((索引))_表达式的值。

{{id array_indexing}}
{{index "zero-based counting"}}

数组的第一个索引是 0，不是 1。所以第一个能被提取的值是 `listOfNumbers[0]`。从零数起是科技的一个习俗，虽然理论上说得通，但的确需要一段适应期。将索引想像成需要跳过的数量，从数组的头开始数起。

{{id properties}}

## Properties

{{index "Math object", "Math.max function", ["length property", "for string"], [object, property], "period character", [property, access]}}

前面的章节中，我们见过一些可疑的表达式，比如 `myString.length`（得到字符串的长度）和 `Math.max`（最大值函数）。这些表达式都在访问一些值的_属性_。第一个例子中，我们在访问 `myString`中的 `length`属性。第二个例子中，我们在访问 `Math`对象（一组和数学相关的函数和常数）中名为 `max` 的属性。

{{index [property, access], null, undefined}}

除了 `null` 和 `undefined` 外的所有 JavaScript 值都有属性。你如果试图访问它们俩的属性，你会得到错误提示。

```{test: no}
null.length;
// → TypeError: null has no properties
```

{{indexsee "dot character", "period character"}}
{{index "[] (subscript)", "period character", "square brackets", "computed property", [property, access]}}

JavaScript 中两种常用访问属性的方法是点和中括号。`value.x` 和 `value[x]` 都可以访问 `value` 的一个属性（不一定是相等）。区别在于如何解读 `x`。如果用点，那么点后面的词就是被访问的属性名。如果用中括号，其包含的表达式在被评估后得到的值才是被访问的属性名。所以 `value.x` 得到的是名为 `value` 中名为 `x` 的属性，而 `value[x]` 会先评估表达式 `x`在通过那个值（转换成字符串）得到将被访问的属性名。

所以你如果直到你要访问的属性是 _color_ 的话，直接叫 `value.color`。如果你想访问的属性名绑定在变量 `i` 上，就需要叫 `value[i]`。属性的名字都是字符串。它们可以是任何字符串，不过点只适用于类似变量名的属性名。如果你需要访问属性 _2_ 或者 _John Doe_，那么你必须用中括号：`value[2]` 或者 `value["John Doe"]`。

((数组))中的元素是以数组属性的方式储存的，数字则是属性名。因为点无法读取数字属性，而且通常习惯用变量代表索引，所以数字属性必须用中括号读取。

{{index ["length property", "for array"], [array, "length of"]}}

数组的`length`属性返回这个数组所含元素数量。这是一个有效的变量名，而且我们已知这个名字，所以可以直接通过 `array.length` 获取数组的元素数量。毕竟这个写法比 `array["length"]` 简介多了。

{{id methods}}

## Methods

{{index [function, "as property"], method, string}}

除了 `length`属性外，字符串和数组还有其他属性。

```
let doh = "Doh";
console.log(typeof doh.toUpperCase);
// → function
console.log(doh.toUpperCase());
// → DOH
```

{{index "case conversion", "toUpperCase method", "toLowerCase method"}}

每个字符串都有一个 `toUpperCase` 属性。它会返回一个复制的字符串，其每个字母都被转换成大写。同理，也有一个 `toLowerCase` 属性的存在。

{{index "this binding"}}

有趣的是，尽管 `toUpperCase` 没有任何参数，它依旧知道可以调用字符串本身的值 `"Doh"`。这背后的原理会在[第六章](object#obj_methods)揭晓。

函数属性通常被叫做值的_方法_。比如 `toUpperCase` 是字符串的一个方法。

{{id array_methods}}

下例展现了数组的两种方法：

```
let sequence = [1, 2, 3];
sequence.push(4);
sequence.push(5);
console.log(sequence);
// → [1, 2, 3, 4, 5]
console.log(sequence.pop());
// → 5
console.log(sequence);
// → [1, 2, 3, 4]
```

{{index collection, array, "push method", "pop method"}}

`push`方法在数组的后面添加新值，`pop`方法则全然相反，移除且返回数组最后的值。

{{index ["data structure", stack]}}

这些看似愚蠢的名字是_((堆栈))_的传统操作术语。在编程中，堆栈是一个以后进先出的顺序添加（push）和提取（pop）值的数据结构。这种编程中非常常见。你也许还记得[上一章](functions#stack)提到的((调用栈))，就是一个同原理的实例。

## Objects

{{index journal, "weresquirrel example", array, record}}

回到小松鼠的问题上。一组日志可以以数组的形式储存。不过，每篇日志不仅只有数字和字符串，它还包括一系列的活动和一个表示 Jacque 是否变身成小松鼠的布尔值。最好可以把这些数据都组合成一个值，在把这些组合的值统一储存在一个日志数组中。

{{index [syntax, object], [property, definition], [braces, object], "{} (object)"}}

_((对象))_的值是任意属性集。创建对象的一种方法是通过大括号表达式。

```
let day1 = {
  squirrel: false,
  events: ["work", "touched tree", "pizza", "running"]
};
console.log(day1.squirrel);
// → false
console.log(day1.wolf);
// → undefined
day1.wolf = false;
console.log(day1.wolf);
// → false
```

{{index [quoting, "of object properties"], "colon character"}}

在大括号中，有一系列被逗号分开的属性。每个属性都有一个名字，后面跟着一个分号和一个值。为了更好的阅读，一个对象通常会写多行，且有缩进，就如上例所示。如果属性的名字不是有效的变量名或数字，则必须用引号围绕。

```
let descriptions = {
  work: "Went to work",
  "touched tree": "Touched a tree"
};
```

{{index [braces, object]}}

因此大括号在 JavaScript 中有 _2_ 种意思。在((语句))的开头，它们用来表示一段((语句块))。另一方面，它们用来表示对象。不过混淆两者的问题不大，因为很少以对象开始一段语句块。

{{index undefined}}

调用一个不存在的属性会得到 `undefined`。

{{index [property, assignment], mutability, "= operator"}}

可以通过 `=` 给属性一个新的值。如果该属性存在的话，就会改写这个原有的值。否则会创建一个新的属性。

{{index "tentacle (analogy)", [property, "model of"], [binding, "model of"]}}

回顾绑定变量的触角模型，属性绑定有着相同的原理。它们_抓住_值，但是其他的变量和属性可以抓去相同的值。你可以把对象当成一个有无数触角的八抓鱼，每个触角都印有一个名字。

{{index "delete operator", [property, deletion]}}

`delete`运算符可以砍掉该八抓鱼的触角。这是一个一元运算符。当用在一个对象属性上时，该属性就会从该对象中移除。不过这不是一个常用的运算符。

```
let anObject = {left: 1, right: 2};
console.log(anObject.left);
// → 1
delete anObject.left;
console.log(anObject.left);
// → undefined
console.log("left" in anObject);
// → false
console.log("right" in anObject);
// → true
```

{{index "in operator", [property, "testing for"], object}}

`in`二元运算符，当用在一个字符串和一个对象上时，会告诉你这个对象是否有以该字符串命名的属性。把一个属性定义为 `undefined` 和直接删除该属性的区别在于，`undefined` 属性依旧存在于该对象中（只是没有任何值），而删除后的对象中找不到这个属性（所以 `in` 会返回 `false`）。

{{index "Object.keys function"}}

可以通过 `Object.keys` 函数来知道一个对象中有什么属性。它需要一个对象参数，会返回一个以该对象属性名组成的字符串数组。

```
console.log(Object.keys({x: 0, y: 0, z: 2}));
// → ["x", "y", "z"]
```

还有一个 `Object.assign` 函数会将一个对象的所有属性复制到另一个对象中。

```
let objectA = {a: 1, b: 2};
Object.assign(objectA, {b: 3, c: 4});
console.log(objectA);
// → {a: 1, b: 3, c: 4}
```

{{index array, collection}}

数组其实就是一个储存一组物件的对象。如果你评估 `typeof []`，你会得到 `"object"`。你可以把数组想像成一个长而平的八抓鱼，它所有的触角都有序的，按数字排在一起。

{{index journal, "weresquirrel example"}}

我们用一个对象数组来储存 Jacque 的日志。

```{test: wrap}
let journal = [
  {events: ["work", "touched tree", "pizza",
            "running", "television"],
   squirrel: false},
  {events: ["work", "ice cream", "cauliflower",
            "lasagna", "touched tree", "brushed teeth"],
   squirrel: false},
  {events: ["weekend", "cycling", "break", "peanuts",
            "beer"],
   squirrel: true},
  /* and so on... */
];
```

## Mutability

我们很快就会开始真正的编程了。不过那之前，还有一个需要理解的理论。

{{index mutability, "side effect", number, string, Boolean, [object, mutability]}}

我们已知对象的值是可以被改写的。前面章节中介绍的值，比如数字、字符串你、布尔值，都是_((不变))_的：这些类型的值是不可以被改写的。你可以将他们组合起来从而得到新的值，但当你定义一个字符串的值时，它将永远存有该值。如果你有一个值为 `"cat"` 的字符串，其他代码将无法改变该字符串的任意字母，让它变成 `"rat"`。

对象则完全不同。你_可以_改变它们的属性，使一个对象的值在不同时期有所不同。

{{index [object, identity], identity, [memory, organization], mutability}}

当我们有两个数字时，120 和 120，我们认为它们完全相等，不管它们是否共享同一个内存空间。对对象而言，两个参照点指向同一个对象或两个不同的对象是有区别的。如下：

```
let object1 = {value: 10};
let object2 = object1;
let object3 = {value: 10};

console.log(object1 == object2);
// → true
console.log(object1 == object3);
// → false

object1.value = 15;
console.log(object2.value);
// → 15
console.log(object3.value);
// → 10
```

{{index "tentacle (analogy)", [binding, "model of"]}}

因为 `object1` 和 `objects2` 绑定同一个对象，所以当 `object1` 被改变时，`object2` 也被改变了。它们拥有相同的_身份_。`object3` 被绑定在另一个对象上，尽管它们起始的值相同，但是它们却有不同的结局。

{{index "const keyword", "let keyword", [binding, "as state"]}}

变量可以是常数也可以被改变，这和它们本身的值的属性无关。尽管数字值不可改变，你依旧可以用 `let` 记录一个数字变量，只要改变它的绑定就好。同理，尽管 `const` 定义的对象本身不可以被改变（绑定其他值），但是它的内容可以被改变。

```{test: no}
const score = {visitors: 0, home: 0};
// This is okay
score.visitors = 1;
// This isn't allowed
score = {visitors: 1, home: 1};
```

{{index "== operator", [comparison, "of objects"], "deep comparison"}}

在 JavaScript 中，当你用 `==` 来对比对象时，它对比的是该对象的身份：如果两个对象的值相等，就会返回 `true`。对比不同的对象会得到 `false`，尽管它们的属性相同。在 JavaScript 中没有所谓的“深入”对比，就是对比对象的内容。但你可以自己写一个（本章末的一个[习题](data#exercise_deep_compare)）。

## The lycanthrope's log

{{index "weresquirrel example", lycanthropy, "addEntry function"}}

Jacque 开启了他的 JavaScript 编译器，并搭建了所需记录他((日志))的环境。

```{includeCode: true}
let journal = [];

function addEntry(events, squirrel) {
  journal.push({events, squirrel});
}
```

{{index [braces, object], "{} (object)", [property, definition]}}

注意加入日志的对象看似有些奇怪。它只有一个属性名，而不是定义一个类似 `events: events` 的属性。这是一个简介的写法，如果属性的名字绑定的是一个同名的变量，而不是一个定义的值，该属性的值会从和属性同名的变量中提取。

此后，每晚 10点，偶尔隔天早上，当 Jacque 从他的书架上下来后，Jacque 会记录过去的一天。

```
addEntry(["work", "touched tree", "pizza", "running",
          "television"], false);
addEntry(["work", "ice cream", "cauliflower", "lasagna",
          "touched tree", "brushed teeth"], false);
addEntry(["weekend", "cycling", "break", "peanuts",
          "beer"], true);
```

当他记录足够多的数据后，他打算通过统计学找寻可能促使他变身的活动。

{{index correlation}}

_相关性_用来测量统计变量间的依赖性。一个统计变量和编程中的变量不完全相等。在统计学中，通常会有一组测量，而每个测量的变量都会被记录下来。变量中的相关性通常以 -1 到 1 中的一个数字表示。0 的相关性表示这些变量没有关联。1 的相关性表示这些变量完全相关：你知道一个，就会知道其他。-1 的相关性表示这些变量以相反的关系相连：如果其中一个是真的，另一个则是假的。

{{index "phi coefficient"}}

计算两个布尔值的相关度量，我们可以用 _phi 系数_（_ϕ_）。这是一个公式，它的输入是一个含有观察到的不同组合的变量的((频率表))。它的输入是一个 -1 到 1 的数字，形容这个相关性。

我们可以将吃((pizza))放入这个频率表中，每个数字都代表该组合在测量中发生的次数：

{{figure {url: "img/pizza-squirrel.svg", alt: "Eating pizza versus turning into a squirrel", width: "7cm"}}}

如果这个频率表是 _n_ 的话，我们可以通过下面的公式得出 _ϕ_：

{{if html

<div>
<table style="border-collapse: collapse; margin-left: 1em;"><tr>
  <td style="vertical-align: middle"><em>ϕ</em> =</td>
  <td style="padding-left: .5em">
    <div style="border-bottom: 1px solid black; padding: 0 7px;"><em>n</em><sub>11</sub><em>n</em><sub>00</sub> −
      <em>n</em><sub>10</sub><em>n</em><sub>01</sub></div>
    <div style="padding: 0 7px;">√<span style="border-top: 1px solid black; position: relative; top: 2px;">
      <span style="position: relative; top: -4px"><em>n</em><sub>1•</sub><em>n</em><sub>0•</sub><em>n</em><sub>•1</sub><em>n</em><sub>•0</sub></span>
    </span></div>
  </td>
</tr></table>
</div>

if}}

{{if tex

[\begin{equation}\varphi = \frac{n_{11}n_{00}-n_{10}n_{01}}{\sqrt{n_{1\bullet}n_{0\bullet}n_{\bullet1}n_{\bullet0}}}\end{equation}]{latex}

if}}

（此时你放下书本，开始回忆起高一的数学。等等！我不想给你一堆隐喻。这只是一个公式。而且我们只需要把这个公式转换成 JavaScript。）

符合 [_n_~01~]{if html}[[$n_{01}$]{latex}]{if tex} 表示测量次数。第一次变量（变松鼠）是假的（0），第二次变量（pizza）是真的（1）。在 pizza 表格中，[_n_~01~]{if html}[[$n_{01}$]{latex}]{if tex} 是 9.

值 [_n_~1•~]{if html}[[$n_{1\bullet}$]{latex}]{if tex} 表示第一个变量为真的情况下的测量总和。在 pizza 表格中，这个和是 5。同理，[_n_~•0~]{if html}[[$n_{\bullet0}$]{latex}]{if tex} 表示第二个变量为假的测量综合。

{{index correlation, "phi coefficient"}}

所以该 pizza 表格中，分数线上面的部分（分子）是 1×76−4×9 = 40，而下面的部分（分母）是 5×85×10×80 的平方根，或者[√340000]{if html}[[$\sqrt{340000}$]{latex}]{if tex}。得到的结果是 _ϕ_ ≈ 0.069，非常不可能。所以吃((pizza))和变身无关。

## Computing correlation

{{index [array, "as table"], [nesting, "of arrays"]}}

在 JavaScript 中，我们可以用一个四个元素的数组（`[76, 9, 4, 1]`）来表示一个 2x2 的((表格))。我们也可以用其他方式，比如一个包含两个元素数组的数组（`[[76, 9], [4, 1]]`）或者一个属性为`"11"`，`"01"`的对象。不过第一个数组更加简洁明了。我们把数组的索引定义为两位的二进制，最左边（最有效）的数字表示松鼠变量，最右边（最不有效）的数字表示事件变量。比如二进制`10`表示 Jacque 变成了松鼠，但是他并没有做这个事件（如吃 pizza）。这总共发生了4次。因为二进制`10`是十进制中的 2，我们把这个数字（4）存放在数组索引2的位置上。

{{index "phi coefficient", "phi function"}}

{{id phi_function}}

下面的函数计算上述数组中的 _ϕ_ 系数：

```{includeCode: strip_log, test: clip}
function phi(table) {
  return (table[3] * table[0] - table[2] * table[1]) /
    Math.sqrt((table[2] + table[3]) *
              (table[0] + table[1]) *
              (table[1] + table[3]) *
              (table[0] + table[2]));
}

console.log(phi([76, 9, 4, 1]));
// → 0.068599434
```

{{index "square root", "Math.sqrt function"}}

这是把 _ϕ_ 公式直译转换成 JavaScript。`Math.sqrt` 是平方根函数，由 JavaScript 中的 `Math`对象提供。我们需要把表格中的两个数据相加，从而得到 [n~1•~]{if html}[[$n_{1\bullet}$]{latex}]{if tex} 因为我们的数据结构并没有直接储存行和列的和

{{index "JOURNAL data set"}}

Jacque 记录了三个月的日志。最后的((数据集))在本章[([_https://eloquentjavascript.net/code#4_](https://eloquentjavascript.net/code#4))]{if book}的[代码沙盒](https://eloquentjavascript.net/code#4)中一个叫 `JOURNAL` 的变量里。你可以下载这个[文件](https://eloquentjavascript.net/code/journal.js)。

{{index "tableFor function"}}

在日志里，从一个 2x2的((表格))中提取一个指定的活动，我们必须循环所有的数据，统计和变身松鼠相关的事件发生的次数。

```{includeCode: strip_log}
function tableFor(event, journal) {
  let table = [0, 0, 0, 0];
  for (let i = 0; i < journal.length; i++) {
    let entry = journal[i], index = 0;
    if (entry.events.includes(event)) index += 1;
    if (entry.squirrel) index += 2;
    table[index] += 1;
  }
  return table;
}

console.log(tableFor("pizza", JOURNAL));
// → [76, 9, 4, 1]
```

{{index [array, searching], "includes method"}}

数组有一个 `includes`方法，可以检查一个值是否存在于该数组中。这个函数用此方法来确保某个事件在指定的一天内发生过。

{{index [array, indexing]}}

`tableFor` 中的循环通过检验事件和变身松鼠的关系来计算出每个日志数据应该属于表格的哪一栏中。该循环在表格中正确的那栏里加一。

我们现在可以计算出个体相关性了。如今只剩下找到每个事件的相关性，好检查有没有可疑事件。

{{id for_of_loop}}

## Array loops

{{index "for loop", loop, [array, iteration]}}

在 `tableFor` 函数中，有以下循环：

```
for (let i = 0; i < JOURNAL.length; i++) {
  let entry = JOURNAL[i];
  // 对该 entry 做些什么
}
```

这种循环在传统对 JavaScript 中很常见：一个元素一个元素的走完真个数组。为此，你需要一个计数器，从0到数组的长度，之后依次读取每个元素。

在新的 JavaScript 中，有个更简短的循环方法：

```
for (let entry of JOURNAL) {
  console.log(`${entry.events.length} events.`);
}
```

{{index "for/of loop"}}

类似上面的这种用一个在变量定义后根一个 `of` 词的 `for` 循环，它会把 `of` 后的值从头到尾走一遍。这个方法不仅适用于数组，同时也适用于字符串和其他一些数据结构。我们在[第六章](object)中会深入探讨其原理。

{{id analysis}}

## The final analysis

{{index journal, "weresquirrel example", "journalEvents function"}}

我们需要计算每一个事件的相关性。首先，我们需要_找到_所有类型的事件。

{{index "includes method", "push method"}}

```{includeCode: "strip_log"}
function journalEvents(journal) {
  let events = [];
  for (let entry of journal) {
    for (let event of entry.events) {
      if (!events.includes(event)) {
        events.push(event);
      }
    }
  }
  return events;
}

console.log(journalEvents(JOURNAL));
// → ["carrot", "exercise", "weekend", "bread", …]
```

上面的函数走过所有的事件，把任何新的事件都添加到 `events`数组中，从而得到所有类型的事件。

通过这个数组，我们可以得出所有的相关性。

```{test: no}
for (let event of journalEvents(JOURNAL)) {
  console.log(event + ":", phi(tableFor(event, JOURNAL)));
}
// → carrot:   0.0140970969
// → exercise: 0.0685994341
// → weekend:  0.1371988681
// → bread:   -0.0757554019
// → pudding: -0.0648203724
// and so on...
```

大部分的相关性都接近于 0。吃胡萝卜、面包、布丁很明显和变身松鼠没什么关联。此外周末变身的次数相对频繁。我们过滤掉一些结果，只看大于 0.1 或者小于 -0.1的相关性。

```{test: no, startCode: true}
for (let event of journalEvents(JOURNAL)) {
  let correlation = phi(tableFor(event, JOURNAL));
  if (correlation > 0.1 || correlation < -0.1) {
    console.log(event + ":", correlation);
  }
}
// → weekend:        0.1371988681
// → brushed teeth: -0.3805211953
// → candy:          0.1296407447
// → work:          -0.1371988681
// → spaghetti:      0.2425356250
// → reading:        0.1106828054
// → peanuts:        0.5902679812
```

这里有两个明显比其他((相关性))强的事件。吃((花生))和变身松鼠有一个强正相关性，而刷牙则是一个强负相关性。

有意思。让我们试试其他：

```
for (let entry of JOURNAL) {
  if (entry.events.includes("peanuts") &&
     !entry.events.includes("brushed teeth")) {
    entry.events.push("peanut teeth");
  }
}
console.log(phi(tableFor("peanut teeth", JOURNAL)));
// → 1
```

这是一个肯定的结果。在 Jacque 吃花生后不刷牙的情况下，他肯定会变成松鼠。如果他不是如此不重视他个人的口腔卫生的话，他也不会发现他这隐藏的痛苦。

发现这点后，Jacque 再也不吃花生了。从此他也再也没变身过。

{{index "weresquirrel example"}}

以后的几年里，Jacque 的日子过的一帆风顺。直到有一天，他失业了。因为他国家的问题，没有工作就意味着没有医疗。他被迫和一家((马戏团))签约，在里面表演_不可思议的松鼠人_。为此，他每次出演前都要吃好多花生。

有一天，他厌倦了这种可怜的生活，Jacque 再也没有变回人。他跳出了马戏团，消失在了森林里。

## Further arrayology

{{index [array, methods], [method, array]}}

在本章结束前，还有几个和对象相关的知识点。我们先来看看一些对数组有帮助的方法。

{{index "push method", "pop method", "shift method", "unshift method"}}

我们[前面](data#array_methods)已经见过 `push` 和 `pop` 了，分别添加和移除数组结尾的元素。同理，在数组前添加或移除元素的方法叫做 `unshift` 和 `shift`。

```
let todoList = [];
function remember(task) {
  todoList.push(task);
}
function getTask() {
  return todoList.shift();
}
function rememberUrgently(task) {
  todoList.unshift(task);
}
```

{{index "task management example"}}

上面的程序管理一个队列的任务。你通过调用 `remember("买菜")` 把任务添加到队列的后面。在你准备做某件事时，通过调用 `getTask()` 从队列的前面得到（并移除）任务。函数 `rememberUrgently` 可以把任务添加到队列的前面。

{{index [array, searching], "indexOf method", "lastIndexOf method"}}

数组还有一个 `indexOf` 方法，可以找寻一个指定的值。这个方法会从头到尾走一遍数组，之后返回指定值的第一个索引，或者 -1 如果该值不存在的话。如果需要从尾到头找寻一个元素的话，用 `lastIndexOf` 方法。

```
console.log([1, 2, 3, 2, 1].indexOf(2));
// → 1
console.log([1, 2, 3, 2, 1].lastIndexOf(2));
// → 3
```

`indexOf` 和 `lastIndexOf` 也可以有一个选择性参数，指定起始的索引值。

{{index "slice method", [array, indexing]}}

还有一个重要的数组方法是 `slice`，它的参数是起始和结束两个索引，并返回一个包含所以从起始到结束索引元素的数组。该数组包括起始元素，但不包括结束元素。

```
console.log([0, 1, 2, 3, 4].slice(2, 4));
// → [2, 3]
console.log([0, 1, 2, 3, 4].slice(2));
// → [2, 3, 4]
```

{{index [string, indexing]}}

如果结束索引不存在的话，`slice` 会返回所有起始索引后面的元素。如果起始索引也不存在的话，则返回一个复制的原数组。

{{index concatenation, "concat method"}}

`concat`方法可以用来合并多个数组，类似于字符串中的 `+` 运算符。

下面的例题运用了 `concat` 和 `slice`。它的参数是一个数组和一个索引，返回一个新的数组，是复制原数组后移除索引的值。

```
function remove(array, index) {
  return array.slice(0, index)
    .concat(array.slice(index + 1));
}
console.log(remove(["a", "b", "c", "d", "e"], 2));
// → ["a", "b", "d", "e"]
```

你如果给 `concat` 一个非数组的参数，它会以一个元素数组的形式添加到新的数组里。

## Strings and their properties

{{index [string, properties]}}

我们可以读取字符串中的 `length` 和 `toUpperCase` 属性。但是你却无法给其添加新的属性。

```
let kim = "Kim";
kim.age = 88;
console.log(kim.age);
// → undefined
```

字符串、数字、和布尔的值并不是对象，尽管 JavaScript 允许你给它们定义新的属性。但是这些属性并不会被储存。正如上面提到过的，这些值是不可以更改的。

{{index [string, methods], "slice method", "indexOf method", [string, searching]}}

但是这些类型的确有内置的属性。每个字符串都有一系列的方法。一些常用的包括 `slice` 和 `indexOf`，和数组中的同名方法相同。

```
console.log("coconuts".slice(4, 7));
// → nut
console.log("coconut".indexOf("u"));
// → 5
```

需要注意的是，字符串中的 `indexOf` 可以查询多个字符串，但是数组的只能查找一个元素。

```
console.log("one two three".indexOf("ee"));
// → 11
```

{{index [whitespace, trimming], "trim method"}}

`trim` 方法移除字符串开头和结尾的所有空格（包括空格、换行符、tab等）。

```
console.log("  okay \n ".trim());
// → okay
```

[上一章](functions)中的 `zeroPad` 函数也是字符串的方法之一。它的名字是 `padStart`，它的参数是添加的长度和字符。

```
console.log(String(6).padStart(3, "0"));
// → 006
```

{{id split}}

你可以通过 `split` 把字符串分开，也可以通过 `join` 把它们重新连起来。

```
let sentence = "Secretarybirds specialize in stomping";
let words = sentence.split(" ");
console.log(words);
// → ["Secretarybirds", "specialize", "in", "stomping"]
console.log(words.join(". "));
// → Secretarybirds. specialize. in. stomping
```

{{index "repeat method"}}

字符串可以通过 `repeat` 方法而自我重复，从而得到一个新的字符串。

```
console.log("LA".repeat(3));
// → LALALA
```

{{index ["length property", "for string"], [string, indexing]}}

我们已经见过字符串的 `length`属性了。读取字符串中的一个字符和读取数组中的元素很像。（除了一点，我们会在[下一章](higher_order#code_units))中探讨）

```
let string = "abc";
console.log(string.length);
// → 3
console.log(string[1]);
// → b
```

{{id rest_parameters}}

## Rest parameters

{{index "Math.max function"}}

It can be useful for a function to accept any number of ((argument))s.
For example, `Math.max` computes the maximum of _all_ the arguments it
is given.

{{index "period character", "max example", spread}}

To write such a function, you put three dots before the function's
last ((parameter)), like this:

```{includeCode: strip_log}
function max(...numbers) {
  let result = -Infinity;
  for (let number of numbers) {
    if (number > result) result = number;
  }
  return result;
}
console.log(max(4, 1, 9, -2));
// → 9
```

When such a function is called, the _((rest parameter))_ is bound to
an array containing all further arguments. If there are other
parameters before it, their values aren't part of that array. When, as
in `max`, it is the only parameter, it will hold all arguments.

{{index [function, application]}}

You can use a similar three-dot notation to _call_ a function with an
array of arguments.

```
let numbers = [5, 1, 7];
console.log(max(...numbers));
// → 7
```

This "((spread))s" out the array into the function call, passing its
elements as separate arguments. It is possible to include an array
like that along with other arguments, as in `max(9, ...numbers, 2)`.

{{index [array, "of rest arguments"], "square brackets"}}

Square bracket array notation similarly allows the triple-dot operator
to spread another array into the new array.

```
let words = ["never", "fully"];
console.log(["will", ...words, "understand"]);
// → ["will", "never", "fully", "understand"]
```

## The Math object

{{index "Math object", "Math.min function", "Math.max function", "Math.sqrt function", minimum, maximum, "square root"}}

As we've seen, `Math` is a grab bag of number-related utility
functions, such as `Math.max` (maximum), `Math.min` (minimum), and
`Math.sqrt` (square root).

{{index namespace, [object, property]}}

{{id namespace_pollution}}

The `Math` object is used as a container to group a bunch of related
functionality. There is only one `Math` object, and it is almost never
useful as a value. Rather, it provides a _namespace_ so that all these
functions and values do not have to be global bindings.

{{index [binding, naming]}}

Having too many global bindings "pollutes" the namespace. The more
names have been taken, the more likely you are to accidentally
overwrite the value of some existing binding. For example, it's not
unlikely to want to name something `max` in one of your programs.
Since JavaScript's built-in `max` function is tucked safely inside the
`Math` object, we don't have to worry about overwriting it.

{{index "let keyword", "const keyword"}}

Many languages will stop you, or at least warn you, when you are
defining a binding with a name that is already taken. JavaScript does
this for bindings you declared with `let` or `const`
but—perversely—not for standard bindings nor for bindings declared
with `var` or `function`.

{{index "Math.cos function", "Math.sin function", "Math.tan function", "Math.acos function", "Math.asin function", "Math.atan function", "Math.PI constant", cosine, sine, tangent, "PI constant", pi}}

Back to the `Math` object. If you need to do ((trigonometry)), `Math`
can help. It contains `cos` (cosine), `sin` (sine), and `tan`
(tangent), as well as their inverse functions, `acos`, `asin`, and
`atan`, respectively. The number π (pi)—or at least the closest
approximation that fits in a JavaScript number—is available as
`Math.PI`. There is an old programming tradition of writing the names
of ((constant)) values in all caps.

```{test: no}
function randomPointOnCircle(radius) {
  let angle = Math.random() * 2 * Math.PI;
  return {x: radius * Math.cos(angle),
          y: radius * Math.sin(angle)};
}
console.log(randomPointOnCircle(2));
// → {x: 0.3667, y: 1.966}
```

If sines and cosines are not something you are familiar with, don't
worry. When they are used in this book, in [Chapter ?](dom#sin_cos),
I'll explain them.

{{index "Math.random function", "random number"}}

The previous example used `Math.random`. This is a function that
returns a new pseudorandom number between zero (inclusive) and one
(exclusive) every time you call it.

```{test: no}
console.log(Math.random());
// → 0.36993729369714856
console.log(Math.random());
// → 0.727367032552138
console.log(Math.random());
// → 0.40180766698904335
```

{{index "pseudorandom number", "random number"}}

Though computers are deterministic machines—they always react the same
way if given the same input—it is possible to have them produce
numbers that appear random. To do that, the machine keeps some hidden
value, and whenever you ask for a new random number, it performs
complicated computations on this hidden value to create a new value.
It stores a new value and returns some number derived from it. That
way, it can produce ever new, hard-to-predict numbers in a way that
_seems_ random.

{{index rounding, "Math.floor function"}}

If we want a whole random number instead of a fractional one, we can
use `Math.floor` (which rounds down to the nearest whole number) on
the result of `Math.random`.

```{test: no}
console.log(Math.floor(Math.random() * 10));
// → 2
```

Multiplying the random number by 10 gives us a number greater than or
equal to 0 and below 10. Since `Math.floor` rounds down, this
expression will produce, with equal chance, any number from 0 through
9.

{{index "Math.ceil function", "Math.round function", "Math.abs function", "absolute value"}}

There are also the functions `Math.ceil` (for "ceiling", which rounds
up to a whole number), `Math.round` (to the nearest whole number), and
`Math.abs`, which takes the absolute value of a number, meaning it
negates negative values but leaves positive ones as they are.

## Destructuring

{{index "phi function"}}

Let's go back to the `phi` function for a moment.

```{test: wrap}
function phi(table) {
  return (table[3] * table[0] - table[2] * table[1]) /
    Math.sqrt((table[2] + table[3]) *
              (table[0] + table[1]) *
              (table[1] + table[3]) *
              (table[0] + table[2]));
}
```

{{index "destructuring binding", parameter}}

One of the reasons this function is awkward to read is that we have a
binding pointing at our array, but we'd much prefer to have bindings
for the _elements_ of the array, that is, `let n00 = table[0]` and so on.
Fortunately, there is a succinct way to do this in JavaScript.

```
function phi([n00, n01, n10, n11]) {
  return (n11 * n00 - n10 * n01) /
    Math.sqrt((n10 + n11) * (n00 + n01) *
              (n01 + n11) * (n00 + n10));
}
```

{{index "let keyword", "var keyword", "const keyword", [binding, destructuring]}}

This also works for bindings created with `let`, `var`, or
`const`. If you know the value you are binding is an array, you can
use ((square brackets)) to "look inside" of the value, binding its
contents.

{{index [object, property], [braces, object]}}

A similar trick works for objects, using braces instead of square
brackets.

```
let {name} = {name: "Faraji", age: 23};
console.log(name);
// → Faraji
```

{{index null, undefined}}

Note that if you try to destructure `null` or `undefined`, you get an
error, much as you would if you directly try to access a property
of those values.

## JSON

{{index [array, representation], [object, representation], "data format", [memory, organization]}}

Because properties only grasp their value, rather than contain it,
objects and arrays are stored in the computer's memory as
sequences of bits holding the _((address))es_—the place in memory—of
their contents. So an array with another array inside of it consists
of (at least) one memory region for the inner array, and another for
the outer array, containing (among other things) a binary number that
represents the position of the inner array.

If you want to save data in a file for later or send it to another
computer over the network, you have to somehow convert these tangles
of memory addresses to a description that can be stored or sent. You
_could_ send over your entire computer memory along with the address
of the value you're interested in, I suppose, but that doesn't seem
like the best approach.

{{indexsee "JavaScript Object Notation", JSON}}

{{index serialization, "World Wide Web"}}

What we can do is _serialize_ the data. That means it is converted
into a flat description. A popular serialization format is called
_((JSON))_ (pronounced "Jason"), which stands for JavaScript Object
Notation. It is widely used as a data storage and communication format
on the Web, even in languages other than JavaScript.

{{index [array, notation], [object, creation], [quoting, "in JSON"], comment}}

JSON looks similar to JavaScript's way of writing arrays and objects,
with a few restrictions. All property names have to be surrounded by
double quotes, and only simple data expressions are allowed—no
function calls, bindings, or anything that involves actual
computation. Comments are not allowed in JSON.

A journal entry might look like this when represented as JSON data:

```{lang: "application/json"}
{
  "squirrel": false,
  "events": ["work", "touched tree", "pizza", "running"]
}
```

{{index "JSON.stringify function", "JSON.parse function", serialization, deserialization, parsing}}

JavaScript gives us the functions `JSON.stringify` and `JSON.parse` to
convert data to and from this format. The first takes a JavaScript
value and returns a JSON-encoded string. The second takes such a
string and converts it to the value it encodes.

```
let string = JSON.stringify({squirrel: false,
                             events: ["weekend"]});
console.log(string);
// → {"squirrel":false,"events":["weekend"]}
console.log(JSON.parse(string).events);
// → ["weekend"]
```

## Summary

Objects and arrays (which are a specific kind of object) provide ways
to group several values into a single value. Conceptually, this allows
us to put a bunch of related things in a bag and run around with the
bag, instead of wrapping our arms around all of the individual things
and trying to hold on to them separately.

Most values in JavaScript have properties, the exceptions being `null`
and `undefined`. Properties are accessed using `value.prop` or
`value["prop"]`. Objects tend to use names for their properties
and store more or less a fixed set of them. Arrays, on the other hand,
usually contain varying amounts of conceptually identical values and
use numbers (starting from 0) as the names of their properties.

There _are_ some named properties in arrays, such as `length` and a
number of methods. Methods are functions that live in properties and
(usually) act on the value they are a property of.

You can iterate over arrays using a special kind of `for` loop—`for
(let element of array)`.

## Exercises

### The sum of a range

{{index "summing (exercise)"}}

The [introduction](intro) of this book alluded to the following as a
nice way to compute the sum of a range of numbers:

```{test: no}
console.log(sum(range(1, 10)));
```

{{index "range function", "sum function"}}

Write a `range` function that takes two arguments, `start` and `end`,
and returns an array containing all the numbers from `start` up to
(and including) `end`.

Next, write a `sum` function that takes an array of numbers and
returns the sum of these numbers. Run the example program and see
whether it does indeed return 55.

{{index "optional argument"}}

As a bonus assignment, modify your `range` function to take an
optional third argument that indicates the "step" value used when
building the array. If no step is given, the elements go up by
increments of one, corresponding to the old behavior. The function
call `range(1, 10, 2)` should return `[1, 3, 5, 7, 9]`. Make sure it
also works with negative step values so that `range(5, 2, -1)`
produces `[5, 4, 3, 2]`.

{{if interactive

```{test: no}
// Your code here.

console.log(range(1, 10));
// → [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log(range(5, 2, -1));
// → [5, 4, 3, 2]
console.log(sum(range(1, 10)));
// → 55
```

if}}

{{hint

{{index "summing (exercise)", [array, creation], "square brackets"}}

Building up an array is most easily done by first initializing a
binding to `[]` (a fresh, empty array) and repeatedly calling its
`push` method to add a value. Don't forget to return the array at the
end of the function.

{{index [array, indexing], comparison}}

Since the end boundary is inclusive, you'll need to use the `<=`
operator rather than `<` to check for the end of your loop.

{{index "arguments object"}}

The step parameter can be an optional parameter that defaults (using
the `=` operator) to 1.

{{index "range function", "for loop"}}

Having `range` understand negative step values is probably best done
by writing two separate loops—one for counting up and one for counting
down—because the comparison that checks whether the loop is finished
needs to be `>=` rather than `<=` when counting downward.

It might also be worthwhile to use a different default step, namely,
-1, when the end of the range is smaller than the start. That way,
`range(5, 2)` returns something meaningful, rather than getting stuck
in an ((infinite loop)). It is possible to refer to previous
parameters in the default value of a parameter.

hint}}

### Reversing an array

{{index "reversing (exercise)", "reverse method", [array, methods]}}

Arrays have a `reverse` method that changes the array by inverting
the order in which its elements appear. For this exercise, write two
functions, `reverseArray` and `reverseArrayInPlace`. The first,
`reverseArray`, takes an array as argument and produces a _new_ array
that has the same elements in the inverse order. The second,
`reverseArrayInPlace`, does what the `reverse` method does: it
_modifies_ the array given as argument by reversing its elements.
Neither may use the standard `reverse` method.

{{index efficiency, "pure function", "side effect"}}

Thinking back to the notes about side effects and pure functions in
the [previous chapter](functions#pure), which variant do you expect to
be useful in more situations? Which one runs faster?

{{if interactive

```{test: no}
// Your code here.

console.log(reverseArray(["A", "B", "C"]));
// → ["C", "B", "A"];
let arrayValue = [1, 2, 3, 4, 5];
reverseArrayInPlace(arrayValue);
console.log(arrayValue);
// → [5, 4, 3, 2, 1]
```

if}}

{{hint

{{index "reversing (exercise)"}}

There are two obvious ways to implement `reverseArray`. The first is
to simply go over the input array from front to back and use the
`unshift` method on the new array to insert each element at its start.
The second is to loop over the input array backwards and use the `push`
method. Iterating over an array backwards requires a (somewhat awkward)
`for` specification, like `(let i = array.length - 1; i >= 0; i--)`.

{{index "slice method"}}

Reversing the array in place is harder. You have to be careful not to
overwrite elements that you will later need. Using `reverseArray` or
otherwise copying the whole array (`array.slice(0)` is a good way to
copy an array) works but is cheating.

The trick is to _swap_ the first and last elements, then the second
and second-to-last, and so on. You can do this by looping over half
the length of the array (use `Math.floor` to round down—you don't need
to touch the middle element in an array with an odd number of
elements) and swapping the element at position `i` with the one at
position `array.length - 1 - i`. You can use a local binding to
briefly hold on to one of the elements, overwrite that one with its
mirror image, and then put the value from the local binding in the
place where the mirror image used to be.

hint}}

{{id list}}

### A list

{{index ["data structure", list], "list (exercise)", "linked list", array, collection}}

Objects, as generic blobs of values, can be used to build all sorts of
data structures. A common data structure is the _list_ (not to be
confused with array). A list is a nested set of objects, with the
first object holding a reference to the second, the second to the
third, and so on.

```{includeCode: true}
let list = {
  value: 1,
  rest: {
    value: 2,
    rest: {
      value: 3,
      rest: null
    }
  }
};
```

The resulting objects form a chain, like this:

{{figure {url: "img/linked-list.svg", alt: "A linked list",width: "8cm"}}}

{{index "structure sharing", [memory, structure sharing]}}

A nice thing about lists is that they can share parts of their
structure. For example, if I create two new values `{value: 0, rest:
list}` and `{value: -1, rest: list}` (with `list` referring to the
binding defined earlier), they are both independent lists, but they
share the structure that makes up their last three elements. The
original list is also still a valid three-element list.

Write a function `arrayToList` that builds up a list structure like
the one shown when given `[1, 2, 3]` as argument. Also write a
`listToArray` function that produces an array from a list. Then add a
helper function `prepend`, which takes an element and a list and
creates a new list that adds the element to the front of the input
list, and `nth`, which takes a list and a number and returns the
element at the given position in the list (with zero referring to the
first element) or `undefined` when there is no such element.

{{index recursion}}

If you haven't already, also write a recursive version of `nth`.

{{if interactive

```{test: no}
// Your code here.

console.log(arrayToList([10, 20]));
// → {value: 10, rest: {value: 20, rest: null}}
console.log(listToArray(arrayToList([10, 20, 30])));
// → [10, 20, 30]
console.log(prepend(10, prepend(20, null)));
// → {value: 10, rest: {value: 20, rest: null}}
console.log(nth(arrayToList([10, 20, 30]), 1));
// → 20
```

if}}

{{hint

{{index "list (exercise)", "linked list"}}

Building up a list is easier when done back to front. So `arrayToList`
could iterate over the array backwards (see the previous exercise) and, for
each element, add an object to the list. You can use a local binding
to hold the part of the list that was built so far and use an
assignment like `list = {value: X, rest: list}` to add an element.

{{index "for loop"}}

To run over a list (in `listToArray` and `nth`), a `for` loop
specification like this can be used:

```
for (let node = list; node; node = node.rest) {}
```

Can you see how that works? Every iteration of the loop, `node` points
to the current sublist, and the body can read its `value` property to
get the current element. At the end of an iteration, `node` moves to
the next sublist. When that is null, we have reached the end of the
list, and the loop is finished.

{{index recursion}}

The recursive version of `nth` will, similarly, look at an ever
smaller part of the "tail" of the list and at the same time count down
the index until it reaches zero, at which point it can return the
`value` property of the node it is looking at. To get the zeroth
element of a list, you simply take the `value` property of its head
node. To get element _N_ + 1, you take the *N*th element of the list
that's in this list's `rest` property.

hint}}

{{id exercise_deep_compare}}

### Deep comparison

{{index "deep comparison (exercise)", [comparison, deep], "deep comparison", "== operator"}}

The `==` operator compares objects by identity. But sometimes you'd
prefer to compare the values of their actual properties.

Write a function `deepEqual` that takes two values and returns true
only if they are the same value or are objects with the same
properties, where the values of the properties are equal when compared
with a recursive call to `deepEqual`.

{{index null, "=== operator", "typeof operator"}}

To find out whether values should be compared directly (use the `===`
operator for that) or have their properties compared, you can use the
`typeof` operator. If it produces `"object"` for both values, you
should do a deep comparison. But you have to take one silly exception
into account: because of a historical accident, `typeof null` also
produces `"object"`.

{{index "Object.keys function"}}

The `Object.keys` function will be useful when you need to go over the
properties of objects to compare them.

{{if interactive

```{test: no}
// Your code here.

let obj = {here: {is: "an"}, object: 2};
console.log(deepEqual(obj, obj));
// → true
console.log(deepEqual(obj, {here: 1, object: 2}));
// → false
console.log(deepEqual(obj, {here: {is: "an"}, object: 2}));
// → true
```

if}}

{{hint

{{index "deep comparison (exercise)", [comparison, deep], "typeof operator", "=== operator"}}

Your test for whether you are dealing with a real object will look
something like `typeof x == "object" && x != null`. Be careful to
compare properties only when _both_ arguments are objects. In all
other cases you can just immediately return the result of applying
`===`.

{{index "Object.keys function"}}

Use `Object.keys` to go over the properties. You need to test whether
both objects have the same set of property names and whether those
properties have identical values. One way to do that is to ensure that
both objects have the same number of properties (the lengths of the
property lists are the same). And then, when looping over one of the
object's properties to compare them, always first make sure
the other actually has a property by that name. If they have the same
number of properties and all properties in one also exist in the
other, they have the same set of property names.

{{index "return value"}}

Returning the correct value from the function is best done by
immediately returning false when a mismatch is found and returning
true at the end of the function.

hint}}
