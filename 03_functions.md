# Functions

{{quote {author: "Donald Knuth", chapter: true}

大家以为计算机科学是属于天才的游戏。事实恰恰相反。只不过是一群搭建积木的人而已。

quote}}

{{index "Knuth, Donald"}}

{{figure {url: "img/chapter_picture_3.jpg", alt: "Picture of fern leaves with a fractal shape", chapter: framed}}}

{{index function, [code, "structure of"]}}

函数是 JavaScript 编程的核心。用一个值包住一段代码的用途有很多。它让我们更好的构建大程序、减少重复的代码、在给子程序命名的同时，让它们无法互通。

最明显的应用是创建新的((词汇))。通常来讲，我们应该避免在文字中创建新的单词。然而在编程的世界中，这是无法避免的。

{{index abstraction, vocabulary}}

大部分成人的英文词汇量有 2w左右。鲜少有编程语言自带 2w 指令。而且编程语言中的词汇普遍有着非常精确的定义，因此远不及人类的语言灵活。所以，我们通常需要引入新的概念来避免过度重复。

## Defining a function

{{index "square example", [function, definition], [binding, definition]}}

函数的定义是一个常规变量，而被绑定的值是一个函数。如下所示，代码定义了一个名为`square`的函数，用来计算所给数字的平方。

```
const square = function(x) {
  return x * x;
};

console.log(square(12));
// → 144
```

{{indexsee "curly braces", braces}}
{{index [braces, "function body"], block, [syntax, function], "function keyword", [function, body], [function, "as value"], [parentheses, arguments]}}

以关键词`function`开头的表达式用来创建函数。函数由一组_((参数))_（上面的例子里，只有参数`x`）和一个_函数体_组成。函数体中包含了该函数需要执行的语句，且必须在大括号内，即使只有一行((语句))。

{{index "power example"}}

函数可以有零个或多个参数。如下所示，`makeNoise`没有任何参数，`power`却有两个：

```
const makeNoise = function() {
  console.log("Pling!");
};

makeNoise();
// → Pling!

const power = function(base, exponent) {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
};

console.log(power(2, 10));
// → 1024
```

{{index "return value", "return keyword", undefined}}

有些函数返回一个值，比如`power`和`square`；有些不会，比如`makeNoise`，其结果只是一个((副作用))。`return`语句决定了该函数返回的值。当遇见`return`语句时，程序会立即跳出该函数，并返回该语句的值。如果`return`后面没有表达式，函数会返回`undefined`。没有`return`的函数，比如`makeNoise`，也会返回`undefined`。

{{index parameter, [function, application], [binding, "from parameter"]}}

函数的参数同普通变量相似，不过它们的初始值由_函数调用者_提供，而不是函数本身。

## Bindings and scopes

{{indexsee "top-level scope", "global scope"}}
{{index "var keyword", "global scope", [binding, global], [binding, "scope of"]}}

每个变量都有一个_((作用域))_，指该变量在程序中可被使用的区域。如果一个变量的定义在所以函数和块语句外，它的作用域就是整个程序，也就是说你可以随时引用该变量。这就是_全局_变量。

{{index "local scope", [binding, local]}}

不过由函数((参数))创建的，或在函数中创建的变量只能在该函数中引用。它们也叫做_本地_变量。每当该函数被引用时，程序会创建新的实例给这些变量。如此创建了函数间的隔离，每个函数都有一个属于自己的小世界（它的本地环境），且简单易懂，即使不了解全局环境。

{{index "let keyword", "const keyword", "var keyword"}}

由`let`和`const`创建的变量都是该_((块语句))_的本地变量。因此一个循环里面的该变量是无法被循环外面的代码“看见”的。在 2015年前的 JavaScript 中，只有函数才能创建新的作用域。因此老版变量中，只有`var`一个关键词，且这些变量在整个函数中都可见。或全局作用域，如果该变量不在函数中。

```
let x = 10;
if (true) {
  let y = 20;
  var z = 30;
  console.log(x + y + z);
  // → 60
}
// y 在这里不存在
console.log(x + z);
// → 40
```

{{index [binding, visibility]}}

