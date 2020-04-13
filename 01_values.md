{{meta {docid: values}}}

# Values, Types, and Operators

{{quote {author: "Master Yuan-Ma", title: "The Book of Programming", chapter: true}

程序在机器内部运转。它不废吹灰之力的伸展或收缩。电子非常和谐的散开或重组。显示器上的画面无非是水面的涟漪。其本质皆隐藏在下面。

quote}}

{{index "Yuan-Ma", "Book of Programming"}}

{{figure {url: "img/chapter_picture_1.jpg", alt: "Picture of a sea of bits", chapter: framed}}}

{{index "binary data", data, bit, memory}}

在电脑的世界里只有数据。你可以读取数据、改写数据、创建新的数据——但任何非数据的东西都是禁止的。所有数据的本质皆相同，因此被储存为长位序列。

{{index CD, signal}}

_位_是任何二值事物，一般用零和一表示。在电脑里，它们可以是高低电荷、强弱信号、或者 CD 光盘表面的亮暗点。任何离散信息都可以转换成零和一的序列，从而用位表示。

{{index "binary number", radix, "decimal number"}}

我们可以用位表示数字13。这和十进制原理相同，只不过由2个((数字))代替10个数字。所以每次进位的倍数也是2。下面就是以位表示数字13和相对应的进位倍数：

```{lang: null}
   0   0   0   0   1   1   0   1
 128  64  32  16   8   4   2   1
```

二进制数 00001101，非0的数字分别代表8、4、1，相加在一起就是13。

## Values

{{index [memory, organization], "volatile data storage", "hard drive"}}

想象一片汪洋大海，其每一滴水都是一个位。一个典型的现代计算机的易失性存储器（RAM等）可容纳超过300亿个位。却也只是其非易失性存储器（硬盘等）的一个零头而已。

为了避免如此庞大的位流失，我们必须把这些位分成代表信息的组。在 JavaScript 中，这些分组叫做((值))。尽管所有的值都是由位组成，但它们各司其职。每个值都有一个代表它职位的((类型))。有些值是数字，有些值是字符，有些值是函数等。

{{index "garbage collection"}}

创建一个值是相当简单方便的。你只需要叫他的名字而已。不需要任何多余的操作或准备。当然值不是无中生有出来的。每个值都必须被储存在某个地方。所以如果你想同时用成千上万个值的话，你的电脑很可能会内存不足。庆幸的是，这只有在你同时需要这些值的时候发生。当你不再需要某个值时，它就会消失，代表它的位这时将被回收，以便用来表示后面的值。

本章将介绍 JavaScript 中最核心的部分，包括简单的类型和可以进行类型运算的运算符。

## Numbers

{{index [syntax, number], number, [number, notation]}}
_数字_类的值顾名思义指数值。在 JavaScript 里，它们写做：

```
13
```

{{index "binary number"}}

把它放在一个程序里，它相对应的位模式13就会被存放在电脑的内存里。

{{index [number, representation], bit}}

JavaScript 使用固定的64位浮点（双精度IEEE 754）储存一个数值。因为64位的组合有限，所以支持的数字是有限的。在_N_位十进制中，可以有 10^N^ 个数字。所以，在64位的二进制里，总共可以有 2^64^ 的不同的数字，大约是 1800京（18后面根18个0）。算是个天文数字。

曾经的电脑内存相当有限，以至于大家习惯用8位或16位来代表数字。在如此小的范围内，数字经常会意外_((溢出))_，得到一个无法无法被8位或16位表示的数字。今天，即使掌上电脑都有相当大的内存储蓄，所以我们可以随便用64位来代表数字。只有和真正的天文数字打交道时，才需要担心溢出。

{{index sign, "floating-point number", "sign bit"}}

但是 JavaScript 的数字并无法支持所有小于 1800京 的整数。除了正数外，我们还需要支持负数。所以第一个位用来表示负号。此外，我们也需要支持小数。因此，有些位用来存放小数点的位置。因此，JavaScript 支持的最大的正整数大约是 9千兆（15个零）。依旧是相当庞大的数字。

{{index [number, notation], "fractional number"}}

分数也用小数来表示。

