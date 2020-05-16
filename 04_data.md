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

有时我们需要一个函数拥有任意数的((参数))。比如，`Math.max` 返回_所有_参数的最大值。

{{index "period character", "max example", spread}}

我们只需在最后一个((参数))前放三个点，就可达到此效果：

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

在调用此函数时，_((剩余参数))_被绑定到一个包含所有其他参数的数组中。任何在此前的参数都不被包括在内。因此上例中的 `max` 只有一个参数，所以它包括了所有的参数。

{{index [function, application]}}

以此类推，你可以用同样的三个点和数组组合去调用一个函数：

```
let numbers = [5, 1, 7];
console.log(max(...numbers));
// → 7
```

这会把数组的值作为参数，逐一“((扩散))”到被调用的函数中。你也可以把它和其他参数混合使用，比如 `max(9, ...numbers, 2)`。

{{index [array, "of rest arguments"], "square brackets"}}

中括号的数组表达也支持三点运算符，将其值融入到新的数组中。

```
let words = ["never", "fully"];
console.log(["will", ...words, "understand"]);
// → ["will", "never", "fully", "understand"]
```

## The Math object

{{index "Math object", "Math.min function", "Math.max function", "Math.sqrt function", minimum, maximum, "square root"}}

已知 `Math` 是一组数字相关的百宝袋，用于许多实用的函数。比如 `Math.max`（最大值）、`Math.min`（最小值）、以及`Math.sqrt`（平方根）。

{{index namespace, [object, property]}}

{{id namespace_pollution}}

`Math` 对象是一个包含许多相关函数的容器。JavaScript 只有一个 `Math` 对象，而且几乎不会被直接用作一个值。通常，它提供一个_命名空间_，好避免这些函数和值被全局绑定。

{{index [binding, naming]}}

过多的全局绑定会“污染”命名空间。当你定义越多的名字，越容易无意间改写这些名字的值。比如，你很可能在一个程序中命名一个 `max` 变量。好在 JavaScript 自带的 `max` 函数被包裹在 `Math` 对象中，我们定义 `max` 时，不需要担心它被改写。

{{index "let keyword", "const keyword"}}

很多语言，在你试图改写内建的名字时，会提醒甚至阻止你。JavaScript 只有在你用 `let` 或者 `const` 定义变量会如此。但对于传统的 `var` 和 `function` 却不会。

{{index "Math.cos function", "Math.sin function", "Math.tan function", "Math.acos function", "Math.asin function", "Math.atan function", "Math.PI constant", cosine, sine, tangent, "PI constant", pi}}

回到 `Math` 对象。它也可以用来计算((三角函数))，包括了 `cos` (cosine), `sin` (sine), 和 `tan` (tangent)，以及和它们对应的反函数 `acos`, `asin`, 和 `atan`。此外，`Math.PI` 承载着 JavaSript 接受范围内的圆周率 π 的最近小数点。传统的编程习惯用全大写字母命名常数。

```{test: no}
function randomPointOnCircle(radius) {
  let angle = Math.random() * 2 * Math.PI;
  return {x: radius * Math.cos(angle),
          y: radius * Math.sin(angle)};
}
console.log(randomPointOnCircle(2));
// → {x: 0.3667, y: 1.966}
```