每个((作用域))都可以看到外面的世界，比如上例中的`x`。除法多个同名变量，这种情况下，取最里层的变量。比如，在`halve`函数中的`n`指的是它自身的`n`而不是全局中的`n`。

```
const halve = function(n) {
  return n / 2;
};

let n = 10;
console.log(halve(100));
// → 50
console.log(n);
// → 10
```

{{id scoping}}

### Nested scope

{{index [nesting, "of functions"], [nesting, "of scope"], scope, "inner function", "lexical scoping"}}

JavaScript 不仅区分_全局_和_本地_变量。块语句和函数中也可以创建其他块语句和函数中，从而产生多个层次的局部。

{{index "landscape example"}}

比如，下面这个函数，列出做芝麻酱的所需材料，就包含了另一个函数：

```
const hummus = function(factor) {
  const ingredient = function(amount, unit, name) {
    let ingredientAmount = amount * factor;
    if (ingredientAmount > 1) {
      unit += "s";
    }
    console.log(`${ingredientAmount} ${unit} ${name}`);
  };

  ingredient(1, "can", "chickpeas");
  ingredient(0.25, "cup", "tahini");
  ingredient(0.25, "cup", "lemon juice");
  ingredient(1, "clove", "garlic");
  ingredient(2, "tablespoon", "olive oil");
  ingredient(0.5, "teaspoon", "cumin");
};
```

{{index [function, scope], scope}}

函数`ingredient`中的代码可以看到外面函数中的变量`factor`。但是它的本地变量，比如`unit`或`ingredientAmount`却无法被外面的函数看到。

块语句中可见的变量是由该变量在程序中的位置决定的。每个本地作用域可看到所以包含它的本地作用域；所有作用域都能看到全局作用域。这就是_((静态作用域))_。

## Functions as values

{{index [function, "as value"], [binding, definition]}}

一般来讲，函数变量只是一个充当程序特定部分的名字。各种变量定义一次，永不改变。因此很容易混淆函数和它的名字。

{{index [binding, assignment]}}

不过它们是不同的。函数值与其他值无异，可用于任意表达式中，而不只是仅仅被引用。函数值还可以被存在另一个变量中，作为参数放入另一个函数中，等。同理，一个包含函数的变量只是一个普通的变量，因此如果它不是常量的话，可被重新分配一个新值：

```{test: no}
let launchMissiles = function() {
  missileSystem.launch("now");
};
if (safeMode) {
  launchMissiles = function() {/* do nothing */};
}
```

{{index [function, "higher-order"]}}

[第五章](higher_order)中，我们会探讨将函数值传递到其他函数中的趣事。

## Declaration notation

{{index [syntax, function], "function keyword", "square example", [function, definition], [function, declaration]}}

创新函数变量有一个相对简洁的方法。当关键词`function`用在一个语句前，它的作用有所改变。

```{test: wrap}
function square(x) {
  return x * x;
}
```

{{index future, "execution order"}}

这就是函数_声明_。这个语句定义了`square`变量，且把它指定给了该函数。这种写法相对简单，且不需要在结尾加分号。

这种函数定义方法有一个微妙之处。

```
console.log("The future says:", future());

function future() {
  return "You'll never have flying cars";
}
```

上面的代码工作，尽管该函数的定义在使用它的代码_下面_。函数声明不在普通的控制流内。从概念上讲。它们被放置在了它们作用域的最顶层，因此可被该作用域的所有代码使用。这提供了排序代码的自由度，让我们可以根据程序的需要来排序这些函数，而不是将它们全部置顶。

## Arrow functions

{{index function, "arrow function"}}

第三种创建函数的方法，与上面两种截然不同。这种方法用一个箭头（`=>`），一个等号加一个大于号（注意不要和大于`>=`等于混淆）代替了关键词`function`。

```{test: wrap}
const power = (base, exponent) => {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
};
```

{{index [function, body]}}

箭头在所有参数后面，其次是函数体。所表达的意思是“这些参数将得到这个结果”。

{{index [braces, "function body"], "square example", [parentheses, arguments]}}

如果只有一个参数，你可以省略掉围绕参数的小括号。如果函数体只有一行表达式，与其一个带有大括号的块语句，可以直接返回该表达式。因此，下面两个`square`函数定义相等：

```
const square1 = (x) => { return x * x; };
const square2 = x => x * x;
```

