{{meta {load_files: ["code/chapter/06_object.js"], zip: "node/html"}}}

# The Secret Life of Objects

{{quote {author: "Barbara Liskov", title: "Programming with Abstract Data Types", chapter: true}

编写一种特殊的程序会得到一个抽象数据类型[……]根据可对其进行的操作而定义的类型。

quote}}

{{index "Liskov, Barbara", "abstract data type"}}

{{figure {url: "img/chapter_picture_6.jpg", alt: "Picture of a rabbit with its proto-rabbit", chapter: framed}}}

[第四章](data)介绍了 JavaScript 中的对象。编程世界中，有一个_((面向对象程序设计))_的存在，一组利用对象（和其相关理念）为核心的的编程典范。

尽管大家无法对其具体定义达成共识，面向对象程序设计依旧影响了许多编程语言，包括 JavaScript，的设计理念。本章将探讨在 JavaScript 中如何运用这些理念。

## Encapsulation

{{index encapsulation, isolation, modularity}}

面向对象程序设计的核心理念是将程序分解成许多小程序，每个小程序负责管理自己的状态。

如此一来，一些每个子程序可以本地储存其具体的细节。其他子程序完全不需要知道这些内容的存在。因此每当这些细节有所更改的时候，只有其相关的子程序需被更改。

{{id interface}}
{{index [interface, object]}}

不同的子程序通过_接口_于彼此沟通。接口是一组有限的，更抽象却有用的函数和变量，从而隐藏了其具体的细节。

{{index "public properties", "private properties", "access control", [method, interface]}}

这类的子程序运用了((对象))的模型。他们的接口包含了一组具体的方法和属性。接口中包含的属性为_公共_。反之，外界代码无法接触的属性则是_专用_。

{{index "underscore character"}}

许多语言提供区分公共和专用属性的方法，并防止一切外界代码调用专属属性的可能性。JavaScript，因为其极简理念，没有（目前为止）这个区分。此工作目前仍在进行中。

尽管 JavaScript 没有内置的区分，程序员们_仍_成功的运用了该理念。一般来讲，公共接口在注释和文档中有记载。此外，大家习惯在专用属相的名字前加一个下划线（`_`）。

将接口与其细节分开是件好事。通常又作_((封装))_。

{{id obj_methods}}

## Methods

{{index "rabbit example", method, [property, access]}}

方法其实就是带有函数值的属性。下面就是一个简单的方法：

```
let rabbit = {};
rabbit.speak = function(line) {
  console.log(`兔子说 '${line}'`);
};

rabbit.speak("我活着。");
// → 兔子说 '我活着。'
```

{{index "this binding", "method call"}}

通常一个方法需要对调用它的对象做些什么。当一个函数以方法的形式（通过属性查找调用，比如 `object.method()`）被调用时，变量 `this` 会在其块语句中指向调用它的对象。

```{includeCode: "top_lines:6", test: join}
function speak(line) {
  console.log(`${this.type}兔子说 '${line}'`);
}
let whiteRabbit = {type: "白", speak};
let hungryRabbit = {type: "饿", speak};

whiteRabbit.speak("我的耳朵和胡须" + "现在已经很晚了！");
// → 白兔子说 '我的耳朵和胡须 现在已经很晚了！'
hungryRabbit.speak("我现在需要些胡萝卜");
// → 饿兔子说 '我现在需要些胡萝卜'
```

{{id call_method}}

{{index "call method"}}

你可以把 `this` 想象成一个以不同形式递交进来的额外的((参数))。你如果想明确的递交进来，你需要调用函数的 `call` 方法。该方法的第一个参数就是 `this`，所有其他参数都会被当作普通的参数。

```
speak.call(hungryRabbit, "Burp!");
// → 饿兔子说 'Burp!'
```

因为每个函数都有自己的 `this` 变量，其值完全取决于该函数的调用方式，因此你不可以在由关键词 `function` 定义的正常函数的作用域内使用 `this`。

{{index "this binding", "arrow function"}}

箭头函数是不一样的——它们绑定的不是属于它们自己的 `this`，而是它们所在作用域内的 `this`。所以下面的代码，在本地函数中使用 `this`，是被允许的：

```
function normalize() {
  console.log(this.coords.map(n => n / this.length));
}
normalize.call({coords: [0, 2, 3], length: 5});
// → [0, 0.4, 0.6]
```

{{index "map method"}}

我如果在 `map` 的参数中用了 `function` 关键词，那么这个代码将不工作。

{{id prototypes}}

## Prototypes

