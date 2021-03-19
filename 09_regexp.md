# 正则表达式

{{quote {author: "Jamie Zawinski", chapter: true}

有的人遇到了一个问题的时候，心想说 “我知道了，这时候得用正则表达式。” 现在他们的问题又多了一个。

quote}}

{{index "Zawinski, Jamie"}}

{{if interactive

{{quote {author: "码渊大师", title: "编程通鉴", chapter: true}

码渊说过，“逆其纹路而伐木，其力也费矣。逆其纹理而编程，其代码也冗余矣。”

quote}}

if}}

{{figure {url: "img/chapter_picture_9.jpg", alt: "A railroad diagram", chapter: "square-framed"}}}

{{index evolution, adoption, integration}}

经年累月，编程((工具))和技术在一种混乱的、优胜劣汰的方式中得以生存和普及。最终胜出的往往并非总是美观或出彩的技术，而是那些在小范围内非常实用、或是与其他良好的技术很契合的技术。

{{index "domain-specific language"}}

那么在这一章节中，我将会讨论的工具正是如此，它叫做 _((正则表达式))_。正则表达式用于描述字符串数据的((样式))。它们在 JavaScript 以及很多其他的编程语言、系统中都构成了一种小巧、单独的语言。

{{index [interface, design]}}

它们的语法是神秘的，而且 JavaScript 为其提供的程序接口很笨拙。然而，在查阅和处理字符串时，它们却是强有效的((工具))。正确理解正则表达式会让你成为一个更有效率的程序员。

## 创建一个正则表达式

{{index ["regular expression", creation], "RegExp class", "literal expression", "slash character"}}

正则表达式是一种对象。我们可以用构造器 `RegExp` 来构造它，或者把要搜索的字符串样式包含在正斜杠 (`/`) 字符中。 

```
let re1 = new RegExp("abc");
let re2 = /abc/;
```

以上这两个正则表达式对象表示着相同的字符串((样式))：一个 _a_ 字符，跟着是一个 _b_ 字符和一个 _c_ 字符。

{{index ["backslash character", "in regular expressions"], "RegExp class"}}

当我们用 `RegExp` 构造器的时候，样式是用普通字符串写成的，所以通用规则适用于反斜杠（\）。

{{index ["regular expression", escaping], [escaping, "in regexps"], "slash character"}}

而在第二种写法中，当样式被写在两个斜杠之间时，对反斜杠的处理则有所不同。首先，我们知道正斜杠用来标记一个样式的结束，如果我们需要匹配带有正斜杠的样式，那就需要在正斜杠前加一个反斜杠。其次，不属于特殊符号（如`\n`）的反斜杠会被_保留_，而不是像在字符串中被忽略掉那样，从而更改样式的含义。在正则表达式中，像问号或者加号这样的字符有特殊的含义，如果要表示问号或加号它们本身的字符的话则需在字符前加上反斜杠。


```
let eighteenPlus = /eighteen\+/;
```

## 测试字符串与目标样式是否匹配

{{index matching, "test method", ["regular expression", methods]}}

正则表达式对象有不少方法可供使用。最简单的是 `test` 方法。
如果你将一段字符串传递给这个方法，它会返回一个布尔值告诉你该字符串中是否有与目标样式相匹配的部分。

```
console.log(/abc/.test("abcde"));
// → true  样式与字符串相匹配
console.log(/abc/.test("abxde"));
// → false  样式与字符串不匹配
```

{{index pattern}}

一个不包含任何特殊字符的正则表达式仅表示一段字符。如果我们要测试一段字符串中是否出现了 _abc_（不单是在字符串的起始位置而已），而该字符串中的任意位置若真的有 _abc_ 这样一段字符，那么 `test` 会返回 `true`。

## 字符集

{{index "regular expression", "indexOf method"}}

其实，若只想确认一个字符串里是否含有 _abc_ 的话，我们调用 `indexOf` 就足够了。正则表达式让我们可以表达更为复杂的((样式))。

比方说我们想匹配任何((数字))。在正则表达式中，把一个((集))的字符置于方括号之间会让该表达式对存在于两个方括号之间的任何一个字符进行匹配。

以下两个表达式皆可对所有含一个((数字))的字符串进行匹配：

```
console.log(/[0123456789]/.test("in 1992"));
// → true
console.log(/[0-9]/.test("in 1992"));
// → true
```

{{index "hyphen character"}}

在两个方括号之间，若两个字符间出现一个连字符 `-` 则表示一整个((范围))内的字符，其排序由字符的((统一码))决定。字符 0 到 9 在统一码的排序(代码 48 至 57)里彼此相邻，所以 `[0-9]` 涵盖了它们，并可以匹配所有的((数字))。

{{index [whitespace, matching], "alphanumeric character", "period character"}}

有的常用字符组有它们内置的快捷写法。数字是其中之一：`\d` 与 `[0-9]` 效果相同。

{{index "newline character", [whitespace, matching]}}

{{table {cols: [1, 5]}}}

| `\d`    | 任何((数字))字符
| `\w`    | 字母或数字字符("((单词字符))")
| `\s`    | 任何空白字符（空格，制表符，换行符及类似字符）
| `\D`    | _不是_ 数字的字符
| `\W`    | 不是字母或数字的字符
| `\S`    | 非空白字符
| `.`     | 除换行符以外的字符

因此你可以以下表达式来匹配 ((日期)) 和 ((时间)) 格式，例如 01-30-2003 15:20:

```
let dateTime = /\d\d-\d\d-\d\d\d\d \d\d:\d\d/;
console.log(dateTime.test("01-30-2003 15:20"));
// → true
console.log(dateTime.test("30-jan-2003 15:20"));
// → false
```

{{index ["backslash character", "in regular expressions"]}}

