{{meta {load_files: ["code/chapter/12_language.js"], zip: "node/html"}}}

# 项目：一门编程语言

{{quote {author: "Hal Abelson and Gerald Sussman", title: "Structure and Interpretation of Computer Programs", chapter: true}

求值器可以决定一门编程语言中表达式的含义，而它本身也只是一个程序。

quote}}

{{index "Abelson, Hal", "Sussman, Gerald", SICP, "project chapter"}}

{{figure {url: "img/chapter_picture_12.jpg", alt: "Picture of an egg with smaller eggs inside", chapter: "framed"}}}

构建你自己的((编程语言))其实出乎意料地简单（只要你的要求不是太高）而且很启发人。

我想要透过本章传达的主要信息在于，构建你自己的编程语言并不需要什么((魔法))。我常常感到有些人类的发明聪明绝顶、纷繁复杂，以至于我永远也无法理解它们。然而通过少量的阅读和实验之后，我常发现它们也不过如此。

{{index "Egg language", [abstraction, "in Egg"]}}

我们将构建一门名为 Egg 的编程语言。它将会是一门微小、简易的语言 —— 但又足够强大，可以表达你所能想到的任何计算。它会允许基于((函数))的简单的((抽象化))。

{{id parsing}}

## 解析

{{index parsing, validation, [syntax, "of Egg"]}}

一门编程语言最显而易见的部分就是其 _句法_，或者说写法。一个 _解析器_ 是一个程序，它读取一段文本，然后生成一个数据结构，以反映那段文本所含的程序的结构。如果这段文本并不能形成有效的程序，那么解析器应当指出其错误。

{{index "special form", [function, application]}}

我们的语言将会有简洁而统一的句法。Egg 中的所有东西都是((表达式))。一个表达式可以是绑定的名字、一个数字、一个字符串，或者一个 _应用_。应用可以被用于函数的调用，以及诸如 `if` 或者 `while` 这样的构造。

{{index "double-quote character", parsing, [escaping, "in strings"], [whitespace, syntax]}}

为了使解析器使用起来简单，在 Egg 中的字符串并不支持类似反斜杠转义符这样的东西。字符串只是被双引号包起来的非双引号的字符序列。一个数值是数字的序列。绑定名称可以包含任何不是空白的、在句法中没有特殊含义的字符。

{{index "comma character", [parentheses, arguments]}}

应用的写法与在 JavaScript 中一样，即通过在表达式后面放置括号，在括号中定义任意数量的、以逗号分隔的((参数))。

```{lang: null}
do(define(x, 10),
   if(>(x, 5),
      print("large"),
      print("small")))
```

{{index block, [syntax, "of Egg"]}}

((Egg 语言))的((统一性))意味着 JavaScript 中的((运算符))(比如 `>`)在这门语言中只是普通的绑定，应用起来就像其他的((函数))一样。而且由于句法中没有语句块的概念，我们需要一个 `do` 结构来表示多项操作的依次执行。

{{index "type property", parsing, ["data structure", tree]}}

解析器用于描述程序的数据结构由((表达式))对象组成，每个对象都含有一个表明表达式种类的 `type` 属性，以及其他描述其内容的属性。

{{index identifier}}

属于 `"value"` 类型的表达式表示字面字符串和数字。它们的 `value` 属性包含了其表示的字符串或数字。`"word"` 类型的表达式则被用于标识器(名称)。这样的对象有一个 `name` 属性，该属性存有字符串形式的标识器名称。最后，`"apply"` 表达式表示应用，它们有一个 `operator` 属性，该属性指向正在被应用的表达式。此外，`"apply"` 表达式还有一个保存一个由参数表达式组成的数组的 `args` 属性。

之前程序中 `>(x, 5)` 的部分会被这样表达：

```{lang: "application/json"}
{
  type: "apply",
  operator: {type: "word", name: ">"},
  args: [
    {type: "word", name: "x"},
    {type: "value", value: 5}
  ]
}
```

{{indexsee "abstract syntax tree", "syntax tree", ["data structure", tree]}}