{{index "toString method"}}

注意看。

```
let empty = {};
console.log(empty.toString);
// → function toString(){…}
console.log(empty.toString());
// → [object Object]
```

{{index magic}}

我从一个空对象中得到了一个属性。简直就是魔术！

{{index [property, inheritance], [object, property]}}

其实不然。我只是隐藏了一些有关 JavaScript 对象的运作信息。除了它们自身的属性外，大部分对象还有一个_((原型))_。原型是另一个作为备用的对象。当调用一个对象的不存在的属性时，JavaScript 会在它的原型中查找同一属性，之后是原型的原型，以此类推。

{{index "Object prototype"}}

那么，一个空对象的((原型))是什么呢？它是伟大的祖先原型，几乎是所有对象的先祖，`Object.prototype`。

```
console.log(Object.getPrototypeOf({}) ==
            Object.prototype);
// → true
console.log(Object.getPrototypeOf(Object.prototype));
// → null
```

{{index "getPrototypeOf function"}}

如你所猜想的，`Object.getPrototypeOf` 返回一个对象的原型。

{{index "toString method"}}

JavaScript 对象的原型关系是一个((树))型结构，它的根节点就是 `Object.prototype`。它提供了几个所有对象都有方法，比如 `toString`，会把对象转换成一个字符串。

{{index inheritance, "Function prototype", "Array prototype", "Object prototype"}}

许多对象的直接原型都不是 `Object.prototype`，而是另一个提供了不同默认属性的对象。函数来自于 `Function.prototype`，而数组来自于 `Array.prototype`。

```
console.log(Object.getPrototypeOf(Math.max) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf([]) ==
            Array.prototype);
// → true
```

{{index "Object prototype"}}

此类原型对象本身也有一个原型，通常是 `Object.prototype`，所以它间接的提供了类似 `toString` 等方法。

{{index "rabbit example", "Object.create function"}}

你可以通过 `Object.create` 创建一个有指定((原型))的对象。

```
let protoRabbit = {
  speak(line) {
    console.log(`${this.type}兔子说 '${line}'`);
  }
};
let killerRabbit = Object.create(protoRabbit);
killerRabbit.type = "杀手";
killerRabbit.speak("SKREEEE!");
// → 杀手兔子说 'SKREEEE!'
```

{{index "shared property"}}

对象中类似 `speak(line)` 的属性表达式是定义方法的一种便捷方式。它创建了一个名为 `speak` 的属性，并给予其一个函数作为值。

"proto" 兔子类似一个装有所有兔子共同属性的容器。一个独立的兔子对象，比如杀手兔子，装有只对它有效的属性，比如上面例子中的 `type`，并从它的原型中衍生共享属性。

{{id classes}}

## Classes

{{index "object-oriented programming"}}

JavaScript 的((原型))体系可以被不严谨的理解为面向对象程序设计中的_((类))_。一个类定义了对象类型的形状——它拥有什么方法和属性。该对象则是这个类的一个_((实例))_。

{{index [property, inheritance]}}

原型对定义类的所有实例的共享属性值很有用，比如((方法))。每个实例的不同属性，比如兔子的 `type`，需要在每个对象中储存。

{{id constructors}}

创建一个类的实例，你需要建一个衍生于该原型的对象，但你_也_需要确认该对象本身有此类实例应有的所有属性。这就是_((构造器))_函数的作用。

```
function makeRabbit(type) {
  let rabbit = Object.create(protoRabbit);
  rabbit.type = type;
  return rabbit;
}
```

{{index "new operator", "this binding", "return keyword", [object, creation]}}

JavaScript 提供了一个简介的定义这类函数的方法。你如果在调用函数前加关键词 `new`，这个函数就会被当作构造器。这意味着一个拥有正确原型的对象被自动创建了，在函数中绑定 `this`，并在该函数的最后返回。

{{index "prototype property"}}

创建对象时用的原型是通过构造器的 `prototype` 属性得到的。

{{index "rabbit example"}}

```
function Rabbit(type) {
  this.type = type;
}
Rabbit.prototype.speak = function(line) {
  console.log(`${this.type}兔子说 '${line}'`);
};

let weirdRabbit = new Rabbit("奇怪");
```

{{index constructor}}

构造器（实际上是所有函数）自动拥有属性 `prototype`，默认为一个从 `Object.prototype` 衍生出来的空对象。你可以用一个新对象覆盖它。或者对现有的对象添加属性，如上例。

{{index capitalization}}

按照惯例，构造器的名字的首写字母需要大写，以便和其他函数区分开来。