你如果对三角函数不了解，也不需担心。我会在本书的[第十四章](dom#sin_cos)讲解它们。

{{index "Math.random function", "random number"}}

上面的例子用到 `Math.random`。这是一个返回零至一（包含）内的伪随机函数。

```{test: no}
console.log(Math.random());
// → 0.36993729369714856
console.log(Math.random());
// → 0.727367032552138
console.log(Math.random());
// → 0.40180766698904335
```

{{index "pseudorandom number", "random number"}}

虽然电脑是确定性机器（一样输入，结果永远一致），但我们依旧可以得到一些看似随机的数字。为此，电脑储存一些隐藏值，当你需要一个新的随机数字时，它会根据这个隐藏值做一系列复制计算得到一个新的值。它把这个新值保存起来，再返回一个从中衍生的数字。如此，它可以一直返回一个难以猜测且_看似_随机的数字。

{{index rounding, "Math.floor function"}}

如果我们想要一个随机的整数，而不是分数的话，我们可以对 `Math.random` 返回的值用 `Math.floor`（向下取整）。

```{test: no}
console.log(Math.floor(Math.random() * 10));
// → 2
```

将随机的值乘以 10 会得到一个大于等于 0 且小于 10 的数。因为 `Math.floor` 向下取整，这个表达式会以相同概率返回 0 至 9 中的任意整数。

{{index "Math.ceil function", "Math.round function", "Math.abs function", "absolute value"}}

类似的，也有 `Math.ceil`（向上取整）、`Math.round`（四舍五入最近整数）、和 `Math.abs`（绝对值）函数。

## Destructuring

{{index "phi function"}}

回顾 `phi` 函数。

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

这个函数难读的原因之一是一个指向数组的变量。我们更希望直接绑定数组中的_元素_。也就是说，`let n00 = table[0]`，以此类推。好在，在 JavaScript 中有个更为简洁的方式。

```
function phi([n00, n01, n10, n11]) {
  return (n11 * n00 - n10 * n01) /
    Math.sqrt((n10 + n11) * (n00 + n01) *
              (n01 + n11) * (n00 + n10));
}
```

{{index "let keyword", "var keyword", "const keyword", [binding, destructuring]}}

此方法适用于 `let`，`var`，或 `const` 创建的变量。如果已知要绑定数组元素，你可以直接用((中括号))“查看”数组的值，并绑定其元素。

{{index [object, property], [braces, object]}}

类似的方法也适用于对象，只需把中括号改为大括号。

```
let {name} = {name: "Faraji", age: 23};
console.log(name);
// → Faraji
```

{{index null, undefined}}

注意：如果试图解构 `null` 或 `undefined`，你会得到错误。正如你直接从它们中提取一个属性。

## JSON

{{index [array, representation], [object, representation], "data format", [memory, organization]}}

因为属性本身不包含任何值，对象和数组是以位序列的方式存在电脑内存中，这些位序列包含其内容_((地址))_（内存中的位置）。所以一个包含数组的数组（至少）有一个属于内部数组的内存区域和一个外部数组的内存区域，以及一个代表内部数组地址的二进制。

你如果想保存一个文件或者通过网络转发到另一个电脑上，你需要将其从原有的内存地址转换成可被储存或转发的叙述。你_可以_转发包括目标在内的全部电脑内存，但这显然不是最佳方案。

{{indexsee "JavaScript Object Notation", JSON}}

{{index serialization, "World Wide Web"}}

我们可以_序列化_该数据。也就是将其转换成一个平面叙述。_((JSON))_（读作 Jason）是一个流行的序列化格式，是 JavaScript Object Notation 的缩写。作为一个网络数据储存和通讯格式，它被广泛运用，包括 JavaScript 以外的语言。

{{index [array, notation], [object, creation], [quoting, "in JSON"], comment}}

JSON 的写作方式类似于 JavaScript 中的数组和对象，但有些限制。所有的属性名字必须在双引号内，而且只接受简单的数据表达式。也就是说函数、变量、或者任何需要运算的值都不可以。此外，注释也不支持。

一篇日志在 JSON 中如下所示：

```{lang: "application/json"}
{
  "squirrel": false,
  "events": ["work", "touched tree", "pizza", "running"]
}
```

{{index "JSON.stringify function", "JSON.parse function", serialization, deserialization, parsing}}

JavaScript 提供了 `JSON.stringify` 和 `JSON.parse` 函数，用来转换数据。第一个将一个 JavaScript 变量转换成 JSON，而第二个则将 JSON 转换成 JavaScript 值。

```
let string = JSON.stringify({squirrel: false,
                             events: ["weekend"]});
console.log(string);
// → {"squirrel":false,"events":["weekend"]}
console.log(JSON.parse(string).events);
// → ["weekend"]
```

## Summary

对象和数组（一个特殊的对象）提供多种不同将多个值组合在一起的方式。打个比方，与其带着一堆东西出门，我们可以将其放入一个书包中，并背着书包出门。

除了 `null` 和 `undefined` 外的 JavaScript 的值都有属性。属性可以通过 `value.prop` 或者 `value["prop"]` 调用。对象的属性通常是名字，并或多或少的储存一组固定的属性。数组的属性则是数字（从0开始），且其数量不定。

数组中有_个别_名字属性，比如 `length` 和些方法。方法其实就是居住在属性中的函数，通常根据其对象的值运行。

你可以通过一种特殊的 `for` 循环遍历数组：`for (let element of arry)`。

## Exercises

### The sum of a range

{{index "summing (exercise)"}}

本书的[介绍](intro)中暗示过一个简洁的计算一系列数字的和的方法：

```{test: no}
console.log(sum(range(1, 10)));
```

{{index "range function", "sum function"}}

写一个 `range` 函数，它有两个参数，`start` 和 `end`，并返回一个包含所有从 `start` 到 `end` 的数字（包括）的数组。

在写一个 `sum` 函数，它介绍一个数字数组参数，并返回这些数字的和。测试上面的例题，确保它的确返回 55。

{{index "optional argument"}}

附加题，改写 `range` 函数，使其接受可选的第三个参数，代表“步数”。如果没有步数，数组中的元素如原先般逐一增加。以 `range(1, 10, 2)` 调用该函数将得到 `[1, 3, 5, 7, 9]`。确保负的步数依旧工作。比如，`range(5, 2, -1)` 应返回 `[5, 4, 3, 2]`。

{{if interactive

```{test: no}
// 你的代码

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

创建一个数组可先给 `[]`（一新建的空数组）绑定一个变量。再反复调用其 `push` 方法添加值。不要忘记在函数的结尾返回该数组。

{{index [array, indexing], comparison}}

因为包括了末端边界，我们需要用 `<=` 运算符，而不是 `<`，检验循环的结束条件。

{{index "arguments object"}}

步数可以是一个默认为1的参数（用 `=` 运算符）。

{{index "range function", "for loop"}}

让 `range` 接受负步数的最好方法是写两个不同的循环：一个往上数，一个往下数。因为向下数的结束条件是 `>=` 而不是 `<=`。

或许考虑当结尾的数字小于起始数字时，用一个不同的默认参数，比如 -1。这样，`range(5, 2)` 可以返回一个有意义的数组，而不是陷入((无限循环))中。在定义默认参数是，可参考前面的参数。

hint}}

### Reversing an array

{{index "reversing (exercise)", "reverse method", [array, methods]}}

数组有一个 `reverse` 方法，改变原数组，倒序其元素。写两个函数，`reverseArray` 和 `reverseArrayInPlace`。第一个函数 `reverseArray` 有一个数组参数，并返回一个_新的_数组，其元素是原数组的倒序。第二个函数 `reverseArrayInPlace` 类似 `reverse` 方法：它通过倒序其元素而_改变_原数组。两个函数皆不可用内置的 `reverse` 方法。

{{index efficiency, "pure function", "side effect"}}

回顾[上一章](functions#pure)中讨论过的副作用和纯函数。大部分情况下，你认为哪个更有用？哪个更快？

{{if interactive

```{test: no}
// 你的代码

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

有两个明显的编写 `reverseArray` 的方法。第一个是从头到尾遍历数组，通过 `unshift` 方法将元素添加到新的数组中。第二个是通过 `push` 方法，从尾到头遍历数组。第二种方法需要一个略微奇怪的 `for`：`(let i = array.length - 1; i >= 0; i--)`。

{{index "slice method"}}

反转原数组略微复杂。注意不要改写以后将用到的元素。用 `reverseArray` 或者复制原元素（比如 `array.slice(0)`）虽然工作，但属作弊行为。

其秘诀是_交换_第一个和最后一个元素，之后在交换第二个和倒数第二个元素，以此类推。你可以循环一半的数组（用 `Math.floor` 向下取整，你不需要管奇数元素中的中间值），将在 `i` 位置上的元素和在 `array.length - 1 - i` 位置上的元素交换。你可以通过本地变量临时绑定其中一个元素，改写该元素后，在把本地变量中的值放入另一个元素里。

hint}}

{{id list}}

### A list

{{index ["data structure", list], "list (exercise)", "linked list", array, collection}}

对象，作为一个通用的变量组合，可以用来组建各种不同的数据结构。一个常见的数据结构是_链表_（不要和数组混淆）。一个链表是一个对象嵌套集，第一个对象含有第二个对象的参考，第二个含有第三个，依此类推。

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

最后得到的对象好比一个链子：

{{figure {url: "img/linked-list.svg", alt: "A linked list",width: "8cm"}}}

{{index "structure sharing", [memory, structure sharing]}}

链表的一个好处是它们可以共享部分结构。比如，我如果创建两个新值 `{value: 0, rest: list}` 和 `{value: -1, rest: list}`（`list` 代表前面定义的变量），它们虽然是两个独立的链表，但它们最后的三个元素共享同一个链表。原先的链表也是一个有效的三元素链表。

写一个 `arrayToList` 函数，当提供数组 `[1, 2, 3]` 作为参数时，返回如上所示的链表。另外在写一个 `listToArray` 函数，将参数中的链表转变成数组并返回。之后写一个辅助函数 `prepend`，其参数为一个元素和一个链表，并创建一个以元素为首，原链表随后的新链表。以及 `nth`，其参数为一个链表和一个数字，并返回链表中在该数字位置的元素（0 表示第一个元素）或者 `undefined` 如果该元素不存在。

{{index recursion}}

试着用递归法写 `nth`。

{{if interactive

```{test: no}
// 你的代码

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

从后往前创建链表更为容易。所以 `arrayToList` 应该从尾向头循环数组（参考上一个习题）。对每个元素，添加一个对象到链表上。你可以利用本地变量储存当前链表，并用类似 `list = {value: X, rest: list}` 的等式添加元素。

{{index "for loop"}}

循环一个链表（比如 `listToArray` 和 `nth`），一个 `for` 循环规范可为：

```
for (let node = list; node; node = node.rest) {}
```

你知道原理么？每次迭代，`node` 都会指向当前的子链表，而表达块中可读取其 `value` 属性得到当前元素。迭代结束时，`node` 指向下一个子链表。当它成 `null` 时，我们就到达链表的末端，该循环也就结束了。

{{index recursion}}

`nth` 的递归法会检验一个更小的链表“尾巴”，与此同时倒数索引直至其为0时，返回当前元素的 `value` 属性。对于第零个链表元素，你只需返回其链表第一元素的 `value` 属性。对于第 _N_ + 1 个元素，你返回 `rest` 属性中的第 *N* 个链表元素。

hint}}

{{id exercise_deep_compare}}

### Deep comparison

{{index "deep comparison (exercise)", [comparison, deep], "deep comparison", "== operator"}}

`==` 运算符通过身份对比对象。但有些时候，我们需要对比它们属性中的值。

写一个 `deepEqual` 函数，有两个参数，并返回 `true` 如果他们的值，或者对象的属性相等。也就是说，如果通过递归法调用 `deepEqual`，其属性的值也相等。

{{index null, "=== operator", "typeof operator"}}

找出是否应该直接对比值（用 `===` 运算符）或者对比它们的属性，你可以用 `typeof` 运算符。如果两个参数的结果都是 `"object"`，你应该用深入对比。但有一个愚蠢的例外：因为一个历史事故，`typeof null` 也返回 `"object"`。

{{index "Object.keys function"}}

`Object.keys` 函数可帮助你遍历其对象的属性，从而进行比较。

{{if interactive

```{test: no}
// 你的代码

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

你可以通过 `typeof x == "object" && x != null` 来检测你是否在处理一个真正的对象。注意只有在两个参数_都_为对象时，才进行属性比较。其他情况下，可直接返回 `===` 的结果。

{{index "Object.keys function"}}

利用 `Object.keys` 去遍历其属性。你需要检验两个对象的属性一致，且属性的值也一致。一个方法是检验两个对象有相同数量的属性（属性数组的长度相等）。之后在遍历其中一个属性数组时，确保另一个对象有其属性后，对它们进行比较。如果它们的属性数量相等，而且其中一个对象的所有属性都在另一个对象中，它们拥有同样的属性。

{{index "return value"}}

返回正确值的最佳方法是在遇到不想等的情况时，立刻返回 `false`。这样在函数的结尾可直接返回 `true`。

hint}}