{{index [parentheses, arguments]}}

一个没有参数的箭头函数，它的参数表是一个空括号。

```
const horn = () => {
  console.log("Toot");
};
```

{{index verbosity}}

没有必要同时使用箭头函数和`function`表达式。除了[第六章](object)中的一个小细节外，它们完全相等。箭头函数是2015年加入的，主要为了简洁化小函数表达式。在[第五章](higher_order)中，我们会频繁使用它。

{{id stack}}

## The call stack

{{indexsee stack, "call stack"}}
{{index "call stack", [function, application]}}

控制流在函数中的顺序稍微复杂。让我们具体研究下。下面是一个调用了数个函数的简单程序：

```
function greet(who) {
  console.log("Hello " + who);
}
greet("Harry");
console.log("Bye");
```

{{index ["control flow", functions], "execution order", "console.log"}}

这个程序大概流程如下：
1. 调用`greet`让程序跳至最上面定义`greet`的代码（第二行）
2. 该函数调用了`console.log`。控制流再度改写，执行该代码，返回至第二行代码
3. 程序运行到函数`greet`的最后一行，所以它返回至调用它的地方（第四行）
4. 第五行再度调用了`console.log`
5. `console.log`再度执行完，程序结束。

下面的示意图展现了上面代码的控制流：

```{lang: null}
不在函数中
   在函数 greet 中
        在 console.log 中
   在函数 greet 中
不在函数中
   在 console.log 中
不在函数中
```

{{index "return keyword", [memory, call stack]}}

因为函数需要跳回调用它的地方，电脑必须记住这个调用的前后关系。上面的例子中，其中一个`console.log`结束后需要回到函数`greet`。而另一个`console.log`直接结束程序。

电脑存储这些相关关系的地方叫做_((堆栈))_。每当一个函数被调用时，当前的信息被存放在堆栈的最上面。当一个函数返回时，电脑移除堆栈最上面的信息，并用该信息继续运行。

{{index "infinite loop", "stack overflow", recursion}}

电脑需要内存来储存这个堆栈。当堆栈过大是，电脑会出现故障，显示类似“没有足够堆栈空间”或者“太多递归”的讯息。例如下面的代码，它问了一个极为复杂的问题，迫使电脑反复往返于两个函数中。如果电脑有一个无限大的堆栈，这个程序将永无止境。现实中，这个程序会用完所以堆栈的空间。

```{test: no}
function chicken() {
  return egg();
}
function egg() {
  return chicken();
}
console.log(chicken() + " came first.");
// → ??
```

## Optional Arguments

{{index argument, [function, application]}}

下面的代码是被允许的，且会正常运行：

```
function square(x) { return x * x; }
console.log(square(4, true, "hedgehog"));
// → 16
```

尽管`square`定义时只有一个((参数))，我们调用时却提供了三个。但是该程序正常运行了。它忽视了多余的参数，并计算了第一个参数的平方。

{{index undefined}}

JavaScript 对一个函数的参数数量的管理非常宽松。多余的参数会被忽略掉，而缺少的参数会被定义为`undefined`。

该缺点就是你会不小心传递错误的参数给一个函数。然而程序并不会提醒你。

优点就是一个函数可以被不同数量的参数使用。比如，下面的函数`minus`试图以一个或两个参数来模仿`-`运算符：

```
function minus(a, b) {
  if (b === undefined) return -a;
  else return a - b;
}

console.log(minus(10));
// → -10
console.log(minus(10, 5));
// → 5
```

{{id power}}
{{index "optional argument", "default value", parameter, ["= operator", "for default value"]}}

如果一个参数后面跟着一个`=`运算符，后跟着一个表达式，且函数调用时，该参数没有任何定义值，那么该表达式的值将取代该参数值。

{{index "power example"}}

比如，这个`power`的第二个参数就是可选的。如果没有提供，或给了`undefined`的话，它的值就会默认为2。那么该函数就类似`square`。

```{test: wrap}
function power(base, exponent = 2) {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
}

console.log(power(4));
// → 16
console.log(power(2, 6));
// → 64
```

{{index "console.log"}}