{{index "prototype property", "getPrototypeOf function"}}

了原型和构造器（通过其 `prototype` 属性）以及原型和对象（通过 `Object.getPrototypeOf`）间的关联非常重要。构造器的真正的原型是 `Function.prototype`，因为构造器本身就是函数。它的 `prototype`_属性_则是其实例所拥有的原型。

```
console.log(Object.getPrototypeOf(Rabbit) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf(weirdRabbit) ==
            Rabbit.prototype);
// → true
```

## Class notation

JavaScript 的((类))是一个有((原型))属性的((构造器))。在 2015年前，它们是这么运行的，你也必须如此写。现如今，我们有个更简洁的写法。

```{includeCode: true}
class Rabbit {
  constructor(type) {
    this.type = type;
  }
  speak(line) {
    console.log(`${this.type}兔子说 '${line}'`);
  }
}

let killerRabbit = new Rabbit("杀手");
let blackRabbit = new Rabbit("黑");
```

{{index "rabbit example", [braces, class]}}

关键词 `class` 开始一个((类声明))，允许我们同时定义一个构造器和一组方法。在该声明块中，可拥有任意数字的方法。名为 `constructor` 的会被特殊对待。它其实就是构造器，这里绑定到 `Rabbit` 上。其他的都被打包在该构造器的原型中。所以，上面的类声明和前面所看到的构造器定义是一样的。只是好看了许多。

{{index ["class declaration", properties]}}

类声明目前只支持添加_方法_——值为函数的属性——到((原型))中。你如果想存一个非函数的值会极为不便。下个版本的 JavaScript 也许会对此有所改进。现在，你可以在定义类后，通过直接改写原型的方法创建此类属性。

同 `function` 一样，`class` 也可以在语句和表达式中使用。用于表达式时，它并不会定义一个变量，而是创建一个构造器作为值。你可以在类表达式中省略该类的名字。

```
let object = new class { getWord() { return "hello"; } };
console.log(object.getWord());
// → hello
```

## Overriding derived properties

{{index "shared property", overriding, [property, inheritance]}}

当你给一个对象添加属性时，无论该属性是否存在于原型中，该属性都会被添加到该对象_自身_。如果原型中已有同名属性存在，这个属性将会被因此在对象自身属性的背后，因此不会对该对象有任何影响。

```
Rabbit.prototype.teeth = "小";
console.log(killerRabbit.teeth);
// → 小
killerRabbit.teeth = "长，尖，且血淋淋的";
console.log(killerRabbit.teeth);
// → 长，尖，且血淋淋的
console.log(blackRabbit.teeth);
// → 小
console.log(Rabbit.prototype.teeth);
// → 小
```

{{index [prototype, diagram]}}

下图描绘了代码运行后的情况。`Rabbit` 和 `Object` ((原型))类似背景般在 `killerRabbit` 的后面。只有在对象本身查无属性时，才会找它们查询。

{{figure {url: "img/rabbits.svg", alt: "Rabbit object prototype schema",width: "8cm"}}}

{{index "shared property"}}

覆盖原型中的属性会很有用。如兔子牙齿的例子所示，覆盖可以用来表达通用类的实例中优异的属性的同时，如那些普通的对象保持原型中的标准值。

{{index "toString method", "Array prototype", "Function prototype"}}

覆盖也使标准函数和数组原型用于和普通对象原型不同的 `toString`。

```
console.log(Array.prototype.toString ==
            Object.prototype.toString);
// → false
console.log([1, 2].toString());
// → 1,2
```

{{index "toString method", "join method", "call method"}}

对数组调用 `toString` 得到的结果类似对其调用 `.join(",")`——它在数组的值中放入逗号。直接给一个数组调用 `Object.prototype.toString` 会得到一个不同的字符串。这个函数不认识数组，所以它直接返回一个中括号，包含了 _object_ 和该类型的名字。

```
console.log(Object.prototype.toString.call([1, 2]));
// → [object Array]
```

## Maps

{{index "map method"}}