然而这个表达式看起来糟糕透顶，对吧？其中反斜杠占了得有一半，使人难以找出内在的((样式))。我们[稍后](regexp#date_regexp_counted)会学习这个表达式的略微改良版本。

{{index [escaping, "in regexps"], "regular expression", set}}

这些带反斜杠的代码也可以被放进((方括号))里使用。比如说，`[\d.]` 表示任何数字或一个句点。值得注意的是，在方括号里的句点其实失去了其特殊的意义。其他的特殊符号亦是如此，比如加号 `+`。

{{index "square brackets", inversion, "caret character"}}

若是想要 _倒置_ 一些字符的话，换句话说，你如果想匹配任何 _除了_ 字符集中的字符之外的字符，可以在左方括号之后加上一个插入符(`^`)。

```
let notBinary = /[^01]/;
console.log(notBinary.test("1100100010100110"));
// → false
console.log(notBinary.test("1100100010200110"));
// → true
```

## 样式里重复的部分

{{index ["regular expression", repetition]}}

我们现在知晓了如何对单个数字进行匹配，但如果我们想要匹配整数——一个或多个((数字))的((序列))呢？

{{index "plus character", repetition, "+ operator"}}

当你在一个正则表达式的元素后面加入一个加号 (`+`) ，这说明该表达式中的元素可以出现不止一次而已。因此，`/\d+/` 可以匹配 1 个或多个数字字符。

```
console.log(/'\d+'/.test("'123'"));
// → true
console.log(/'\d+'/.test("''"));
// → false
console.log(/'\d*'/.test("'123'"));
// → true
console.log(/'\d*'/.test("''"));
// → true
```

{{index "* operator", asterisk}}

星号(`*`)有着与加号相似的作用，但区别在于星号还可以允许匹配次数为 0 的样式。意思是说只要后缀有星号，那就肯定会被匹配上——如果匹配不到合适的文本，且就当作匹配次数为 0 。

{{index "British English", "American English", "question mark"}}

使用问号则会让部分样式变得 _((可有可无))_，这意味着该样式可以出现 0 次或者 1 次。在以下例子中，_u_ 字符可以出现，但即使它不出现的话该样式同样可以匹配成功。 

```
let neighbor = /neighbou?r/;
console.log(neighbor.test("neighbour"));
// → true
console.log(neighbor.test("neighbor"));
// → true
```

{{index repetition, [braces, "in regular expression"]}}

为了精确指定一个样式能出现的次数，使用大括号。比方说，在一个元素后面加上 `{4}`， 则意味着该元素只能出现4次。我们也可以用这种方式指定一个 ((范围))：`{2,4}` 意味着该元素必须最少出现两次，最多不超过四次。

{{id date_regexp_counted}}

这是之前提到的表示((日期))和((时间))样式的改良版本，允许使用个位数和十位数来表示日、月、时。这种写法也稍稍更容易被解读一些。

```
let dateTime = /\d{1,2}-\d{1,2}-\d{4} \d{1,2}:\d{2}/;
console.log(dateTime.test("1-30-2003 8:45"));
// → true
```

在使用大括号时，你也可以省略逗号之后的数字来表示一个开放的((范围))。因此，`{5,}` 意味着 5 次及 5 次以上。

## 对子表达式进行组合

{{index ["regular expression", grouping], grouping, [parentheses, "in regular expressions"]}}

想要一次性对多个元素使用类似 `*` 或 `+` 这样的操作符的话，你需要用括号。被括号括起来的部分正则表达式会被其后出现的操作符视为单独的元素。

```
let cartoonCrying = /boo+(hoo+)+/i;
console.log(cartoonCrying.test("Boohoooohoohooo"));
// → true
```

{{index crying}}

在以上表达式中，第一个和第二个 `+` 符号只对 _boo_ 和 _hoo_ 中的第二个的 _o_ 有效。第三个 `+` 则适用于 `(hoo+)` 这个组，匹配一个或多个类似的序列。

{{index "case sensitivity", capitalization, ["regular expression", flags]}}

这个例子中正则表达式末尾的 `i` 意味着它不区分大小写。即使该目标样式本身都是小写字母组成，也可以与输入字符串中大写的 _B_ 匹配。

## 匹配项与子表达式组

{{index ["regular expression", grouping], "exec method", [array, "RegExp match"]}}

想要对正则表达式进行匹配，采用 `test` 方法无疑是最简单的。该方法只告诉你是否匹配，仅此而已。正则表达式还有一个叫做 `exec` (execute执行)的方法，它会在未找到匹配项时返回 `null`，而在找到匹配项时返回一个关于匹配信息的对象。 

```
let match = /\d+/.exec("one two 100");
console.log(match);
// → ["100"]
console.log(match.index);
// → 8
```

{{index "index property", [string, indexing]}}

从 `exec` 返回的对象有一个 `index` 属性，该属性告诉我们找到的匹配项起始于字符串中的 _位置_。除此之外，这对象看起来像（实际上就是）一个字符串数组，其第一个元素是匹配成功的字符串。 在上一个示例中，
正是我们想要寻找的 ((数字)) 序列。（译者注：100）

{{index [string, methods], "match method"}}

字符串值有一个效果与之相似的 `match` 方法。

```
console.log("one two 100".match(/\d+/));
// → ["100"]
```

{{index grouping, "capture group", "exec method"}}

当正则表达式包含以括号分组的子表达式时，与这些组相匹配的文本也会出现在返回的数组中。整个匹配项总是数组的第一个元素，下一个元素是被第一个子表达式组(在表达式中左边第一个括号)匹配的部分，接着是第二组，以此类推。

```
let quotedText = /'([^']*)'/;
console.log(quotedText.exec("she said 'hello'"));
// → ["'hello'", "hello"]
```

{{index "capture group"}}

当一个子表达式组从头到尾都没有被匹配上的话(比如说后面有一个问号)，那么它在输出数组的位置的值会是 `undefined`。同样地，若一个组被多次成功匹配，只有最后一个匹配项会被放进输出的数组中。

```
console.log(/bad(ly)?/.exec("bad"));
// → ["bad", undefined]
console.log(/(\d)+/.exec("123"));
// → ["123", "3"]
```

{{index "exec method", ["regular expression", methods], extraction}}

子表达式组在提取字符串的某一部分的时候很有用。如果我们不单单想要验证一个字符串是否包含一个((日期))， 而且还想提取这个日期并构造一个代表它的对象，那么我们可以给数字样式用括号括起来，然后通过 `exec` 返回的结果把日期提出来。

然而现在我们先来绕一点远路，来讨论 JavaScript 表示日期和((时间))值的内置方法。

## Date 类

{{index constructor, "Date class"}}

JavaScript有一个标准类专门用于表达((日期))，或者更准确来说，表达((时间))点。这个类叫做 `Date`。如果你直接用关键词 `new` 创建一个新的 Date 对象，便可以得到当下的日期和时间。

```{test: no}
console.log(new Date());
// → Mon Nov 13 2017 16:19:11 GMT+0100 (CET)
```

{{index "Date class"}}

你还可以创建特定一段时间的 Date 对象。

```
console.log(new Date(2009, 11, 9));
// → Wed Dec 09 2009 00:00:00 GMT+0100 (CET)
console.log(new Date(2009, 11, 9, 12, 59, 59, 999));
// → Wed Dec 09 2009 12:59:59 GMT+0100 (CET)
```

{{index "zero-based counting", [interface, design]}}

在 JavaScript 的传统中，月份是从 0 开始算的（所以十二月对应的月份数是 11），但天数却是从 1 开始。这确实有点奇葩，容易混淆，要当心。

在 Date 对象末尾的四个参数（小时，分钟，秒和毫秒）是可选的。如果未指定，则将其设为零。

{{index "getTime method"}}

在世界协调时间((时区))的框架下，自1970年以来所经过的毫秒数被储存为时间戳。这遵循了 1970 年左右被发明的"((Unix 时间))" 所定下的传统。对于 1970 年之前的时间，你可以用负数来表示。Date 对象的 `getTime` 方法可以返回时间戳的数值，可以想象，这个数会很大。

```
console.log(new Date(2013, 11, 19).getTime());
// → 1387407600000
console.log(new Date(1387407600000));
// → Thu Dec 19 2013 00:00:00 GMT+0100 (CET)
```

{{index "Date.now function", "Date class"}}

如果你给 `Date` 构造器指定单个参数，那该参数会被视为毫秒数。你可以通过创建一个新的 `Date` 对象并调用该对象的 `getTime` 方法来得到当下的毫秒计数，或者调用 `Date.now` 函数。

{{index "getFullYear method", "getMonth method", "getDate method", "getHours method", "getMinutes method", "getSeconds method", "getYear method"}}

Date 对象提供诸如 `getFullYear`, `getMonth`,
`getDate`, `getHours`, `getMinutes`, 及 `getSeconds` 这些方法来提取其组件。除了 `getFullYear` 之外，还有一个基本上没人用的 `getYear` 方法，它可以返回当前年份减去 1900 的值(`98` 或 `119`)。


{{index "capture group", "getDate method", [parentheses, "in regular expressions"]}}

一旦给表达式中我们感兴趣的部分添上括号，便可以从字符串创建一个日期对象。

```
function getDate(string) {
  let [_, month, day, year] =
    /(\d{1,2})-(\d{1,2})-(\d{4})/.exec(string);
  return new Date(year, month - 1, day);
}
console.log(getDate("1-30-2003"));
// → Thu Jan 30 2003 00:00:00 GMT+0100 (CET)
```

{{index destructuring, "underscore character"}}

`_` (下划线)绑定会被忽略，它的作用是跳过 `exec` 返回的数组中第一个含完整匹配项的元素。

## 单词边界与字符串边界

{{index matching, ["regular expression", boundary]}}

麻烦的事情在于，`getDate` 对字符串 `"100-1-30000"` 中像 00-1-3000 这样无意义的日期也会欣然提取。匹配可以发生在字符串任何一个位置，所以在这种情况下，匹配会从第二个字符起始至倒数第二个字符为止。

{{index boundary, "caret character", "dollar sign"}}

如果我们要强制让匹配项拥有整个字符串的长度，那我们可以添加 `^` 和 `$` 这两个标记。插入符标志着输入字符串的起始位置，而美元符号标志其结束位置。因此，`/^\d+$/` 会匹配完全由一个或多个数字组成的字符串，`/^!/` 会匹配任何以感叹号开头的字符串， 而 `/x^/` 则无法匹配任何字符串(在字符串开始之前不可能有 _x_)。

{{index "word boundary", "word character"}}

另一方面，如果我们只想确保一个日期的开始和结束都在单词边界上，那可以使用 `\b` 这个标记。一个单词边界可以是字符串的开头或结尾，也可以是字符串中的任何一个点，前提是那个点一侧有单词符号(比如 `\w`)， 且另一侧的字符不是字母或数字。

```
console.log(/cat/.test("concatenate"));
// → true
console.log(/\bcat\b/.test("concatenate"));
// → false
```

{{index matching}}

值得注意的是，边界标记与实际字符不会匹配。它只是强行让正则表达式在特定条件下匹配那些出现在样式中的位置。

## 选择样式

{{index branching, ["regular expression", alternatives], "farm example"}}

假设我们不仅想要知道一段文本有没有包含一个数字，而且要知道这个数字后面是否还跟着 _pig_, _cow_, 或 _chicken_ 这三个单词的其中一词，或者是它们的复数形式。

我们可以写三个正则表达式并依次进行测试，然而有更好的办法。((竖线符))(`|`)表示在竖线的左边和右边的样式都可以成为((选择))。所以我可以这样写：

```
let animalCount = /\b\d+ (pig|cow|chicken)s?\b/;
console.log(animalCount.test("15 pigs"));
// → true
console.log(animalCount.test("15 pigchickens"));
// → false
```

{{index [parentheses, "in regular expressions"]}}

括号可以用来限制竖线操作符所针对的那部分样式，而且你可以通过放置多个相邻的竖线操作符来表示多于两个选项的选择。

## 匹配机制

{{index ["regular expression", matching], [matching, algorithm], "search problem"}}

从概念上来说，当你使用 `exec` 或 `test` 方法时，正则表达式的引擎会在你的字符串中寻找匹配项。表达式会从字符串的第一个字母开始匹配，然后第二个字母，一直到找到匹配项或字符串的末尾为止。它要么会返回第一个匹配成功的匹配项，要么就找不到任何的匹配项。

{{index ["regular expression", matching], [matching, algorithm]}}

在实际完成匹配的过程时，引擎会像以下的((流程图))所展示的那样来处理正则表达式。下图展示了之前我们举过的牲畜表达式的例子：

{{figure {url: "img/re_pigchickens.svg", alt: "Visualization of /\\b\\d+ (pig|cow|chicken)s?\\b/"}}}

{{index traversal}}

如果我们可以在图中从左至右找出一条路径，那便意味着表达式匹配成功。我们会在字符串中保留一个当前的位置，且并且每当我们通过一个方框时，我们都会验证当前位置之后的字符串是否与该框中的内容匹配。

所以说当我们想从第 4 个位置开始匹配 `"the 3 pigs"` 的话，这个匹配的过程通过流程图看起来会是这样：

 - 在第 4 个位置(译者注：从 0 开始，t 的位置为 0 ，h 的位置为1， 以此类推)， 有一个单词((边界))， 所以我们可以通过第一个方框。
 (译者注：3 是数字，字母和数字都属于单词符号，另一侧是空格，所以这里算作单词边界)

 - 还是在第 4 个位置， 我们找到的是数字，所以我们可以顺利通过第二个方框。

 - 在第 5 个位置， 一条路径绕回了第二个((数字))框之前，而另一条路径通向一个仅包含空格符的框。这个位置上有一个空格，而不是数字，所以我们选第二条路径继续走。

 - 现在我们来到第 6 个位置(_pigs_ 的开头)，看见到了一个三岔口。我们并没有看见 _cow_ 或者 _chicken_， 但我们看见了 _pig_ 这个单词，所以我们朝这条路走。

 - 在第 9 个位置，在三岔路口之后，一条路径绕过了带 _s_ 的框， 而另一条会匹配 _s_。在这个位置有一个 _s_ 字母，而不是单词边界，所以我们会通过这个带 _s_ 的框。

 - 我们现在来到第 10 个位置(字符串的结尾)，而且只能匹配到一个单词((边界))。字符串的结尾算作单词边界，所以我们经过这最后一个方框并对字符串匹配成功。

{{id backtracking}}

## 回溯

{{index ["regular expression", backtracking], "binary number", "decimal number", "hexadecimal number", "flow diagram", [matching, algorithm], backtracking}}

我们来看一个正则表达式：`/\b([01]+b|[\da-f]+h|\d+)\b/`。该表达式有 3 个选择样式，它既可以匹配一个后缀是 _b_ 的二进制数字，也可以匹配一个以 _h_ 为后缀的十六进制数字(以 16 为底，且以字母 _a_ 至 _f_ 表示数字 10 至 15)， 还能匹配一个常规的不带任何后缀的十进制数字。以下是对应的流程图：

{{figure {url: "img/re_number.svg", alt: "Visualization of /\\b([01]+b|\\d+|[\\da-f]+h)\\b/"}}}

{{index branching}}

在匹配此表达式时，即使输入不包含二进制数，进入最上面的(二进制)分支也是常有的事。打个比方，当我们匹配 `"103"` 这个字符串时，直到匹配到 3 的时候我们才会意识到其实身处于错误的分支中(译者注：因二进制数仅含 0 和 1)。这个字符串 _的确_ 与表达式匹配，只是不在当前这条分支罢了。

{{index backtracking, "search problem"}}

因此，匹配器会 _回溯_。当进入岔路口的其中一条分支时，匹配器会记住当前的位置(在这里指字符串的开头，即在图中刚经过第一个边界框)，以便在当前这条支路走不通的情况下返回该位置并尝试另一条支路。对于 `"103"` 这个字符串来说，当遇见 3 这个数字时，匹配器会尝试走十六进制那条支路，然后因为数字后缀没有 _h_ 而再次匹配失败。之后匹配器尝试进入十进制数字的分支，这回终于匹配上了，并且会报告匹配项。 

{{index [matching, algorithm]}}

一旦找到完全匹配项，匹配器就会停下。这意味着即使存在多条可以匹配同一个字符串的分支，只有第一条分支(根据分支在正则表达式中出现的位置排序)会被通过。

回溯也会发生在 ((重复)) 操作符上，例如 `+` 和 `*`。当你使用表达式 `/^.*x/` 对 `"abcxe"` 字符串进行匹配时，`.*` 会先试图比对整个字符串。然后，匹配引擎会意识到还需要一个 _x_ 才能匹配目标样式。由于该字符串的结尾没有后缀 _x_，星号操作符会尝试少匹配一个字符。但是匹配器在 `abcx` 的后缀也没有找到 _x_， 所以它会再次回溯，用星号操作符单去匹配 `abc`。_现在_ 它终于在需要的地方成功找到了 _x_ ，并且报告了从位置 0 到位置 4 的匹配项。

{{index performance, complexity}}

我们有可能会写出需要回溯 _很多次_ 的正则表达式。当一个目标样式能用多种方式匹配同一段输入文本的时候，这个问题就会出现。比方说我们稀里糊涂地写二进制数的正则表达式时，可能会写成 `/([01]+)+b/`。

{{figure {url: "img/re_slow.svg", alt: "Visualization of /([01]+)+b/",width: "6cm"}}}

{{index "inner loop", [nesting, "in regexps"]}}

如果这个表达式试图匹配一长串末尾不带 _b_ 字符的零和一的话，匹配器首先会通过内部循环直到所有数字都被迭代完。然后，匹配器意识到并没有 _b_， 所以回溯到前一个位置，通过一次外部循环，然后再次放弃，接着试图再次回溯到内部循环。它会继续在这两个循环中尝试所有可能的路线，这意味着每增加一个字符，工作量将会 _翻倍_。即使只是针对几十个字符，该匹配基本上永远都无法结束。

## replace 方法

{{index "replace method", "regular expression"}}

字符串值有一个 `replace` 方法可以用来将部分字符串由另外一个字符串替换。

```
console.log("papa".replace("p", "m"));
// → mapa
```

{{index ["regular expression", flags], ["regular expression", global]}}

该方法的第一个参数也可以是一个正则表达式，在这种情况下，它的第一个匹配项会被替换。当 `g` 选项(代表 _global_ 全局)被添加到正则表达式中，字符串中 _所有_ 的匹配项都会被替换，而不仅仅是第一个匹配项。

```
console.log("Borobudur".replace(/[ou]/, "a"));
// → Barobudur
console.log("Borobudur".replace(/[ou]/g, "a"));
// → Barabadar
```

{{index [interface, design], argument}}

要想在替换单个匹配项以及替换全部匹配项之间进行选择，比较合理的办法是给 `replace` 方法增加一个参数，或者提供另一个名为 `replaceAll` 的方法。然而，基于某些不幸的原因，替换单个匹配项或全部匹配项的选择仍需依靠正则表达式的属性来决定。  

{{index grouping, "capture group", "dollar sign", "replace method", ["regular expression", grouping]}}

在 `replace` 方法中使用正则表达式的真正妙处在于，我们可以在替换字符串时对匹配项进行指代。举个例子，假设我们有一个包含人名的长字符串，每行有一个人名，其格式为 `Lastname姓, Firstname名`。如果我们想要互换名和姓的位置并且去除分隔它们的逗号，最后想要得到 `Firstname名 Lastname姓` 这样的格式的话，可以写出以下代码： 

```
console.log(
  "Liskov, Barbara\nMcCarthy, John\nWadler, Philip"
    .replace(/(\w+), (\w+)/g, "$2 $1"));
// → Barbara Liskov
//   John McCarthy
//   Philip Wadler
```

替换字符串的 `$1` 和 `$2` 指代样式中被括号括起来的子表达式组。`$1` 会被与第一组相匹配的文本所替换，`$2` 被与第二组相匹配的文本替换，以此类推，最多可指代到 `$9`。整个匹配项可以被 `$&` 指代。

{{index [function, "higher-order"], grouping, "capture group"}}

我们也可以传递一个函数（而不是字符串）作为 `replace` 的第二个参数。对于每一次替换，匹配到的子表达式组(以及所有匹配项)会作为参数来调用该函数，其返回值会被插入到新字符串中。

这有个小小的例子：

```
let s = "the cia and fbi";
console.log(s.replace(/\b(fbi|cia)\b/g,
            str => str.toUpperCase()));
// → the CIA and FBI
```

这有个更好玩的例子：

```
let stock = "1 lemon, 2 cabbages, and 101 eggs";
function minusOne(match, amount, unit) {
  amount = Number(amount) - 1;
  if (amount == 1) { // only one left, remove the 's'
    unit = unit.slice(0, unit.length - 1);
  } else if (amount == 0) {
    amount = "no";
  }
  return amount + " " + unit;
}
console.log(stock.replace(/(\d+) (\w+)/g, minusOne));
// → no lemon, 1 cabbage, and 100 eggs
```

这个表达式会针对一个字符串，然后找出字符串中所有后缀为字母或数字单词的数字，并返回一个所有数字都减 1 的字符串。

`(\d+)` 子表达式组会成为该函数的 `amount` 参数，并且 `(\w+)` 组会被 `unit` 绑定。该函数会将 `amount` 转换成一个数字 —— 这总会行得通，因为它与 `\d+` 相匹配 —— 而且还会针对数字是零或一的情况下做一些微调。

## 贪心

{{index greed, "regular expression"}}

我们可以尝试用 `replace` 写出除去一段 JavaScript ((代码))中的所有((注释))的函数。第一次尝试如下：

```{test: wrap}
function stripComments(code) {
  return code.replace(/\/\/.*|\/\*[^]*\*\//g, "");
}
console.log(stripComments("1 + /* 2 */3"));
// → 1 + 3
console.log(stripComments("x = 10;// ten!"));
// → x = 10;
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1  1
```

{{index "period character", "slash character", "newline character", "empty set", "block comment", "line comment"}}

在 _or_ 操作符之前的部分会匹配两个正斜杠以及尾随其后的任意数量的非换行字符。针对多行注释的部分更复杂一些，我们用 `[^]` (任何不在空字符集中的字符) 来匹配任意字符。我们不能在此处使用句点，因为块注释可以换行，而且句点符号并不匹配换行符号。

然而，最后一行的输出看起来是错的，为什么呢？

{{index backtracking, greed, "regular expression"}}

就像我之前在回溯的章节所提到的那样，表达式中的 `[^]*` 部分首先会贪得无厌地去匹配。如果这导致了样式的下一部分匹配失败，匹配器将后退一个字符然后从那里再次尝试。在示例中，匹配器会先尝试匹配整个字符串的剩余部分，然后从那里开始后退。它会在后退 4 个字符之后发现 `*/` 的出现并与之匹配。这并不是我们的本意 —— 我们本想对单个注释进行匹配，而不是一直到底部的代码去寻找最后一个块注释的结尾。

正因为这样的行为，我们把重复操作符 (`+`, `*`, `?`, 及 `{}`) 定义为 _((贪心))的_，意味着它们会贪得无厌地去匹配，并且在那之后回溯。如果你放一个((问号))在它们后面 (`+?`, `*?`, `??`, `{}?`)，它们将变得不再贪心，而开始步步为营地去匹配，只会在小范围内的剩余样式没有匹配项时才会扩大匹配范围。 

而这正是我们在这个示例中想要达到的效果，我们让星形符号匹配到 `*/` 之前的最小段字符，这样仅仅针对单个块注释而已。

```{test: wrap}
function stripComments(code) {
  return code.replace(/\/\/.*|\/\*[^]*?\*\//g, "");
}
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1 + 1
```

((正则表达式))的程序中有很多((bug))的成因都可以追溯到无意间对贪心操作符的使用，而其非贪心的形式可以达到更好的效果。当我们使用((重复))操作符时，应优先考虑非贪心的形式。

## 动态地创建 RegExp 对象

{{index ["regular expression", creation], "underscore character", "RegExp class"}}

在有的情况下，你在写代码的时候可能还不知道具体需要匹配的((样式))是什么。比如说你想在一段文本中查找用户的名字，而且想给它加上下划线以显得突出。由于你只有等到该程序真正运行的时候才会知道名字，所以不能采用基于正斜杠的写法。

但你却可以构建一个字符串并对其使用 `RegExp` ((构造器))。以下有一个示例：

```
let name = "harry";
let text = "Harry is a suspicious character.";
let regexp = new RegExp("\\b(" + name + ")\\b", "gi");
console.log(text.replace(regexp, "_$1_"));
// → _Harry_ is a suspicious character.
```

{{index ["regular expression", flags], ["backslash character", "in regular expressions"]}}

我们写 `\b` ((边界)) 标记的时候需要两个反斜杠，因为我们是把它放在普通的字符串里写，而不是在正斜杠括起来的正则表达式里。传递给 `RegExp` 的第二个参数包含了正则表达式的选项 —— 在这个示例中，`"gi"` 意味着全局范围且不区分大小写。

但如果我们的用户是个会把名字写成 `"dea+hl[]rd"` 的((愣头青))呢？那将会形成一个荒谬的、无法与用户名相匹配的正则表达式。

{{index ["backslash character", "in regular expressions"], [escaping, "in regexps"], ["regular expression", escaping]}}

我们可以在含有特殊意义的字符之前加上反斜杠来解决这个问题。

```
let name = "dea+hl[]rd";
let text = "This dea+hl[]rd guy is super annoying.";
let escaped = name.replace(/[\\[.+*?(){|^$]/g, "\\$&");
let regexp = new RegExp("\\b" + escaped + "\\b", "gi");
console.log(text.replace(regexp, "_$&_"));
// → This _dea+hl[]rd_ guy is super annoying.
```

## search 方法

{{index ["regular expression", methods], "indexOf method", "search method"}}

字符串的 `indexOf` 方法不能与正则表达式一起调用。然而有另一个叫 `search` 的方法适用于正则表达式，该方法与 `indexOf` 类似，如果匹配项被找到则会返回第一个匹配项的索引，找不到的话则返回 -1。

```
console.log("  word".search(/\S/));
// → 2
console.log("    ".search(/\S/));
// → -1
```

然而不幸的是，目前并没有办法可以指定从字符串的具体哪一个位置开始匹配(就像 `indexOf` 的第二个参数)，这通常会比较有用。

##  lastIndex 属性

{{index "exec method", "regular expression"}}

`exec` 方法同样没有给我们提供从字符串特定位置开始搜索的简便办法，但它提供了一种*不*简便的办法。

{{index ["regular expression", matching], matching, "source property", "lastIndex property"}}

正则表达式的对象具有属性，其中一个属性叫 `source`，它包含了创建表达式的字符串。另外还有一个叫 `lastIndex` 的属性，它在某些特定情况下可以控制从字符串的哪个位置开始进行下一次匹配。

{{index [interface, design], "exec method", ["regular expression", global]}}

这些特定情况指的是正则表达式必须启用了全局 (`g`) 或者粘性 (`y`) 选项，而且通过 `exec` 方法进行匹配。同样地，我们本可以有更简单明了的解决方案，比如说直接给 `exec` 传递一个额外的参数。但是 JavaScript 正则表达式接口的显著特点之一就是令人摸不着头脑。

```
let pattern = /y/g;
pattern.lastIndex = 3;
let match = pattern.exec("xyzzy");
console.log(match.index);
// → 4
console.log(pattern.lastIndex);
// → 5
```

{{index "side effect", "lastIndex property"}}

在匹配成功的情况下，对 `exec` 的调用将会自动更新 `lastIndex` 属性并使其指向匹配项之后的位置。如果找不到任何匹配项，`lastIndex` 会被调回零，这也是该属性在一个新构建的正则表达式对象中的值。

全局选项与粘性选项的区别在于，当粘性选项被启用时，只有在 `lastIndex` 的位置开始搜索才有可能匹配成功。而在启用全局选项时，匹配器会向前找寻一个可以开始匹配的位置。

```
let global = /abc/g;
console.log(global.exec("xyz abc"));
// → ["abc"]
let sticky = /abc/y;
console.log(sticky.exec("xyz abc"));
// → null
```

{{index bug}}

当我们用同一个正则表达式的值多次调用 `exec` 方法时，对 `lastIndex` 的自动更新会带来麻烦。你的正则表达式可能会意外地从之前调用过的位置开始匹配。

```
let digit = /\d/g;
console.log(digit.exec("here it is: 1"));
// → ["1"]
console.log(digit.exec("and now: 1"));
// → null
```

{{index ["regular expression", global], "match method"}}

全局选项的另一个有意思的效果在于，它改变了字符串上的 `match` 方法的运作方式。当调用全局表达式的时候，`match` 返回的数组不像 `exec` 返回的那样，而是会找到字符串中 _所有_ 样式的匹配项，并且返回一个包含这些匹配项的数组。

```
console.log("Banana".match(/an/g));
// → ["an", "an"]
```

所以说，在使用全局正则表达式的时候需谨慎。在不得不使用它们的情况下 —— 调用 `replace` 或者当你必须使用 `lastIndex` 的时候 —— 通常也只有这些时候你会想要用到它们。

### 在比配中循环

{{index "lastIndex property", "exec method", loop}}

想要获取循环中的匹配项，通常的做法是对字符串中所有出现的目标样式进行扫描。我们可以用 `lastIndex` 和 `exec` 来做到这一点。

```
let input = "A string with 3 numbers in it... 42 and 88.";
let number = /\b\d+\b/g;
let match;
while (match = number.exec(input)) {
  console.log("Found", match[0], "at", match.index);
}
// → Found 3 at 14
//   Found 42 at 33
//   Found 88 at 40
```

{{index "while loop", ["= operator", "as expression"], [binding, "as state"]}}

以上示例巧妙地利用了((赋值))表达式 (`=`) 的值。所以一旦 `match = number.exec(input)` 成为 `while` 循环语句的条件，我们便可以在每一次迭代之初进行匹配，将匹配结果绑定，然后在找不到更多匹配项时停止循环。  

{{id ini}}
## 解析一个 INI 文件

{{index comment, "file format", "enemies example", "INI file"}}

现在，我们来试图解决一个需要调用((正则表达式))的问题来总结这一章的内容。试想一下，我们现在打算写一个可以程序，它可以自动收集我们在((互联网))上仇敌的信息。(开玩笑，我们并不会真的在这里写这样一个程序，只是写其中可以读取((配置))文件的部分而已。)以下是我们的配置文件：

```{lang: "text/plain"}
searchengine=https://duckduckgo.com/?q=$1
spitefulness=9.7

; 分号后面的是注释
; 每一个区块针对一个仇敌
[larry]
fullname=Larry Doe 拉里·兜
type=kindergarten bully 幼儿园小霸王
website=http://www.geocities.com/CapeCanaveral/11451

[davaeorn]
fullname=Davaeorn 达瓦尔
type=evil wizard 邪恶巫师
outputdir=/home/marijn/enemies/davaeorn
```

{{index grammar}}

这个格式(一种很常见的格式，通常被称为 _INI_ 文件)的书写规则如下：

- 空白行或者由分号起头的行会被忽略

- 被方括号 `[` 和 `]` 括起来的行意味着开始一个新的((区块))

- 如果一行含有字母或数字的字符，而且其后缀为 `=` 符号的话，则会为当前的区块增加一条设定。

- 其余的内容都会被视为无效。

我们的任务是把类似于上述示例中的字符串给转换成一个对象，这个对象的属性会包含第一个区块标题之前用作设定的字符串，然后它还有针对区块的子对象，这些子对象包含对每一个区块的设定。

{{index "carriage return", "line break", "newline character"}}

由于这种格式的文件会被逐行处理，将其拆分成单独的行是一个不错的开始。我们在 [第四章](data#split) 里已经学到过 `split` 方法。然而，有的操作系统不单用一个换行符来分行，而是用回车符后跟一个换行符 (`"\r\n"`)。鉴于 `split` 方法也可以将正则表达式作为其参数，我们可以用类似于 `/\r?\n/` 的正则表达式来进行分行，它同时允许 `"\n"` 和 `"\r\n"` 这两种方式。

```{startCode: true}
function parseINI(string) {
  // Start with an object to hold the top-level fields
  let result = {};
  let section = result;
  string.split(/\r?\n/).forEach(line => {
    let match;
    if (match = line.match(/^(\w+)=(.*)$/)) {
      section[match[1]] = match[2];
    } else if (match = line.match(/^\[(.*)\]$/)) {
      section = result[match[1]] = {};
    } else if (!/^\s*(;.*)?$/.test(line)) {
      throw new Error("Line '" + line + "' is not valid.");
    }
  });
  return result;
}

console.log(parseINI(`
name=Vasilis
[address]
city=Tessaloniki`));
// → {name: "Vasilis", address: {city: "Tessaloniki"}}
```

{{index "parseINI function", parsing}}

这些代码会遍历该输入文件的每一行并且构建出一个对象。位于顶部的属性(译者注：即在第一个区块之前的属性)会直接被储存进这个对象，而在区块中的属性则会被存入其包含的区块对象中。`section` 绑定会指向当前区块的对象。

有两种类型的行比较突出 —— 区块的标题行或者属性行。当我们遇到包含常规属性的行时，该属性会被储存在当前的区块中。而当我们遇见区块的标题行，一个新的区块对象则会被创建，且 `section` 将被设置为指向这个新的子对象。

{{index "caret character", "dollar sign", boundary}}

要注意我们重复使用了 `^` 及 `$` 来确保表达式会对整行进行匹配，而不是只匹配该行的一部分而已。如果不用这些操作符，结果可能大部分代码还是行得通，但对于某些输入则会表现得很奇怪，从而带来难以溯源的问题。

{{index "if keyword", assignment, ["= operator", "as expression"]}}

`if (match = string.match(...))` 这个样式与之前我们以赋值作为 `while` 的条件时所用到的技巧相似。你通常不确定对 `match` 的调用可以成功，所以你先用 `if` 语句帮你测试这一点，然后再访问返回的对象。为了不破坏美观的 `else if` 链式，我们把匹配的结果给一个绑定赋值，然后立刻将其赋的值交由 `if` 语句来测试。

{{index [parentheses, "in regular expressions"]}}

如果遇到既不是区块标题行也不是含属性的行，该函数会用 `/^\s*(;.*)?$/` 这个表达式来查看该行是注释还是空行。你看出端倪了吗？被括号括住的部分会与注释匹配，而 `?` 则确保空行也能被匹配上。当一行内容与任何预期的形式都不匹配时，函数会抛出异常。

## 国际字符

{{index internationalization, Unicode, ["regular expression", internationalization]}}

由于 JavaScript 最初较为精简的实现，以及后来这种简化的方针被敲定为((标准))行为的既成事实，其正则表达式对不以英文显示的字符可以说一窍不通。比方说，就 JavaScript 的正则表达式而言，一个 "((单词字符))" 只包含了拉丁字母中的26个字符(含大小写)、十进制数字、以及(由于某些原因)下划线字符。诸如 _é_ 或 _β_ 这样显然算是文字的字符不会被 `\w` 匹配。(它们 _会_ 与非单词字符 `\W` 匹配)

{{index [whitespace, matching]}}

然而，因为奇怪的历史原因，`\s` (空白字符)并没有这个问题，它可以匹配所有 Unicode(译者注：统一码)标准下的任何空白字符，比如说((不间断空格))和((蒙古语元音分隔符))。

另外一个问题在于，默认情况下正则表达式会对码单位起作用(在[第五章](higher_order#code_units)曾讨论过)，而不是直接针对字符。这意味着由双码单位组成的字符的行为会显得有些异常。

```
console.log(/🍎{3}/.test("🍎🍎🍎"));
// → false
console.log(/<.>/.test("<🌹>"));
// → false
console.log(/<.>/u.test("<🌹>"));
// → true
```

我们看到第一行中的 🍎 会被视为两个码单位，而 `{3}` 的部分仅适用于第二个码单位。与之类似地，句点只会匹配一个码单位，而不会匹配由两个码单位构成的玫瑰((表情符号))。

若想让正则表达式妥当处理这样的字符，你需要为其添加一个 `u` 选项(表示 ((Unicode)))。不幸的是，因为一旦把默认的这种错误的行为给修改过来的话，可能会导致依赖它们的代码出现问题，所以这种默认的错误行为仍然存在。

{{index "character category", [Unicode, property]}}

你可以在(启用了 Unicode 选项的)正则表达式中使用 `\p` 来匹配所有 Unicode 标准下有分配特定属性的字符。虽然在撰写本文时，这个方法才刚刚被标准化，尚未得到广泛支持。

```{test: never}
console.log(/\p{Script=Greek}/u.test("α"));
// → true
console.log(/\p{Script=Arabic}/u.test("α"));
// → false
console.log(/\p{Alphabetic}/u.test("α"));
// → true
console.log(/\p{Alphabetic}/u.test("!"));
// → false
```

Unicode 定义了很多有用的属性，尽管要找到你需要的那个属性并非总是轻而易举的事情。你可以用 `\p{Property=Value}` 这个写法来匹配符合该属性的字符。如果属性名没写的话，比如 `\p{Name}` 这种形式里的属性名会被假定为类似 `Alphabetic` 的二元属性或者类似 `Number` 的种类属性。

{{id summary_regexp}}

## 摘要

正则表达式是用来表示字符串的样式的对象。它们用自己的语言来表达这些样式。

{{table {cols: [1, 5]}}}

| `/abc/`     | 一列字符
| `/[abc]/`   | 该字符集中的任何字符
| `/[^abc]/`  | 任何一个 _不属于_ 该字符集的字符
| `/[0-9]/`   | 在一个范围里的任何字符
| `/x+/`      | 样式 `x` 出现一次或多次
| `/x+?/`     | 出现一次或多次， 非贪心
| `/x*/`      | 出现零次或多次
| `/x?/`      | 出现零次或一次
| `/x{2,4}/`  | 出现两次至四次
| `/(abc)/`   | 一个子表达式组
| `/a|b|c/`   | 几种样式任选其一
| `/\d/`      | 任何数字字符
| `/\w/`      | 字母或数字字符("((单词字符))")
| `/\s/`      | 任何空白字符
| `/./`       | 除换行符以外的字符
| `/\b/`      | 单词边界
| `/^/`       | 输入之始
| `/$/`       | 输入末尾


正则表达式有一个 `test` 方法来测试一段字符串是否与其匹配。它还有 `exec` 方法，可以在匹配成功时返回包含所有匹配项的数组。这样的数组有一个能指示匹配项的起始位置的 `index` 属性。

字符串既有可以匹配正则表达式的 `match` 方法，也有用于搜索并返回匹配项的位置的 `search` 方法。它们的 `replace` 方法还能以新的字符串或函数对样式的匹配项进行替换。

正则表达式可以包含选项，这些选项写在闭合正斜杠的后面。`i` 选项使匹配不区分大小写。`g` 选项可以让表达式 _全局化_， 意味着让 `replace` 不单对其找到的第一个匹配项进行替换，而是替换所有的匹配项。`y` 选项让表达式具有粘性，意味着它在匹配时不会跃过部分字符串而向前搜索。`u` 选项会开启 Unicode 模式，该模式可以解决由双码单位字符引发的一些问题。

正则表达式像是一把具有钝柄的利器。它们在某些情况下使一些任务大幅简化，然而被应用于一些复杂的问题时很快又变得无法掌控。了解如何使用正则表达式的其中一个要点在于，在那些不能清晰地运用它们的时候，你得抑制住自己强行使用它们的冲动。

## 练习题

{{index debugging, bug}}

在试图解决以下练习题的过程中，你极有可能会因为某些正则表达式不可理喻的行为而感到头疼欲裂。有的时候不妨将你的正则表达式打在类似[_https://debuggex.com_](https://www.debuggex.com/)这样的在线工具网站上，这样你就可以测试它是否达到了你想要的效果，还可以用不同的输入字符串来进行((实验))。

### Regexp Golf 

{{index "program size", "code golf", "regexp golf (exercise)"}}

_Code golf_ 是一个游戏的名称，在这个游戏里，你得用尽可能少的字符来表达一个特定的程序。与之相似的是， _regexp golf_ 需要你写出尽可能短的正则表达式来匹配一个给定的样式，并且 _只_ 匹配该样式而已。

{{index boundary, matching}}

对于下列每一行的要求，写出可以测试字符串中是否出现了给定的其中一个子字符串的((正则表达式))。你写出的正则表达式只需要匹配含有其中一个提到的子字符串的字符串即可。无需担心单词边界的问题，除非有特别提到。当你的正则表达式有效时，看看能不能使它变得更短。

 1. _car_ 与 _cat_
 2. _pop_ 与 _prop_
 3. _ferret_, _ferry_, 与 _ferrari_
 4. 所有以 _ious_ 结尾的单词
 5. 空白字符，后跟句号，逗号，冒号或分号
 6. 长度大于六个字母的单词
 7. 不含 _e_ (或者 _E_) 的单词

如需帮助，请参阅[本章摘要](regexp#summary_regexp)中的表格。可以用几个测试字符串来测试你的解法。

{{if interactive
```
// Fill in the regular expressions

verify(/.../,
       ["my car", "bad cats"],
       ["camper", "high art"]);

verify(/.../,
       ["pop culture", "mad props"],
       ["plop", "prrrop"]);

verify(/.../,
       ["ferret", "ferry", "ferrari"],
       ["ferrum", "transfer A"]);

verify(/.../,
       ["how delicious", "spacious room"],
       ["ruinous", "consciousness"]);

verify(/.../,
       ["bad punctuation ."],
       ["escape the period"]);

verify(/.../,
       ["hottentottententen"],
       ["no", "hotten totten tenten"]);

verify(/.../,
       ["red platypus", "wobbling nest"],
       ["earth bed", "learning ape", "BEET"]);


function verify(regexp, yes, no) {
  // Ignore unfinished exercises
  if (regexp.source == "...") return;
  for (let str of yes) if (!regexp.test(str)) {
    console.log(`Failure to match '${str}'`);
  }
  for (let str of no) if (regexp.test(str)) {
    console.log(`Unexpected match for '${str}'`);
  }
}
```

if}}

### 引述风格 

{{index "quoting style (exercise)", "single-quote character", "double-quote character"}}

假设你写了一篇故事，而且这个故事中出现的对话都只用了((单引号))来叙述。现在你需要把所有对话中的单引号换成双引号，并且像 _aren't_ 这种缩写的单引号要保持不变。

{{index "replace method"}}

想一想有什么样式可以将这两种引用符号区分开来，然后设计对 `replace` 方法的调用来进行适当的替换。

{{if interactive
```{test: no}
let text = "'I'm the cook,' he said, 'it's my job.'";
// Change this call.
console.log(text.replace(/A/g, "B"));
// → "I'm the cook," he said, "it's my job."
```
if}}

{{hint

{{index "quoting style (exercise)", boundary}}

最明显的解法是只对至少一边是非单词字符的引号进行替换，比如说 `/\W'|'\W/` 。但你也得考虑每一行的起点和终点。

{{index grouping, "replace method", [parentheses, "in regular expressions"]}}

另外，你需要确保替换项也包含可以被 `\W` 样式所匹配的字符，这样它们才不会被落下。我们可以将它们用括号括起来，然后在替换字符串 (`$1`, `$2`) 包括它们的子表达式组。没有被匹配上的组则不会被替换。

hint}}

### 何为数字

{{index sign, "fractional number", [syntax, number], minus, "plus character", exponent, "scientific notation", "period character"}}

写一个只与 JavaScript 风格的((数字))匹配的正则表达式。它必须允许数字的前面可以有减号 _或_ 加号、小数点、或指数表示法 —— `5e-3` 或 `1E10` —— 指数前面也可以有加号或减号。另外，小数点前后不一定要有数字，但是只有一个小数点也是不行的。比如说，`.5` 和 `5.` 是有效的 JavaScript 数字，但一个单独的小数点 _不是_。

{{if interactive
```{test: no}
// Fill in this regular expression.
let number = /^...$/;

// Tests:
for (let str of ["1", "-1", "+15", "1.55", ".5", "5.",
                 "1.3e2", "1E-4", "1e+12"]) {
  if (!number.test(str)) {
    console.log(`Failed to match '${str}'`);
  }
}
for (let str of ["1a", "+-1", "1.2.3", "1+1", "1e4.5",
                 ".5.", "1f5", "."]) {
  if (number.test(str)) {
    console.log(`Incorrectly accepted '${str}'`);
  }
}
```

if}}

{{hint

{{index ["regular expression", escaping], ["backslash character", "in regular expressions"]}}

首先，不要忘记句点前的反斜杠。First, do not forget the backslash in front of the period.

要匹配((数字))或((指数))前面可选的加号或减号，可以通过 `[+\-]?` 或 `(\+|-|)`(加号、减号或啥也没有)来达到目的。

{{index "pipe character"}}

这个练习题更为复杂的地方在于，如何在同时匹配 `"5."` 和 `".5"` 的情况下做到不匹配 `"."`。为此，一个好的解法是使用 `|` 操作符来将两种情况分开考虑 —— 一种是小数点和零或多个数前面可以有一个或多个数，_或者_ 是一个小数点后面有一个或多个数。

{{index exponent, "case sensitivity", ["regular expression", flags]}}

最后，为了让 _e_ 不区分大小写，可以给正则表达式添加一个 `i` 选项或者使用 `[eE]`。

hint}}