这样的数据结构被称为 _((句法树))_。如果你将对象想像成点，而对象之间的链接想像成这些点之间的线段的话，它就有了一个((树))的形状。表达式之中包含了其他表达式，而其他的表达式可能含有更多的表达式 —— 这就像一再分杈的树枝一样。

{{figure {url: "img/syntax_tree.svg", alt: "The structure of a syntax tree",width: "5cm"}}}

{{index parsing}}

将这个解析器与我们在[第九章](regexp#ini)里给配置文件格式写的解析器做一个对比，我们发现之前的解析器有一个简单的结构：它将输入分割成行，并且逐行进行处理。每一行只允许有几种简单的形式。

{{index recursion, [nesting, "of expressions"]}}

我们必须找到一个不同的办法来面对当下的问题。表达式并没有被分割成行，而且它们有一个递归的结构。应用表达式 _包含_ 其他的表达式。

{{index elegance}}

幸运的是，通过编写一个能反映这种语言本身递归性质的递归解析器函数就可以很好地解决这个问题。

{{index "parseExpression function", "syntax tree"}}

我们定义一个名为 `parseExpression` 的函数，该函数接受一个字符串作为输入，并且返回一个含有字符串初始位置的表达式的数据结构、字符串在解析这个表达式之后余下的部分的对象。当解析子表达式(比如一个应用的参数)的时候，这个函数可以被再次调用，产出参数的表达式以及剩余文本。剩余的文本可以依次包含更多的参数，或者成为结束参数列表的闭括号。

这是解析器的第一部分：

```{includeCode: true}
function parseExpression(program) {
  program = skipSpace(program);
  let match, expr;
  if (match = /^"([^"]*)"/.exec(program)) {
    expr = {type: "value", value: match[1]};
  } else if (match = /^\d+\b/.exec(program)) {
    expr = {type: "value", value: Number(match[0])};
  } else if (match = /^[^\s(),#"]+/.exec(program)) {
    expr = {type: "word", name: match[0]};
  } else {
    throw new SyntaxError("不是预期的句法: " + program);
  }

  return parseApply(expr, program.slice(match[0].length));
}

function skipSpace(string) {
  let first = string.search(/\S/);
  if (first == -1) return "";
  return string.slice(first);
}
```

{{index "skipSpace function", [whitespace, syntax]}}

由于 Egg 与 JavaScript 一样，在其元素之间允许任意数量的空白存在，我们需要在程序字符串的开头重复地剔除空白。这就是 `skipSpace` 函数的用武之地。

{{index "literal expression", "SyntaxError type"}}

抛开所有开头的空格之后，`parseExpression` 使用三个((正则表达式))来针对 Egg 所支持的三种基础元素：字符串、数字、单词。解析器会根据其匹配到的不同数据结构进行构造。倘若输入并不符合这三种元素中的任何一种，那么该表达式就是无效的，而且解析器会抛出一个错误。我们用属于另一种标准错误类型的 `SyntaxError` 作为异常构造器，而不是 `Error`，因为它稍微更具体一些 —— 它也是试图运行无效的 JavaScript 程序时会被抛出的错误类型。

{{index "parseApply function"}}

然后，我们从程序字符串剔除已匹配的部分，将余下的字符串与表达式对象传递给 `parseApply` 函数，该函数检查一个表达式是否为应用。如果是应用的话，则会解析一个带有括号的参数列表。

```{includeCode: true}
function parseApply(expr, program) {
  program = skipSpace(program);
  if (program[0] != "(") {
    return {expr: expr, rest: program};
  }

  program = skipSpace(program.slice(1));
  expr = {type: "apply", operator: expr, args: []};
  while (program[0] != ")") {
    let arg = parseExpression(program);
    expr.args.push(arg.expr);
    program = skipSpace(arg.rest);
    if (program[0] == ",") {
      program = skipSpace(program.slice(1));
    } else if (program[0] != ")") {
      throw new SyntaxError("Expected ',' or ')'");
    }
  }
  return parseApply(expr, program.slice(1));
}
```

{{index parsing}}

如果程序中下一个字符不是开括号，那么它就不是一个应用，且 `parseApply` 返回给定的表达式。

{{index recursion}}

否则的话，该函数会跳过开括号，并创建这个应用表达式的((句法树))对象。然后，它将递归调用 `parseExpression` 来对每一个参数进行解析，直到一个闭括号被找到。这个递归是间接的，通过 `parseApply` 和 `parseExpression` 彼此调用。

由于一个应用表达式本身也可以被应用(比如 `multiplier(2)(1)`)，`parseApply` 必须在它对一个应用解析之后，再次对其进行调用以检查自身是否后面还跟着另外一对括号。

{{index "syntax tree", "Egg language", "parse function"}}

我们解析 Egg 所需要做的就是这些。我们将程序包装在方便的 `parse` 函数里，该函数确保在解析表达式(一个 Egg 程序就是一个表达式)之后输入字符串的末尾已被达到，而且这给了我们该程序的数据结构。

```{includeCode: strip_log, test: join}
function parse(program) {
  let {expr, rest} = parseExpression(program);
  if (skipSpace(rest).length > 0) {
    throw new SyntaxError("Unexpected text after program");
  }
  return expr;
}

console.log(parse("+(a, 10)"));
// → {type: "apply",
//    operator: {type: "word", name: "+"},
//    args: [{type: "word", name: "a"},
//           {type: "value", value: 10}]}
```

{{index "error message"}}

这行得通！它在出故障的时候并不会给我们非常有用的信息，也不保存每一个表达式起始的行和列(可能在之后报告错误的时候会有帮助)，但是它已经足够满足我们的目的了。

## 求值器

{{index "evaluate function", evaluation, interpretation, "syntax tree", "Egg language"}}

我们可以对程序的句法树做些什么呢？当然是运行它了！而这正是求值器(evaluator)所做的事情。你给它一个句法树和一个将名称和值联系起来的作用域对象，它就会对句法树所表示的表达式进行求值，并且返回结果的值。

```{includeCode: true}
const specialForms = Object.create(null);

function evaluate(expr, scope) {
  if (expr.type == "value") {
    return expr.value;
  } else if (expr.type == "word") {
    if (expr.name in scope) {
      return scope[expr.name];
    } else {
      throw new ReferenceError(
        `Undefined binding: ${expr.name}`);
    }
  } else if (expr.type == "apply") {
    let {operator, args} = expr;
    if (operator.type == "word" &&
        operator.name in specialForms) {
      return specialForms[operator.name](expr.args, scope);
    } else {
      let op = evaluate(operator, scope);
      if (typeof op == "function") {
        return op(...args.map(arg => evaluate(arg, scope)));
      } else {
        throw new TypeError("Applying a non-function.");
      }
    }
  }
}
```

{{index "literal expression", scope}}

这个求值器针对每一种类型的((表达式))都有相应代码。一个字面值表达式产生它自身的值(譬如，`100` 这个表达式求值的结果为 100 这个数字)。对于一个绑定而言，我们必须检查它是否已经在作用域中被定义了，如果是的话，就获取该绑定的值。

{{index [function, application]}}

应用(Applications)则更复杂一些。如果它们属于像 `if` 那样的((特殊形式))，我们不对其进行求值，而是将参数表达式与作用域一起传递给处理这个形式的函数。如果应用是普通调用，那我们对运算符进行求值，确认其是否是函数，并且以求值后的参数来调用它。

为了表示 Egg 的函数值，我们使用普通的 JavaScript 函数。我们 [之后](language#egg_fun) 在定义了名为 `fun` 的特殊形式的时候，会回到这个话题。

{{index readability, "evaluate function", recursion, parsing}}

`evaluate` 的递归结构与解析器的结构类似，而且两者都反映出该语言本身的结构。将解析器和求值器整合于一体、并在解析的时候进行求值也是可以的，不过将它们如此分离使得该程序更容易被理解。

{{index "Egg language", interpretation}}

这便是解读 Egg 所需的所有代码了，就是如此简单。然而，如果不定义一些特殊形式、以及给该语言的((环境))添加一些有用的值的话，你用这个语言就做不了什么事情。

## 特殊形式

{{index "special form", "specialForms object"}}

`specialForms` 对象用于定义 Egg 中的特殊句法，它将单词和求解这种形式的函数联系了起来。当前该对象是空的，我们加上 `if`。

```{includeCode: true}
specialForms.if = (args, scope) => {
  if (args.length != 3) {
    throw new SyntaxError("传给 if 的参数数量错误");
  } else if (evaluate(args[0], scope) !== false) {
    return evaluate(args[1], scope);
  } else {
    return evaluate(args[2], scope);
  }
};
```

{{index "conditional execution", "ternary operator", "?: operator", "conditional operator"}}

Egg 的 `if` 构造语句必须接受三个参数，它会首先求解第一个参数，且在其结果不为 `false` 的情况下求解第二个参数。否则的话，第三个参数会被求解。与 JavaScript 的 `if` 相比，这个 Egg 中的 `if` 形式与 JavaScript 的三元 `?:` 操作符更为相似。它是一个表达式，而不是语句，而且它产生一个值，也就是第二个或第三个参数的结果。

{{index Boolean}}

在处理 `if` 的条件值时，Egg 也与 JavaScript 有所不同。它并不会将零或空字符串视为 false，只有当值确实为 `false` 的时候，才将其视为 false。

{{index "short-circuit evaluation"}}

我们将 `if` 表示为一个特殊形式，而不是一个普通的函数，这是由于所有函数的参数在调用之前都会被求解，而 `if` 只应该根据第一个参数的值对第二个 _或_ 第三个参数进行求解。

与之类似的还有 `while` 形式。

```{includeCode: true}
specialForms.while = (args, scope) => {
  if (args.length != 2) {
    throw new SyntaxError("Wrong number of args to while");
  }
  while (evaluate(args[0], scope) !== false) {
    evaluate(args[1], scope);
  }

  // 由于 undefined 在 Egg 中并不存在
  // 缺乏有意义的结果，所以我们返回 false
  return false;
};
```

另一个基本构建块是 `do`，它会自上而下执行其所有的参数。该表达式的值是最后一个参数产生的值。

```{includeCode: true}
specialForms.do = (args, scope) => {
  let value = false;
  for (let arg of args) {
    value = evaluate(arg, scope);
  }
  return value;
};
```

{{index ["= operator", "in Egg"], [binding, "in Egg"]}}

为了可以创建绑定并对其赋予新的值，我们还创建了名为 `define` 的形式。它期待一个单词作为其第一个参数，一个产生的值被赋予那个单词的表达式作为其第二个参数。既然 `define` 也是个表达式，像别的表达式一样，它必须返回一个值。我们会使它返回赋予给绑定的值(就像 JavaScript 的 `=` 运算符一样)。

```{includeCode: true}
specialForms.define = (args, scope) => {
  if (args.length != 2 || args[0].type != "word") {
    throw new SyntaxError("Incorrect use of define");
  }
  let value = evaluate(args[1], scope);
  scope[args[0].name] = value;
  return value;
};
```

## 环境

{{index "Egg language", "evaluate function", [binding, "in Egg"]}}

`evaluate` 接受的((作用域))是一个对象，该对象的属性名对应绑定名，其属性的值对应这些绑定所绑定的值。让我们定义一个用于表示((全局作用域))的对象。

为了能够使用我们刚才定义的 `if` 构造语句，我们必须能够访问((布尔))值。由于只存在两个布尔值，我们不需要给它们特殊的句法。我们只需将两个名称与 `true` 和 `false` 值绑定，就可以使用它们了。

```{includeCode: true}
const topScope = Object.create(null);

topScope.true = true;
topScope.false = false;
```

我们现在可以对一个简单的、对布尔值求反的表达式进行求解。

```
let prog = parse(`if(true, false, true)`);
console.log(evaluate(prog, topScope));
// → false
```

{{index arithmetic, "Function constructor"}}

为了提供基本的((算术))和((比较))运算符，我们也会给((作用域))添加一些函数值。因为想要让代码保持简短，与其说单独对运算符函数进行定义，我们用 `Function` 在一个循环中合成一批运算符。

```{includeCode: true}
for (let op of ["+", "-", "*", "/", "==", "<", ">"]) {
  topScope[op] = Function("a, b", `return a ${op} b;`);
}
```

拥有一种能((输出))值的方法也会很有用，所以我们将 `console.log` 包装在一个函数中，并将其称为 `print`。

```{includeCode: true}
topScope.print = value => {
  console.log(value);
  return value;
};
```

{{index parsing, "run function"}}

这给了我们足够的基本工具来编写简单的程序。下列函数提供了一个简单的方法来解析程序，并且在一个新的作用域中运行该程序：

```{includeCode: true}
function run(program) {
  return evaluate(parse(program), Object.create(topScope));
}
```

{{index "Object.create function", prototype}}

我们将使用对象原型链来表示嵌套的作用域，这样一来，该程序就可以向其本地作用域添加绑定，而无需更改顶层的作用域。

```
run(`
do(define(total, 0),
   define(count, 1),
   while(<(count, 11),
         do(define(total, +(total, count)),
            define(count, +(count, 1)))),
   print(total))
`);
// → 55
```

{{index "summing example", "Egg language"}}

这个程序我们之前已经见了几次，它计算数字 1 到 10 的和， 以 Egg 语言表达。显然，与同样功能的 JavaScript 程序相比，它更为难看 —— 但是对一门只用了不到 150 ((行代码))就实现的语言来说，已经不错了。

{{id egg_fun}}

## 函数

{{index function, "Egg language"}}

一门没有函数的编程语言确实是一门糟糕的编程语言。

所幸想要添加一个 `fun` 构造语句并不难，它将其最后一个参数视为函数主体，并将最后一个参数之前的参数都用作函数的参数名称。

```{includeCode: true}
specialForms.fun = (args, scope) => {
  if (!args.length) {
    throw new SyntaxError("Functions need a body");
  }
  let body = args[args.length - 1];
  let params = args.slice(0, args.length - 1).map(expr => {
    if (expr.type != "word") {
      throw new SyntaxError("Parameter names must be words");
    }
    return expr.name;
  });

  return function() {
    if (arguments.length != params.length) {
      throw new TypeError("Wrong number of arguments");
    }
    let localScope = Object.create(scope);
    for (let i = 0; i < arguments.length; i++) {
      localScope[params[i]] = arguments[i];
    }
    return evaluate(body, localScope);
  };
};
```

{{index "local scope"}}

Egg 中的函数拥有它们自己的本地作用域。由 `fun` 形式产生的函数可以创建这个本地作用域，并且向其添加参数绑定。然后，它在这个作用域中对函数主体进行求解，并返回结果。

```{startCode: true}
run(`
do(define(plusOne, fun(a, +(a, 1))),
   print(plusOne(10)))
`);
// → 11

run(`
do(define(pow, fun(base, exp,
     if(==(exp, 0),
        1,
        *(base, pow(base, -(exp, 1)))))),
   print(pow(2, 10)))
`);
// → 1024
```

## 编译

{{index interpretation, compilation}}

我们构建的是一个解读器(interpreter)。在求值的过程中，解读器直接作用于解析器产生的表征程序。

{{index efficiency, performance, [binding, definition], [memory, speed]}}

_编译_ 是在程序的解析和运行之间添加的另一道工序，它通过事先完成尽可能多的工作，将程序转化之后，该程序可以被更有效率地求解。比方说，在设计优良的编程语言中，即使没有真的运行程序，每一个绑定的用法、绑定指向的值都是显而易见的。这可以被用来避免每一次访问绑定时都搜索绑定名称，从而直接从预先定义好的内存地址获取绑定。

从传统上来说，((编译))包括将程序转换成((机器代码))，也就是计算机的处理器可以执行的原始格式。然而，任何将程序转换成一种不同的表征形式的过程都可以被称为编译。

{{index simplicity, "Function constructor", transpilation}}

为 Egg 编写一种替代的((求值))策略是可行的，首先将程序转换成 JavaScript 程序，采用 `Function` 调用 JavaScript 的编译器，然后运行编译结果。如果做法无误，这将会使得 Egg 运行起来非常迅速，而实现起来仍然很简单。

如果你对这个话题有兴趣，并且愿意多花些时间在上面的话，我鼓励你试着去实现一个这样的编译器，作为锻炼。

## 借鉴

{{index "Egg language"}}

当我们定义 `if` 和 `while` 的时候，你可能意识到了它们或多或少不过是 JavaScript 自身的 `if` 和 `while` 语句的小型包装器罢了。同样的，Egg 中的值就是 Javascript 中普通的值。

如果你将 Egg 的实现方式(构建于 JavaScript 的基础之上)与另一种直接基于机器提供的原始功能之上构建的编程语言相比，你会发现后者所需的精力和复杂程度都多出许多。不论如何，本章这个 Egg 语言的例子就是想让你对((编程语言))的工作方式有一个印象。

在需要完成一些事情的时候，借鉴别人的代码比起你自己编写所有的功能要有效率得多。虽然本章我们编写的闹着玩的语言并不能在任何 JavaScript 能做的事情上做得更为出色，但 _有_ 编写微型编程语言帮助我们完成真实工作的情况。

这样的语言并不需要与一门典型的编程语言相仿。打个比方，如果 JavaScript 并没有自带正则表达式的话，你可以自行为正则表达式编写解析器和求值器。

{{index "artificial intelligence"}}

或者，想象一下你正在建造一个巨型机器((恐龙))，并且需要对其((行为))进行编程。JavaScript 可能不是完成这个任务最有效的方式。你可能需要选择一门看起来像这样的语言：

```{lang: null}
behavior walk
  perform when
    destination ahead
  actions
    move left-foot
    move right-foot

behavior attack
  perform when
    Godzilla in-view
  actions
    fire laser-eyes
    launch arm-rockets
```

{{index expressivity}}

这通常被称为 _((领域特定语言))_，也就是一门专为表达某个精细领域的知识而设计的语言。这样的语言比通用目的的语言更有表述性，因为它就是为了描述它领域中的事物而生的，不包含多余的东西。

## 练习题

### 数组

{{index "Egg language", "arrays in egg (exercise)", [array, "in Egg"]}}

为了给 Egg 添加数组支持，通过将下列三个函数添加到顶层作用域：`array(...values)` 来构造一个含有参数值的数组，`length(array)` 获取一个数组的长度，还有 `element(array, n)` 来获取一个数组中第 n^th^ 个元素。

{{if interactive

```{test: no}
// 修改下列定义

topScope.array = "...";

topScope.length = "...";

topScope.element = "...";

run(`
do(define(sum, fun(array,
     do(define(i, 0),
        define(sum, 0),
        while(<(i, length(array)),
          do(define(sum, +(sum, element(array, i))),
             define(i, +(i, 1)))),
        sum))),
   print(sum(array(1, 2, 3))))
`);
// → 6
```

if}}

{{hint

{{index "arrays in egg (exercise)"}}

最简单的方法是用 JavaScript 数组来表示 Egg 数组。

{{index "slice method"}}

加入顶层作用域的值必须是函数。一旦采用剩余参数(...三点写法)，`array` 的定义就会 _很_ 简单。

hint}}

### 闭包

{{index closure, [function, scope], "closure in egg (exercise)"}}

我们定义 `fun` 的方式允许 Egg 中的函数引用它周围的作用域，这样一来，函数主体就可以使用在定义函数时可见的本地值，就像 JavaScript 函数那样。

下列程序展示了这个特点：函数 `f` 返回一个将自己的参数添加到 `f` 参数的函数，这意味着为了可以使用绑定 `a`，该函数需要访问 `f` 中的本地((作用域))。

```
run(`
do(define(f, fun(a, fun(b, +(a, b)))),
   print(f(4)(5)))
`);
// → 9
```

回到 `fun` 形式的定义，解释哪一个机制使得它可以如此被使用。

{{hint

{{index closure, "closure in egg (exercise)"}}

同样的，我们正借鉴 JavaScript 的机制来建立在 Egg 中等效的特性。特殊形式是可以接受它们被求解的本地作用域，从而让它们可以在那个作用域中求解其次形式。由 `fun` 返回的函数可以访问传递给它封闭函数的 `scope` 参数，并且在调用该函数时将其用于创建它的本地((作用域))。

{{index compilation}}

这意味着本地作用域的((原型))会成为函数被创建时的作用域，也就使得从函数中访问那个作用域中的绑定成为可能。这就是实现闭包所需要做的(然而要将它以一种真实有效率的方式编译的话，你还需要做一些额外的功课)。

hint}}

### 注释

{{index "hash character", "Egg language", "comments in egg (exercise)"}}

如果我们可以在 Egg 中编写((注释))的话，那是很好的。举个例子，每当我们找到一个井号(`#`)，我们可以将该行剩余的部分视为注释，并忽略它，像 JavaScript 中的 `//` 一样。

{{index "skipSpace function"}}

我们并不需要为了支持这个特性而对解析器做出很大的改动。我们可以简单地改动 `skipSpace`，让它像跳过((空格))那样跳过注释，因此所有调用 `skipSpace` 的地方现在也可以跳过注释。做出上述的这个改动。

{{if interactive

```{test: no}
// 这是之前的 skipSpace，对它进行修改
function skipSpace(string) {
  let first = string.search(/\S/);
  if (first == -1) return "";
  return string.slice(first);
}

console.log(parse("# hello\nx"));
// → {type: "word", name: "x"}

console.log(parse("a # one\n   # two\n()"));
// → {type: "apply",
//    operator: {type: "word", name: "a"},
//    args: []}
```
if}}

{{hint

{{index "comments in egg (exercise)", [whitespace, syntax]}}

确保你的解决方法可以一下子处理多行注释(前后可能都有空格)。

解决这个问题最简单的方法大概是((正则表达式))。编写可以匹配“空格或一段注释，零次或多次”。采用 `exec` 或者 `match` 方法，并且查看返回的数组中的第一个元素(整个匹配项)的长度，从而找出需要剔除多少个字符。

hint}}

### 修改作用域

{{index [binding, definition], assignment, "fixing scope (exercise)"}}

目前，将绑定赋值的唯一办法是采用 `define`。这个构造既能定义新的绑定，也可以赋予已存在的绑定一个新的值。

{{index "local binding"}}

这种((模棱两可的特性))导致了一个问题。当你想要给一个非本地绑定赋上新的值的时候，你最终会用同样的名称定义一个本地绑定。有的编程语言就是照此设计的，但我总觉得这种处理((作用域))的做法有些尴尬。

{{index "ReferenceError type"}}

添加一个名为 `set` 的特殊形式，它与 `define` 相似，会给绑定一个新的值，并且在该绑定不存在于内部作用域的情况下更新其外部作用域的绑定。如果绑定根本就没有被定义的话，抛出一个 `ReferenceError` (另一种类型的标准错误)。

{{index "hasOwnProperty method", prototype, "getPrototypeOf function"}}

到目前为止，将作用域表示为简单对象的做法让事情变得方便了，但此刻这种做法会给你带来一些阻碍。你可能会想要使用 `Object.getPrototypeOf` 函数，它会返回一个对象的原型。同时别忘了作用域并不继承 `Object.prototype`，所以如果你想要对作用域调用 `hasOwnProperty` 的话，你必须采用以下这个笨拙的表达式：

```{test: no}
Object.prototype.hasOwnProperty.call(scope, name);
```

{{if interactive

```{test: no}
specialForms.set = (args, scope) => {
  // 在这里输入你的代码
};

run(`
do(define(x, 4),
   define(setx, fun(val, set(x, val))),
   setx(50),
   print(x))
`);
// → 50
run(`set(quux, true)`);
// → 某些种类的引用错误
```
if}}

{{hint

{{index [binding, "compilation of"], assignment, "getPrototypeOf function", "hasOwnProperty method", "fixing scope (exercise)"}}

你将会需要一次循环一个((作用域))，使用 `Object.getPrototypeOf` 去向下一个外部作用域。对于每个作用域，采用 `hasOwnProperty` 来找出由 `set` 的首个参数的 `name` 属性指向的绑定在该作用域中是否存在。如果它存在的话，将其设为对 `set` 第二个参数求值的结果，然后返回这个值。 

{{index "global scope", "run-time error"}}

如果已经到达最外部的作用域(`Object.getPrototypeOf` 返回 null)，而我们仍尚未找到该绑定，那就意味着它并不存在，那么应该抛出一个异常。

hint}}