[上一章](higher_order#map)中我们见到 _map_ 如何对一个数据结构中的每个元素的函数调动而改变其本身。然而编程中，同一个单词也用来形容一个相关却非常不同的东西。

{{index "map (data structure)", "ages example", ["data structure", map]}}

_map_（名词）是一个可以把值（键）和其他值关联起来的数据结构。比如，你也许想把名字和年龄挂钩。这当然可以通过对象来完成。

```
let ages = {
  Boris: 39,
  Liang: 22,
  Júlia: 62
};

console.log(`Júlia ${ages["Júlia"]}岁`);
// → Júlia 62岁
console.log("已知 Jack 的年龄么？", "Jack" in ages);
// → 已知 Jack 的年龄么？ false
console.log("已知 toString 的年龄么？", "toString" in ages);
// → 已知 toString 的年龄么？ true
```

{{index "Object.prototype", "toString method"}}

这里，map 的属性的名字就是人名，而属性的值就是他们的年龄。可是我们的对象中并没有名为 toString 的人。但是因为对象衍生自 `Object.prototype`，所以该属性显示存在。

{{index "Object.create function", prototype}}

因此用纯对象作为 map 是很危险的。这里当然有几种避免该问题的方法。首先，可以创建一个_没有_原型的对象。你如果把 `null` 作为参数递给 `Object.create`，产生的对象将不会衍生自 `Object.prototype`，也就可以安全的当作 map 使用。

```
console.log("toString" in Object.create(null));
// → false
```

{{index [property, naming]}}

对象属性名字必须是字符串。如果你需要一个键并不能轻易被转换成字符串的 map，比如对象，你就不能用对象代替 map。

{{index "Map class"}}

幸运的是，JavaScript 有一个名为 `Map` 的类，就是为此设计的。它储存一个映射，并允许任何类型的键。

```
let ages = new Map();
ages.set("Boris", 39);
ages.set("Liang", 22);
ages.set("Júlia", 62);

console.log(`Júlia ${ages.get("Júlia")}岁`);
// → Júlia is 62
console.log("已知 Jack 的年龄么？", ages.has("Jack"));
// → 已知 Jack 的年龄么？ false
console.log(ages.has("toString"));
// → false
```

{{index [interface, object], "set method", "get method", "has method", encapsulation}}

方法 `set`，`get`，和 `has` 是 `Map` 对象接口的一部分。写一个可以快速更新和搜索一大组值的数据结构并不简单，但这不是我们需要关心的。有人已经帮我们完成了，因此我们只需要通过这几个简单的接口来借助他们的劳动成果。

{{index "hasOwnProperty method", "in operator"}}

如果，因为某些原因，你需要把一个纯对象做 map 使用，可以通过 `Object.keys` 得到仅限该对象_本身_的键，而不是原型中的。或者可以通过 `in` 运算符；还有 `hasOwnProperty` 方法也会忽略掉对象原型。

```
console.log({x: 1}.hasOwnProperty("x"));
// → true
console.log({x: 1}.hasOwnProperty("toString"));
// → false
```

## Polymorphism

{{index "toString method", "String function", polymorphism, overriding, "object-oriented programming"}}

当你对一个对象调用 `String` 函数（将一个值转变成字符串）时，它会在该对象上调用 `toString` 方法，试图创建一个有意义的字符串。前面讲过，有些标准的原型会定义他们自己的 `toString`，好让他们创建一个比 `"[object object]"` 更有意义的字符串。你也可以如此。

```{includeCode: "top_lines: 3"}
Rabbit.prototype.toString = function() {
  return `一个 ${this.type} 兔子`;
};

console.log(String(blackRabbit));
// → 一个 black 兔子
```

{{index "object-oriented programming", [interface, object]}}

这是一个伟大想法中的简单的实例。当编写一段代码和某些接口的对象运行时，比如这里所指的 `toString` 方法，任何支持该接口的对象都会拥有这段代码，而且会自然而然的工作。

这就是_多态性_。多态的代码可兼容不同类型的值，只要这些值支持该接口。

{{index "for/of loop", "iterator interface"}}

在[第四章](data#for_of_loop)，我说过一个 `for`/`of` 可循环几个不同的数据类型。这又是一个多态性的例子，这类循环要求数据结构暴露某种具体的接口，而数组和字符串正符合要求。我们也可以将这个接口加入到你自己的对象中！但首先，我们需要知道符号（symbol）是什么。

## Symbols

多个接口可以用同一个属性名代表不同的东西。比如，我可以定义一个接口 `toString` 把一个对象转换成一根毛线。一个对象不可能同时完成上面的接口和标准的 `toString`。

这个想法很糟糕，好在这个问题不是那么常见。大部分 JavaScript 程序员完全不会这么想。但语言设计者，他们的工作就是思考这类问题，为我们提供了一个办法。

{{index "Symbol function", [property, naming]}}

我前面说属性名是字符串，这其实并不完全准确。他们通常是字符串，但也可以是_((符号))_。符号是由 `Symbol` 函数创建的值。不同于字符串，新创建的符号是独一无二的 —— 你无法创建相同名字的值。

```
let sym = Symbol("name");
console.log(sym == Symbol("name"));
// → false
Rabbit.prototype[sym] = 55;
console.log(blackRabbit[sym]);
// → 55
```

当你将递交给 `Symbol` 的字符串转换成的字符串，它会被包括在内，而且在控制台中显示该符号时，也可更轻松的识别它。但除此之外，它没有其他意义 —— 多个符号可以拥有同一个名字。

因为独一无二且实用的属性名使符号成为定义接口名的不二人选，他们可以和其他属性和平相处。

```{includeCode: "top_lines: 1"}
const toStringSymbol = Symbol("toString");
Array.prototype[toStringSymbol] = function() {
  return `${this.length} cm of blue yarn`;
};

console.log([1, 2].toString());
// → 1,2
console.log([1, 2][toStringSymbol]());
// → 2 cm of blue yarn
```

{{index [property, naming]}}

通过((方括号))，我们可以在对象表达式和类中包括符号属性。类似于方括访问属性的，该方法会评估属性名，因此允许我们用一个变量来指符号。

```
let stringObject = {
  [toStringSymbol]() { return "a jute rope"; }
};
console.log(stringObject[toStringSymbol]());
// → a jute rope
```

## The iterator interface

{{index "iterable interface", "Symbol.iterator symbol", "for/of loop"}}

给 `for`/`of` 的对象应该是_可迭代_的。所以它有一个名为 `Symbol.iterator` 的符号（被语言定义的符号值，储存 `Symbol` 函数的属性）。

{{index "iterator interface", "next method"}}

被调用时，该方法应返回一个提供第二接口，_iterator_，的对象。这才是真正迭代的东西。它有一个 `next` 方法返回下一个值。该值理应是一个有 `value` 属性的对象，提供下一个值，如果存在的话，或者 `done` 属性，在没有更多值时时 `true`，否则是 `false`。

注意 `next`，`value`，和 `done` 属性名是纯字符串，而不是符号。只有 `Symbole.iterator`，很可能被添加到_许多_不同的对象上，是一个符号。

我们可以直接用这个接口。

```
let okIterator = "OK"[Symbol.iterator]();
console.log(okIterator.next());
// → {value: "O", done: false}
console.log(okIterator.next());
// → {value: "K", done: false}
console.log(okIterator.next());
// → {value: undefined, done: true}
```

{{index "matrix example", "Matrix class", [array, "as matrix"]}}

{{id matrix}}

让我们写一个迭代的数据结构。我们将创建一个 _matrix_ 类，代表一个二维数组。

```{includeCode: true}
class Matrix {
  constructor(width, height, element = (x, y) => undefined) {
    this.width = width;
    this.height = height;
    this.content = [];

    for (let y = 0; y < height; y++) {
      for (let x = 0; x < width; x++) {
        this.content[y * width + x] = element(x, y);
      }
    }
  }

  get(x, y) {
    return this.content[y * this.width + x];
  }
  set(x, y, value) {
    this.content[y * this.width + x] = value;
  }
}
```

这个类通过一个有 _width_ x _height_ 元素的数组来储存它的内容。这些元素是一行行储存的，比如，第五行的第三个元素（以零开始的索引）被存放在了 4 x _width + 2 的位置上。

构造器需要一个宽、一个高、和一个可选 `element` 函数用来填充原始值。可以通过 `get` 和 `set` 方法来获取和更新矩阵中的元素。

在迭代一个矩阵时，我们通常对元素的位置和元素本身都有兴趣。所以我们的迭代器返回一个有 `x`，`y` 和 `value` 属性的对象。

{{index "MatrixIterator class"}}

```{includeCode: true}
class MatrixIterator {
  constructor(matrix) {
    this.x = 0;
    this.y = 0;
    this.matrix = matrix;
  }

  next() {
    if (this.y == this.matrix.height) return {done: true};

    let value = {x: this.x,
                 y: this.y,
                 value: this.matrix.get(this.x, this.y)};
    this.x++;
    if (this.x == this.matrix.width) {
      this.x = 0;
      this.y++;
    }
    return {value, done: false};
  }
}
```

这个类通过它的 `x` 和 `y` 属性记录着在矩阵上迭代的进度。`next` 方法开始先检测如果以达到矩阵的底部。如果没有，它_先_创建一个对象储存当前的值，_再_更新它的位置，如果有需要的话移动到下一个行。

我们现在让 `Matrix` 类可迭代。本书中，我偶尔会用事后原型处理给类添加方法，以便独立的代码可以保持小巧。在正常的程序中，并没有把代码分成更小块的必要，你可以直接在类中定义这些方法。

```{includeCode: true}
Matrix.prototype[Symbol.iterator] = function() {
  return new MatrixIterator(this);
};
```

{{index "for/of loop"}}

我们现在可以通过 `for`/`of` 迭代一个矩阵。

```
let matrix = new Matrix(2, 2, (x, y) => `value ${x},${y}`);
for (let {x, y, value} of matrix) {
  console.log(x, y, value);
}
// → 0 0 value 0,0
// → 1 0 value 1,0
// → 0 1 value 0,1
// → 1 1 value 1,1
```

## Getters, setters, and statics

{{index [interface, object], [property, definition], "Map class"}}

Interfaces often consist mostly of methods, but it is also okay to
include properties that hold non-function values. For example, `Map`
objects have a `size` property that tells you how many keys are stored
in them.

It is not even necessary for such an object to compute and store such
a property directly in the instance. Even properties that are accessed
directly may hide a method call. Such methods are called
_((getter))s_, and they are defined by writing `get` in front of the
method name in an object expression or class declaration.

```{test: no}
let varyingSize = {
  get size() {
    return Math.floor(Math.random() * 100);
  }
};

console.log(varyingSize.size);
// → 73
console.log(varyingSize.size);
// → 49
```

{{index "temperature example"}}

Whenever someone reads from this object's `size` property, the
associated method is called. You can do a similar thing when a
property is written to, using a _((setter))_.

```{test: no, startCode: true}
class Temperature {
  constructor(celsius) {
    this.celsius = celsius;
  }
  get fahrenheit() {
    return this.celsius * 1.8 + 32;
  }
  set fahrenheit(value) {
    this.celsius = (value - 32) / 1.8;
  }

  static fromFahrenheit(value) {
    return new Temperature((value - 32) / 1.8);
  }
}

let temp = new Temperature(22);
console.log(temp.fahrenheit);
// → 71.6
temp.fahrenheit = 86;
console.log(temp.celsius);
// → 30
```

The `Temperature` class allows you to read and write the temperature
in either degrees ((Celsius)) or degrees ((Fahrenheit)), but
internally it stores only Celsius and automatically converts to
and from Celsius in the `fahrenheit` getter and setter.

{{index "static method"}}

Sometimes you want to attach some properties directly to your
constructor function, rather than to the prototype. Such methods won't
have access to a class instance but can, for example, be used to
provide additional ways to create instances.

Inside a class declaration, methods that have `static` written before
their name are stored on the constructor. So the `Temperature` class
allows you to write `Temperature.fromFahrenheit(100)` to create a
temperature using degrees Fahrenheit.

## Inheritance

{{index inheritance, "matrix example", "object-oriented programming", "SymmetricMatrix class"}}

Some matrices are known to be _symmetric_. If you mirror a symmetric
matrix around its top-left-to-bottom-right diagonal, it stays the
same. In other words, the value stored at _x_,_y_ is always the same
as that at _y_,_x_.

Imagine we need a data structure like `Matrix` but one that enforces
the fact that the matrix is and remains symmetrical. We could write it
from scratch, but that would involve repeating some code very similar
to what we already wrote.

{{index overriding, prototype}}

JavaScript's prototype system makes it possible to create a _new_
class, much like the old class, but with new definitions for some of
its properties. The prototype for the new class derives from the old
prototype but adds a new definition for, say, the `set` method.

In object-oriented programming terms, this is called
_((inheritance))_. The new class inherits properties and behavior from
the old class.

```{includeCode: "top_lines: 17"}
class SymmetricMatrix extends Matrix {
  constructor(size, element = (x, y) => undefined) {
    super(size, size, (x, y) => {
      if (x < y) return element(y, x);
      else return element(x, y);
    });
  }

  set(x, y, value) {
    super.set(x, y, value);
    if (x != y) {
      super.set(y, x, value);
    }
  }
}

let matrix = new SymmetricMatrix(5, (x, y) => `${x},${y}`);
console.log(matrix.get(2, 3));
// → 3,2
```

The use of the word `extends` indicates that this class shouldn't be
directly based on the default `Object` prototype but on some other class. This
is called the _((superclass))_. The derived class is the
_((subclass))_.

To initialize a `SymmetricMatrix` instance, the constructor calls its
superclass's constructor through the `super` keyword. This is necessary
because if this new object is to behave (roughly) like a `Matrix`, it
is going to need the instance properties that matrices have. 
To ensure the matrix is symmetrical, the constructor wraps the
`element` function to swap the coordinates for values below the
diagonal.

The `set` method again uses `super` but this time not to call the
constructor but to call a specific method from the superclass's set of
methods. We are redefining `set` but do want to use the original
behavior. Because `this.set` refers to the _new_ `set` method, calling
that wouldn't work. Inside class methods, `super` provides a way to
call methods as they were defined in the superclass.

Inheritance allows us to build slightly different data types from
existing data types with relatively little work. It is a fundamental
part of the object-oriented tradition, alongside encapsulation and
polymorphism. But while the latter two are now generally regarded as
wonderful ideas, inheritance is more controversial.

{{index complexity, reuse, "class hierarchy"}}

Whereas ((encapsulation)) and polymorphism can be used to _separate_
pieces of code from each other, reducing the tangledness of the
overall program, ((inheritance)) fundamentally ties classes together,
creating _more_ tangle. When inheriting from a class, you usually have
to know more about how it works than when simply using it. Inheritance
can be a useful tool, and I use it now and then in my own programs,
but it shouldn't be the first tool you reach for, and you probably
shouldn't actively go looking for opportunities to construct class
hierarchies (family trees of classes).

## The instanceof operator

{{index type, "instanceof operator", constructor, object}}

It is occasionally useful to know whether an object was derived from a
specific class. For this, JavaScript provides a binary operator called
`instanceof`.

```
console.log(
  new SymmetricMatrix(2) instanceof SymmetricMatrix);
// → true
console.log(new SymmetricMatrix(2) instanceof Matrix);
// → true
console.log(new Matrix(2, 2) instanceof SymmetricMatrix);
// → false
console.log([1] instanceof Array);
// → true
```

{{index inheritance}}

The operator will see through inherited types, so a `SymmetricMatrix`
is an instance of `Matrix`. The operator can also be applied to
standard constructors like `Array`. Almost every object is an instance
of `Object`.

## Summary

So objects do more than just hold their own properties. They have
prototypes, which are other objects. They'll act as if they have
properties they don't have as long as their prototype has that
property. Simple objects have `Object.prototype` as their prototype.

Constructors, which are functions whose names usually start with a
capital letter, can be used with the `new` operator to create new
objects. The new object's prototype will be the object found in the
`prototype` property of the constructor. You can make good use of this
by putting the properties that all values of a given type share into
their prototype. There's a `class` notation that provides a clear way
to define a constructor and its prototype.

You can define getters and setters to secretly call methods every time
an object's property is accessed. Static methods are methods stored in
a class's constructor, rather than its prototype.

The `instanceof` operator can, given an object and a constructor, tell
you whether that object is an instance of that constructor.

One useful thing to do with objects is to specify an interface for
them and tell everybody that they are supposed to talk to your object
only through that interface. The rest of the details that make up your
object are now _encapsulated_, hidden behind the interface.

More than one type may implement the same interface. Code written to
use an interface automatically knows how to work with any number of
different objects that provide the interface. This is called
_polymorphism_.

When implementing multiple classes that differ in only some details,
it can be helpful to write the new classes as _subclasses_ of an
existing class, _inheriting_ part of its behavior.

## Exercises

{{id exercise_vector}}

### A vector type

{{index dimensions, "Vec class", coordinates, "vector (exercise)"}}

Write a ((class)) `Vec` that represents a vector in two-dimensional
space. It takes `x` and `y` parameters (numbers), which it should save
to properties of the same name.

{{index addition, subtraction}}

Give the `Vec` prototype two methods, `plus` and `minus`, that take
another vector as a parameter and return a new vector that has the sum
or difference of the two vectors' (`this` and the parameter) _x_ and
_y_ values.

Add a ((getter)) property `length` to the prototype that computes the
length of the vector—that is, the distance of the point (_x_, _y_) from
the origin (0, 0).

{{if interactive

```{test: no}
// Your code here.

console.log(new Vec(1, 2).plus(new Vec(2, 3)));
// → Vec{x: 3, y: 5}
console.log(new Vec(1, 2).minus(new Vec(2, 3)));
// → Vec{x: -1, y: -1}
console.log(new Vec(3, 4).length);
// → 5
```
if}}

{{hint

{{index "vector (exercise)"}}

Look back to the `Rabbit` class example if you're unsure how `class`
declarations look.

{{index Pythagoras, "defineProperty function", "square root", "Math.sqrt function"}}

Adding a getter property to the constructor can be done by putting the
word `get` before the method name. To compute the distance from (0, 0)
to (x, y), you can use the Pythagorean theorem, which says that the
square of the distance we are looking for is equal to the square of
the x-coordinate plus the square of the y-coordinate. Thus, [√(x^2^ +
y^2^)]{if html}[[$\sqrt{x^2 + y^2}$]{latex}]{if tex} is the number you
want, and `Math.sqrt` is the way you compute a square root in
JavaScript.

hint}}

### Groups

{{index "groups (exercise)", "Set class", "Group class", "set (data structure)"}}

{{id groups}}

The standard JavaScript environment provides another data structure
called `Set`. Like an instance of `Map`, a set holds a collection of
values. Unlike `Map`, it does not associate other values with those—it
just tracks which values are part of the set. A value can be part
of a set only once—adding it again doesn't have any effect.

{{index "add method", "delete method", "has method"}}

Write a class called `Group` (since `Set` is already taken). Like
`Set`, it has `add`, `delete`, and `has` methods. Its constructor
creates an empty group, `add` adds a value to the group (but only if
it isn't already a member), `delete` removes its argument from the
group (if it was a member), and `has` returns a Boolean value
indicating whether its argument is a member of the group.

{{index "=== operator", "indexOf method"}}

Use the `===` operator, or something equivalent such as `indexOf`, to
determine whether two values are the same.

{{index "static method"}}

Give the class a static `from` method that takes an iterable object
as argument and creates a group that contains all the values produced
by iterating over it.

{{if interactive

```{test: no}
class Group {
  // Your code here.
}

let group = Group.from([10, 20]);
console.log(group.has(10));
// → true
console.log(group.has(30));
// → false
group.add(10);
group.delete(10);
console.log(group.has(10));
// → false
```

if}}

{{hint

{{index "groups (exercise)", "Group class", "indexOf method", "includes method"}}

The easiest way to do this is to store an array of group members
in an instance property. The `includes` or `indexOf` methods can be
used to check whether a given value is in the array.

{{index "push method"}}

Your class's ((constructor)) can set the member collection to an empty
array. When `add` is called, it must check whether the given value is
in the array or add it, for example with `push`, otherwise.

{{index "filter method"}}

Deleting an element from an array, in `delete`, is less
straightforward, but you can use `filter` to create a new array
without the value. Don't forget to overwrite the property holding the
members with the newly filtered version of the array.

{{index "for/of loop", "iterable interface"}}

The `from` method can use a `for`/`of` loop to get the values out of
the iterable object and call `add` to put them into a newly created
group.

hint}}

### Iterable groups

{{index "groups (exercise)", [interface, object], "iterator interface", "Group class"}}

{{id group_iterator}}

Make the `Group` class from the previous exercise iterable. Refer 
to the section about the iterator interface earlier in the chapter if
you aren't clear on the exact form of the interface anymore.

If you used an array to represent the group's members, don't just
return the iterator created by calling the `Symbol.iterator` method on
the array. That would work, but it defeats the purpose of this exercise.

It is okay if your iterator behaves strangely when the group is
modified during iteration.

{{if interactive

```{test: no}
// Your code here (and the code from the previous exercise)

for (let value of Group.from(["a", "b", "c"])) {
  console.log(value);
}
// → a
// → b
// → c
```

if}}

{{hint

{{index "groups (exercise)", "Group class", "next method"}}

It is probably worthwhile to define a new class `GroupIterator`.
Iterator instances should have a property that tracks the current
position in the group. Every time `next` is called, it checks whether
it is done and, if not, moves past the current value and returns it.

The `Group` class itself gets a method named by `Symbol.iterator`
that, when called, returns a new instance of the iterator class for
that group.

hint}}

### Borrowing a method

Earlier in the chapter I mentioned that an object's `hasOwnProperty`
can be used as a more robust alternative to the `in` operator when you
want to ignore the prototype's properties. But what if your map needs
to include the word `"hasOwnProperty"`? You won't be able to call that
method anymore because the object's own property hides the method
value.

Can you think of a way to call `hasOwnProperty` on an object that has
its own property by that name?

{{if interactive

```{test: no}
let map = {one: true, two: true, hasOwnProperty: true};

// Fix this call
console.log(map.hasOwnProperty("one"));
// → true
```

if}}

{{hint

Remember that methods that exist on plain objects come from
`Object.prototype`.

Also remember that you can call a function with a specific `this`
binding by using its `call` method.

hint}}