下[下一章](data#rest_parameters)中，我们会见识一直读取所有参数的方法。这会让一个函数可以接收任意数量的参数。比如，`console.log`就可以返回所有参数。

```
console.log("C", "O", 2);
// → C O 2
```

## Closure

{{index "call stack", "local binding", [function, "as value"], scope}}

JavaScript 把函数当作值的处理方式，而且每次函数被调用时，本地变量都会重建。这两点延发了一个非常有意思的问题。当这些被调用的函数不再运行时，这些本地变量要如何处理？

比如下面的代码。它创建了一个函数`wrapValue`，里面有一个本地变量。它之后返回一个函数，而这个被返回的函数返回了被那个本地变量。

```
function wrapValue(n) {
  let local = n;
  return () => local;
}

let wrap1 = wrapValue(1);
let wrap2 = wrapValue(2);
console.log(wrap1());
// → 1
console.log(wrap2());
// → 2
```

这是被允许的，且按照你希望的那样运行。两个变量的实例都依旧存在。这个例子完美展现了每次调用的本地变量都是重新创建的，而且不同的调用是无法改变另一个调用的本地变量的。

这种在封闭作用域中引用指定的本地变量实例的功能叫做_((闭包))_。一个函数对其本地变量的引用捆绑叫做闭包。这不仅减少对变量寿命的不必要担心，还给函数值的运用提供了许多新的思路。

{{index "multiplier function"}}

只需一点改变，我们可以把前面的例子改成一个乘以任意数量的函数。

```
function multiplier(factor) {
  return number => number * factor;
}

let twice = multiplier(2);
console.log(twice(5));
// → 10
```

{{index [binding, "from parameter"]}}

在`wrapValue`例子中，并不需要那个`local`变量的绑定。因为参数本身就是一个本地参数绑定。

{{index [function, "model of"]}}

考虑类似的程序需要一些练习。一个好的建议是函数值想象成一个包裹着它自身的代码和创建它的环境的整体。在被调用时，函数体可以看到创建它的环境，但是看不到调用它的环境。

上个例子中，`multiplier`被调用时创建了一个参数`factor`为2的环境。它返回的值被储存在了`twice`里，并记住了这个环境。所以当`twice`被调用时，它会将自身的参数乘以2。

## Recursion

{{index "power example", "stack overflow", recursion, [function, application]}}

一个函数完全可以叫自己，只要堆栈没有溢出。一个叫自己的函数又名_递归_。递归使一些函数可以用另一种方式写出。比如下例中的`power`：

```{test: wrap}
function power(base, exponent) {
  if (exponent == 0) {
    return 1;
  } else {
    return base * power(base, exponent - 1);
  }
}

console.log(power(2, 3));
// → 8
```

{{index loop, readability, mathematics}}

这其实与数学家定义的求幂方法非常相似，且比循环更清晰的描述了这个概念。该函数不断以以更小的指数作为参数调用自己，从而实现重复的乘法。

{{index [function, application], efficiency}}

不过这个实作有一个问题：在正常的 JavaScript 实作中，这比循环的方法慢3倍左右。运行一个简单的循环比多次调用一个程序快许多。

{{index optimization}}

速度和((优雅))的矛盾是一个有趣的命题。它在人性化与机械化中反复出现。几乎所以程序在更大更复杂化后，速度都有所提升。而程序员必须取决一个适当的平衡。

比如`power`这个例子，并不优雅（循环）的版本依旧简单易懂。所以不需要被递归的版本取代。不过，如果一个程序的概念比较复杂时，应该放弃一部分的速度，让代码更加简单易懂。

{{index profiling}}

担心效率问题可能会分散注意力。这也是一个复杂化程序设计的因子。而当你已经在做一件很复杂的事情是，任何多余的事情都可能成为那最后一根稻草。

{{index "premature optimization"}}

因此，先写一些正确且简单易懂的代码。你如果担心它的速度，虽然大部分简单的代码被运行的次数没有频繁到影响速度，你可以事后进行测速再决定是否需要改进。

{{index "branching recursion"}}

递归并不总是循环的低效代替方法。有些问题用递归的方法远比循环的方法简单。这些问题通常需要探索或处理多个“分支”，而每个分支很可能有更多的分支。

{{id recursive_puzzle}}
{{index recursion, "number puzzle example"}}

假设：从数字1开始，反复加5或者乘3，可得到一个无限数集。你要如何写一个函数，给一个数字，试图找到一个用加5或乘3而得到该数字的方法？

比如，数字13可以通过先成3在加5两次得到。但是却永远无法到达数字15。

下面是递归的方法：

```
function findSolution(target) {
  function find(current, history) {
    if (current == target) {
      return history;
    } else if (current > target) {
      return null;
    } else {
      return find(current + 5, `(${history} + 5)`) ||
             find(current * 3, `(${history} * 3)`);
    }
  }
  return find(1, "1");
}

console.log(findSolution(24));
// → (((1 * 3) + 5) * 3)
```

需要注意的是，这个方法不一定找到_最短_的操作顺序。它只是找一个符合条件的操作顺序。

不要担心，你如果还无法理解这个函数。我们一步步来，毕竟递归思维需要练习。

里面的函数`find`是真正的递归。它需要两个((参数))：当前数字和一个字符串记录得到该数字的步骤。如果它找到了一个解，它将返回记录了全部过程的字符串。如果没有解的话，它会返回`null`。

{{index null, "|| operator", "short-circuit evaluation"}}

因此，该函数需要做到以下三点。如果当前数字和目标一致的话，当前的字符串就是得到该数字的一种方法。所以直接返回字符串。如果当前数字大于目标，那么当前的解法没有意义继续下去，因为任何加法或乘法都会让当前数字越来越大。所以直接返回`null`。最后，如果当前数字没有目标大，该函数会调用自己两次，而以当前数字将这两种方法都尝试一遍。如果第一个调用返回的值不是`null`的话，它会被直接返回。否则，第二个值会被返回，即使它的最终结果是`null`。

{{index "call stack"}}

为了更好的理解这个函数的运行方式，我们看看它如何调用`find`，而找到一个能到达数字13的解。

```{lang: null}
find(1, "1")
  find(6, "(1 + 5)")
    find(11, "((1 + 5) + 5)")
      find(16, "(((1 + 5) + 5) + 5)")
        过大，返回
      find(33, "(((1 + 5) + 5) * 3)")
        过大，返回
    find(18, "((1 + 5) * 3)")
      过大，返回
  find(3, "(1 * 3)")
    find(8, "((1 * 3) + 5)")
      find(13, "(((1 * 3) + 5) + 5)")
        找到了！
```

缩进用来表示调用堆栈的深度。`find`第一次被调用时，它调用自己去探索以`(1 + 5)`为起点的解。这个调用进一步递归探索所有可能的方法。因为它找不到一个符合要求的方法，它返回`null`到第一个调用。这时，`||`运算开始了`(1 * 3)`的探索。这个探索比上一个幸运，经过两个递归之后，得到了目标数字。最里面的调用返回了一个字符串，此后的每个`||`都返回该字符串，最终返回这个解。

## Growing functions

{{index [function, definition]}}

此外还有两种可以在程序中使用函数的方法。

{{index repetition}}

第一个是当你发现你多次写相同的代码。你希望减少代码的重复性。更多的代码意味着更多出错的可能性，而且其他试图理解代码的人需要花更多的精力。因此，你把重复的代码提取出来，想一个适当的名字，组成一个函数。

第二个是当你发现你需要一个新的功能，而且这个功能应该有属于自己的函数。你会先写下这个函数的名字，之后是它的函数体。你甚至可能在编写该函数前，已经在代码中调用它了。

{{index [function, naming], [binding, naming]}}

给函数取名的难度可以很好的说明这个函数的概念是否清晰。让我们看个例题。

{{index "farm example"}}

我们想写一个打印2个数字的程序：农场中牛的数量和鸡的数量。为了确保这两个数字永远都是三位数，我们会在前面放一定数量的 0，而且后面会用`Cows`和`Chickens`表示数字代表的动物。

```{lang: null}
007 Cows
011 Chickens
```

这是一个需要两个参数的函数：牛的数量和鸡的数量。让我们开始吧。

```
function printFarmInventory(cows, chickens) {
  let cowString = String(cows);
  while (cowString.length < 3) {
    cowString = "0" + cowString;
  }
  console.log(`${cowString} Cows`);
  let chickenString = String(chickens);
  while (chickenString.length < 3) {
    chickenString = "0" + chickenString;
  }
  console.log(`${chickenString} Chickens`);
}
printFarmInventory(7, 11);
```

{{index ["length property", "for string"], "while loop"}}

在字符串后面加`.length`会告诉我们这个字符串的长度。所以，`while`循环会一直在字符串前面加0，直到它至少有三个字符。

大功告成！但就在我们准备把代码（外加发票）发给农场主时，她打电话过来表示她又开始养猪了。我们可不可把猪的数量也加入代码中？

{{index "copy-paste programming"}}

我们当然可以了。就在我们复制和粘贴代码时，我们开始思考。一定有个更好的办法。下面是第一次尝试：

```
function printZeroPaddedWithLabel(number, label) {
  let numberString = String(number);
  while (numberString.length < 3) {
    numberString = "0" + numberString;
  }
  console.log(`${numberString} ${label}`);
}

function printFarmInventory(cows, chickens, pigs) {
  printZeroPaddedWithLabel(cows, "Cows");
  printZeroPaddedWithLabel(chickens, "Chickens");
  printZeroPaddedWithLabel(pigs, "Pigs");
}

printFarmInventory(7, 11, 3);
```

{{index [function, naming]}}

成功了！但这个函数的名字，`printZeroPaddedWithLabel`，听上去很怪。它在一个函数中包含了三件事：打印、在字符串前加0、和加一个标签。

{{index "zeroPad function"}}

与其把代码中重复的部分全部提取出来，我们试图提取一个_概念_。

```
function zeroPad(number, width) {
  let string = String(number);
  while (string.length < width) {
    string = "0" + string;
  }
  return string;
}

function printFarmInventory(cows, chickens, pigs) {
  console.log(`${zeroPad(cows, 3)} Cows`);
  console.log(`${zeroPad(chickens, 3)} Chickens`);
  console.log(`${zeroPad(pigs, 3)} Pigs`);
}

printFarmInventory(7, 16, 3);
```

{{index readability, "pure function"}}

一个拥有明显名字的函数，比如`zeroPad`，会让他人更容易理解代码。这种函数在很多情况下，而不仅局限在当前的程序中，都适用。比如，你可以用它打印对齐的数字表。

{{index [interface, design]}}

函数_应该_有多聪明和多能化？我们可以写任何东西，从一个简单的只能将一个数字变成三个字符长的函数，到一个可以处理分数、负数、对齐小数点等复杂且广义的数字格式化系统，

一个实用的原则是不要画蛇添足，除非你非常确定你会用到这个功能。你也许会试图为每个功能写一个通用的((框架))。一定要抵制这种冲动。你只是在写多余的代码，并不是在工作。

{{id pure}}
## Functions and side effects

{{index "side effect", "pure function", [function, purity]}}

函数可以大体分为两种：产生副作用和返回值。（当然一个函数可以在产生副作用的同时，返回一个值）

{{index reuse}}

在 ((farm example))例子中，第一个辅助函数`printZeroPaddedWithLabel`就是因其副作用被调用的：它打印一行字。第二个版本中，`zeroPad`是因为它返回的值而被调用的。第二个函数比第一个函数的通途更广。返回值的函数比副作用的函数更容易以新的方法被结合使用。

{{index substitution}}

_纯_函数是一种特定的产生值的函数。它不仅没有副作用，也不会依赖其他代码中的副作用。比如，它不需要全局变量，因为这些值很可能改变。纯函数有一个很美好的特征，只要调用的参数一样，它返回的值就一样。它返回的值可代替此函数的调用，且无需更改代码本身的意思。当你不确定一个纯函数是否工作时，你可以通过简单的调用来测试它。并确信如果它在简单的测试中工作，那么它在任何环境中都会工作。非纯函数往往需要更多的框架进行测试。

{{index optimization, "console.log"}}

尽管如此，编写非纯函数时也不必难过，或试图把它们从代码中清除掉。副作用往往有用。比如，纯函数版的`console.log`并不存在，而`console.log`也非常实用。有些运算符在副作用下也更容易表达。因此计算速度可能是一个避免纯函数的原因。

## Summary

本章探讨了如何写函数。关键词`function`在表达式中，可以创建一个函数值。在语句中，它可以用了定义一个变量，而该变量的值就是一个函数。箭头函数也是一个创建函数的方法。

```
// 定义 f 用来存一个函数值
const f = function(a) {
  console.log(a + 2);
};

// 创建函数 g
function g(a, b) {
  return a * b * 3.5;
}

// 一个简洁的函数值
let h = a => a % 3;
```

理解函数的关键概念是理解作用域。每个块语句都创建一个新的作用域。在一个作用域中的参数和变量都仅限本地，在外界不存在。以`var`定义的变量略有不同，它们属于最近的函数作用域或全局作用域。

把程序的任务分成不同的函数。你不需要重复自己，而且函数可以进行代码分类，而更好的整理一个程序。

## Exercises

### Minimum

{{index "Math object", "minimum (exercise)", "Math.min function", minimum}}


[上一章](program_structure#return_values)中介绍了标准函数`Math.min`，它返回最小值。我们现在可以写个类似的函数。写一个函数`min`，它有两个参数，且返回最小的那个。

{{if interactive

```{test: no}
// 你的代码

console.log(min(0, 10));
// → 0
console.log(min(0, -10));
// → -10
```
if}}

{{hint

{{index "minimum (exercise)"}}

你如果对小括号和大括号的放置位置有问题的话，可以复制一个例题再修改。

{{index "return keyword"}}

一个函数可以有多个`return`语句。

hint}}

### Recursion

{{index recursion, "isEven (exercise)", "even number"}}

`%`（模赋值）可以通过`% 2`来测试一个数字是奇数还是偶数。还有一个方法可以测试一个正整数是奇数还是偶数：

- 0 是偶数。

- 1 是奇数。

- 任何数字 _N_，它和 _N_ - 2 的奇偶数相同。

根据上述，定义一个递归函数`isEven`。该函数有一个参数（一个正整数），并返回一个布尔值。

{{index "stack overflow"}}

用它测试 50 和 75。在测试 -1。你知道原因么？你有解决的方法么？

{{if interactive

```{test: no}
// 你的代码

console.log(isEven(50));
// → true
console.log(isEven(75));
// → false
console.log(isEven(-1));
// → ??
```

if}}

{{hint

{{index "isEven (exercise)", ["if keyword", chaining], recursion}}

你的函数很可能和递归[例题]](functions#recursive_puzzle)中`findSolution`的子函数`find`相似。用一个`if`/`else if`/`else`组测试上述的三种情况。最后的`else`代表第三种情况，需要使用递归。每个分支都应该有一个`return`语句，或者以其他方式返回一个具体的值。

{{index "stack overflow"}}

当提供一个负数时，这个函数会以一个更负的数不断递归。从而离结果越来越远。它最终会因为没有足够内存而停止。

hint}}

### Bean counting

{{index "bean counting (exercise)", [string, indexing], "zero-based counting", ["length property", "for string"]}}

你可以通过`"string"[N]`得到一个字符串中的第 N 个字符。返回的值是一个仅有一个字符的字符串（比如，`"b"`）。第一个字符的位置是 0，最后一个字符的位置是 `string.length - 1`。也就是说，一个拥有两个字符的字符串，它的长度是 2，但是它的字符的位置分别是 0 和 1。

写一个有字符串参数的函数`countBs`，它返回一个数字，表示字符串中大写字母"B"出现的次数。

在写一个类似`countBs`的函数`countChar`，只不过它的第二个参数代表要找寻的字符（不仅限于大写字母"B"）。用新函数重新`countBs`。

{{if interactive

```{test: no}
// 你的代码

console.log(countBs("BBC"));
// → 2
console.log(countChar("kakkerlak", "k"));
// → 4
```

if}}

{{hint

{{index "bean counting (exercise)", ["length property", "for string"], "counter variable"}}

这个函数需要一个((循环))去检测每一个字符。他可以从 0 跑至该字符串的长度（`< string.length`）。如果某个位置的字符和该函数寻找的字符一致的话，一个用来计数的变量需要加 1。在循环结束后，返回这个计数的变量。

{{index "local binding"}}

用关键词`let`和`const`来管理函数中的所有本地变量。

hint}}