```
9.81
```

{{index exponent, "scientific notation", [number, notation]}}

对特别大或小的数字，我们也可以用科学计数法：_e_ ((代表 _指数_ exponent))后面跟上相对应的指数。

```
2.998e8
```

这就是 2.998 x 10^8^ = 299,800,000。

{{index pi, [number, "precision of"], "floating-point number"}}

小于 9千兆的整数运算永远都是精准的。不过，分数运算往往无法保证精准度。正如 π（圆周率）只能约等于一个有限小数一样，很多数字在64位二进制中会失去精准率。虽然可惜，好在这只会在特定情况下引起实际问题。更重要的是知道它的存在，因此用约等，而不是对等，对待分数。

### Arithmetic

{{index [syntax, operator], operator, "binary operator", arithmetic, addition, multiplication}}

数字最重要的就是算术。算术运算，比如加、乘，是用两个数字得出一个新的数字。在 JavaScript 中这样表示：

```
100 + 4 * 11
```

{{index [operator, application], asterisk, "plus character", "* operator", "+ operator"}}

`+`和`*`是 _算术运算符_。第一个代表加法，第二个是乘法。把一个算术运算符放置两个数值间，会对这两个数值进行算术，从而得到一个新的值。

{{index grouping, parentheses, precedence}}

但上面的例子是“100加上4之后乘以11”还是先做乘法再做加法呢？你也许猜到了，先做乘法。但和数学一样，你可以把加法运算放进括号内，以提升其优先权。

```
(100 + 4) * 11
```

{{index "hyphen character", "slash character", division, subtraction, minus, "- operator", "/ operator"}}

减法的算术运算符是`-`，除法的则是`/`。

在没有括号的前提下，运算的先后顺序是根据算术运算符自身的优先权而决定的。如前面例子所示，乘法的优先权在加法之前。除法(`/`)和乘法(`*`)的优先权一致，加法(`+`)和减法(`-`)的优先权一致。在算术运算符的优先权一致的情况下，比如`1 - 2 + 1`，运算会按照从左到右的顺序依次进行：`(1 - 2) + 1`。

你不需要担心运算顺序。如果不确定的话，直接加括号。

{{index "modulo operator", division, "remainder operator", "% operator"}}

除此之外，还有一个你也许不熟悉的算术运算符。`%`代表求余。`X % Y`的意思是`X`除以`Y`的余数。比如，`314 % 100`的结果是`14`，`144 % 12`的结果是`0`。求余的优先权和乘除法一致。

### Special numbers

{{index [number, "special values"]}}

JavaScript 中有三个特殊的数值。尽管它们是数字，却和普通的数字不一样。

{{index infinity}}

前两个是`Infinity`和`-Infinity`，分别代表正负无穷大。需要注意的是，`Infinity - 1`还是`Infinity`。不要太相信无穷大的运算。它不具备一定的数学合理性，而且很快就会导致`NaN`，也是第三个特殊数。

{{index NaN, "not a number", "division by zero"}}

`NaN` 代表“不是数字”（英文：Not A Number)，尽管它的值_是_数值。任何导致无意义结果的数字运算，比如`0 / 0`（零除以零）或者`Infinity - Infinity`，都会得到这个结果。

## Strings

{{indexsee "grave accent", backtick}}

{{index [syntax, string], text, character, [string, notation], "single-quote character", "double-quote character", "quotation mark", backtick}}

下一个基本的数据类型是_字符串_。字符串是用来表示文字的。字符串的内容是被引号包围的。

```
`Down on the sea`
"Lie on the ocean"
'Float on the ocean'
```

你可以用单引号、双引号、或者反引号去表示字符串，只要起始和结尾的引号一致。

{{index "line break", "newline character"}}

JavaScript 几乎把所以被引号包围的东西都转换成字符串。但也有些特殊字符。比如用引号包围引号。_换行符_（在你输入[回车键]{keyname}所得到的字符）在不通过转义字符的情况下，只能在反引号中表达(`` ` ``)。

{{index [escaping, "in strings"], ["backslash character", "in strings"]}}

为了让所有字符都可以被转换成字符串，我们引入了反斜杠（`\`）。它后面的字符有特殊的意义。它就是_转义字符_。在反斜杠后面的引号只是字符串里的一个字符，而不是字符串的结束标志。而紧随反斜杠其后的`n`代表换行符。紧随反斜杠后面的`t`是((水平制表符))。如下面的例子：

```
"第一行文字\n第二行文字"
```

它实际的文字展现是:

```{lang: null}
第一行文字
第二行文字
```

当然，有时候我们想要反斜杠作为一个普通字符，而不是转义字符。当两个反斜杠挨在一起，它们会合二为一，成为一个普通的字符出现在我们的字符串中。比如字符串 "一个换行符可以写做\"\\n\"。" 的字符串表达方式是：

```
"一个换行符可以写做 \"\\n\"."
```

{{id unicode}}

{{index [string, representation], Unicode, character}}

字符串也需要给转换成一连串的位存在电脑的内存中。JavaScript 是根据_((Unicode))_标准转换的。这个标准给每一个我们可能遇到的字符了一个编号，包括各种语言。因为每一个字符都有一个编号，一个字符串可以由一系列的编号组成。

{{index "UTF-16", emoji}}

JavaScript 正是这么做的。可是有一个问题：JavaScript 用16位来表示每一个字符，所以只支持 2^16^ 个不同的字符。但是 Unicode 支持的字符数量差不多是这个的一倍。所以许多字符，比如表情符号，需要两个“字符位置”。我们[第五章](higher_order#code_units)会重温这个问题。

{{index "+ operator", concatenation}}

字符串不可以被乘、被除、或被剪，但是支持`+`运算符号。虽然字符串无法相加，但是它们可以相连在一起。必须下面的字符串运算结果是`"concatenate"`：

```
"con" + "cat" + "e" + "nate"
```

字符串值支持一系列函数操作，可以用来做字符串运算。详情会在[第四章](data#methods)讲。

{{index interpolation, backtick}}

单引号和双引号的字符串本质上没什么区别，除了在字符串内需要转义的引号不同。反引号的字符串，也叫_((模板字面量))_，略微不同。除了支持换行符外，也支持其他变量值。

```
`一百的一半是 ${100 / 2}`
```

任何在模板字面量里的`${}`都会先得出其相对应的值后在转换成字符串。所以上面的最终结果是 "一百的一半是 50"。

## Unary operators

{{index operator, "typeof operator", type}}

不是所有的操作都是符号。有些可以是单词，比如 `typeof`操作，返回一个字符串表示被操作的值的类型。

```
console.log(typeof 4.5)
// → number
console.log(typeof "x")
// → string
```

{{index "console.log", output, "JavaScript console"}}

{{id "console.log"}}

在例题中，我们会用 `console.log` 来表示我们想鉴定结果。[下一章](program_structure)中会遇到更多。

{{index negation, "- operator", "binary operator", "unary operator"}}

以上所有例题中，除了 `typeof` 外，其他操作皆需要两个值。需要两个值的操作叫做_二元_操作，需要一个值的操作叫做_一元_操作。减号可以是一元也可以是二元操作。

```
console.log(- (10 - 2))
// → -8
```

## Boolean values

{{index Boolean, operator, true, false, bit}}

一般用于表示一个只可能有两种结果的值，比如“是”和“否”，“开”和“关”。为此，JavaScript 中有一个_布尔_值，只能有两个值：真和假，写做 true（真）和 false（假）。

### Comparison

{{index comparison}}

下面是一种可以得到布尔值的情况：

```
console.log(3 > 2)
// → true
console.log(3 < 2)
// → false
```

{{index [comparison, "of numbers"], "> operator", "< operator", "greater than", "less than"}}

`>`和`<`是两个传统符号，分别用来表示“大于”和“小于”。它们是二元操作。通过它们可以得到一个布尔值，从而知道结果是否正确。

字符串也可以用相同的方法进行比较。

```
console.log("Aardvark" < "Zoroaster")
// → true
```

{{index [comparison, "of strings"]}}

尽管字符串是按字母顺序排列的，但和字典中的顺序并不完全相同。大写字母永远排在小写字母后面，即`"Z" < "a"`。而且非字母的字符（比如标点符号）也在排列顺序中。在字符串排序中，JavaScript 会从左到右依次比较每一个((Unicode))。

{{index equality, ">= operator", "<= operator", "== operator", "!= operator"}}

其他相似的操作包括：`>=`（大于或等于），`<=`（小于或等于），`==`（等于），和`!=`（不等于）。

```
console.log("Itchy" != "Scratchy")
// → true
console.log("Apple" == "Orange")
// → false
```

{{index [comparison, "of NaN"], NaN}}

在 JavaScript 中，只有一个值不等于它自身。那就是`NaN`（不是数字）。

```
console.log(NaN == NaN)
// → false
```

`NaN`理论上表示一个不荒谬的运算，所以它也等于任何_其他_荒谬的运算。

### Logical operators

{{index reasoning, "logical operators"}}

还有一些可以运用在布尔值上的操作。JavaScript 支持三种逻辑运算符：_逻辑与_，_逻辑或_，以及_逻辑非_。这些可以用来“推理”出布尔值。

{{index "&& operator", "logical and"}}

`&&`是_逻辑与_操作符。它是一个二元操作，而且返回的结果只有在两个值都是真的情况下才会是真。

```
console.log(true && false)
// → false
console.log(true && true)
// → true
```

{{index "|| operator", "logical or"}}

`||`是_逻辑或_操作符。它的返回结果在任意值是真的情况下皆为真。

```
console.log(false || true)
// → true
console.log(false || false)
// → false
```

{{index negation, "! operator"}}

_逻辑非_是用感叹号（`!`）表示的。这是一个一元操作，它会转换原来的值：`!true`（真）是`false`（假），而`!false`（假）的结果是`true`（真）。

{{index precedence}}

在布尔值和其他运算操作混搭的时候，括号有时至关重要。实际上，我们所见到的所以运算符中，`||`的运算顺序排在最下面，其次是`&&`，之后是对比符号（`>`、`==` 等），再之后是其余的符号。这个顺序的选择是为了在一般运算中，尽可能避免用到括号：

```
1 + 1 == 2 && 10 * 10 > 50
```

{{index "conditional execution", "ternary operator", "?: operator", "conditional operator", "colon character", "question mark"}}

最后一个逻辑运算符不是一元，不是二元，而是三元：需要三个值的操作。它同时需要一个问号和一个冒号，如下所示：

```
console.log(true ? 1 : 2);
// → 1
console.log(false ? 1 : 2);
// → 2
```

这是一个_条件_运算符（有时也叫做_三元_运算符，因为它是唯一需要三个值的操作）。在问号左边的有值选择剩下两个值的权利。如果是真的，它会选中间的值，否则它会选最右边的值。

## Empty values

{{index undefined, null}}

JavaScript 中还有两个特殊的值，`null`和`undefined`。它们用来表示缺席一个_有意义_的值。它们本身虽然也是一个值，但是不具有任何信息意义。

需要操作在无法得出有意义的值时（后面会遇到许多），都会因为必须得出_某些_值而返回`undefined`。

然而`undefined`和`null`的区别是 JavaScript 设计本身的一个错误。一般情况下，可以忽略不计。纵使在个别情况下，需要在意这两个值的不同时，我也建议尽可能把它们看作一体，混淆来用。

## Automatic type conversion

{{index NaN, "type coercion"}}

开篇时，我说过 JavaScript 几乎接收任何程序，即使程序本身很奇怪。下面的例子完美的展示了这一点：

```
console.log(8 * null)
// → 0
console.log("5" - 1)
// → 4
console.log("5" + 1)
// → 51
console.log("five" * 2)
// → NaN
console.log(false == 0)
// → true
```

{{index "+ operator", arithmetic, "* operator", "- operator"}}

当一个操作运用在错误的值上时，JavaScript 会尽可能根据一些条件转换这个值的类型，从而得到一个你意想不到的结果。这就是_((强制类型转换))_。第一个例子中的`null`变成了`0`，第二个例子中的`"5"`变成了`5`（从字符串变成了数字）。然而第三个例子中的`+`选择连接字符串而不是加法，所以`1`变成了`"1"`（从数字变成字符串）。

{{index "type coercion", [number, "conversion to"]}}

当任何无法被轻易转变成数字的值（比如上面的`"five"`和`undefined`）被转换成数字时，你会得到`NaN`。任何在`NaN`上的运算都会得到`NaN`。因此，如果你发现你意外得到一个`NaN`，可以仔细检查是否有被强制类型转换的值。

{{index null, undefined, [comparison, "of undefined values"], "== operator"}}

在用`==`对比同一类型的值时，结果很容易预测：除了`NaN`意外，只要两个值相等，就是真。但如果是不同的两个类型，JavaScript 又会通过一系列莫名其妙的转换来决定结果。大部分时间，它尽可能把其中一个值的类型转换成另一个值的类型。但如果任意一个转换后得`null`或者`undefined`的值，结果只有在另一边也是`null`或者`undefined`时才是真。

```
console.log(null == undefined);
// → true
console.log(null == 0);
// → false
```

一般情况下，这一点很有用。比如你想确认一个值有意义，而不是`null`或者`undefined`，你就可以用它和`null`相比较（`==`或者`!=`）。

{{index "type coercion", [Boolean, "conversion to"], "=== operator", "!== operator", comparison}}

但如果你想知道一件东西的精确值是不是`false`呢？`0 == false`和`"" == false`在强制类型转换下的结果都是真。但如果你_不_想被强制类型转换时，你可以用`===`或者`!==`操作。第一个确保两件东西的_精确_值相等，而第二个确保它们的精确值不等。因此`"" === false`是假的。

我建议用三个字符的对比符号，这样可以避免不必要的类型转换。当然，你如果确信两个值的类型一致的话，也可以使用两个字符的对比符号。

### Short-circuiting of logical operators

{{index "type coercion", [Boolean, "conversion to"], operator}}

逻辑运算符`&&`和`||`对不同类型的值的方式也很奇怪。它们首先会把左边的值强制转换成布尔值后，在决定如何继续。它们会返回_原始_的左边值或者右边值。

{{index "|| operator"}}

比如，如果左边的值在转换后是真的，`||`会直接返回左边的值。不如就返回右边的值。这一点，无论是对布尔值或者其他类型的值都一样。

```
console.log(null || "user")
// → user
console.log("Agnes" || "user")
// → Agnes
```

{{index "default value"}}

我们可以用此方法来保证一个默认值。对于一个可能为空的值，只需在它后面放`||`和默认值。如果转换后的到的值是假的，你会直接得到默认值。只有`0`、`NaN`和空字符串（`""`）在转换成布尔值后是假的。其他都是真的。所以`0 || -1`得`-1`，`"" || "!?"`得`"!?"`。

{{index "&& operator"}}

`&&`的逻辑相似，但决定方式相反。在左边的值是假的时候，它返回左边的值。否则它将返回右边的值。

还需注意的一点是，这两个操作只有在必须的情况下，才会转换右边的值。比如 `true || X`，不管 X 是什么，纵使它对程序本身百害而无一益，因为结果是真，所以`X`不会被运转。同理，如果是`false && X`的话，因为是假，所以`X`也被无视了。这就是_((短路计算))_。

{{index "ternary operator", "?: operator", "conditional operator"}}

条件运算符的工作原理类似。只有被选则的第二或第三个值才会被执行。

## Summary

我们本章看了 JavaScript 中四个不同类型的值：数字，字符串，布尔，和不存在的值。

这些值是通过它们自身的名字（`true`，`null`）或者值（`13`，`"abc"`）而决定的。你可以通过不同操作来改变这些值。我们还接触了数学运算（`+`，`-`，`*`，`/`，和`%`），字符串连接（`+`），对比（`==`，`!=`，`===`，`!==`，`<`，`>`，`<=`，`>=`），和逻辑（`&&`，`||`），还有许多一元操作（`-`负数符号，`!`逻辑非，和`typeof`查询值的类型），以及三元操作（`?:`）通过第三个值进行二选一。

以上内容足以让你把 JavaScript 当中掌声计算机。[下一章](program_structure)我们会通过这些表达式写基本的程序。
