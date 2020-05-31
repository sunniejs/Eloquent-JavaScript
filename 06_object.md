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

We saw the word _map_ used in the [previous chapter](higher_order#map)
for an operation that transforms a data structure by applying a
function to its elements. Confusing as it is, in programming the same
word is also used for a related but rather different thing.

{{index "map (data structure)", "ages example", ["data structure", map]}}

A _map_ (noun) is a data structure that associates values (the keys)
with other values. For example, you might want to map names to ages.
It is possible to use objects for this.

```
let ages = {
  Boris: 39,
  Liang: 22,
  Júlia: 62
};

console.log(`Júlia is ${ages["Júlia"]}`);
// → Júlia is 62
console.log("Is Jack's age known?", "Jack" in ages);
// → Is Jack's age known? false
console.log("Is toString's age known?", "toString" in ages);
// → Is toString's age known? true
```

{{index "Object.prototype", "toString method"}}

Here, the object's property names are the people's names, and the
property values are their ages. But we certainly didn't list anybody named
toString in our map. Yet, because plain objects derive from
`Object.prototype`, it looks like the property is there.

{{index "Object.create function", prototype}}

As such, using plain objects as maps is dangerous. There are several
possible ways to avoid this problem. First, it is possible to create
objects with _no_ prototype. If you pass `null` to `Object.create`,
the resulting object will not derive from `Object.prototype` and can
safely be used as a map.

```
console.log("toString" in Object.create(null));
// → false
```

{{index [property, naming]}}

Object property names must be strings. If you need a map whose
keys can't easily be converted to strings—such as objects—you cannot
use an object as your map.

{{index "Map class"}}

Fortunately, JavaScript comes with a class called `Map` that is
written for this exact purpose. It stores a mapping and allows any
type of keys.

```
let ages = new Map();
ages.set("Boris", 39);
ages.set("Liang", 22);
ages.set("Júlia", 62);

console.log(`Júlia is ${ages.get("Júlia")}`);
// → Júlia is 62
console.log("Is Jack's age known?", ages.has("Jack"));
// → Is Jack's age known? false
console.log(ages.has("toString"));
// → false
```

{{index [interface, object], "set method", "get method", "has method", encapsulation}}

The methods `set`, `get`, and `has` are part of the interface of the
`Map` object. Writing a data structure that can quickly update and
search a large set of values isn't easy, but we don't have to worry
about that. Someone else did it for us, and we can go through this
simple interface to use their work.

{{index "hasOwnProperty method", "in operator"}}

If you do have a plain object that you need to treat as a map for some
reason, it is useful to know that `Object.keys` returns only an
object's _own_ keys, not those in the prototype. As an alternative to
the `in` operator, you can use the `hasOwnProperty` method, which
ignores the object's prototype.

```
console.log({x: 1}.hasOwnProperty("x"));
// → true
console.log({x: 1}.hasOwnProperty("toString"));
// → false
```

## Polymorphism

{{index "toString method", "String function", polymorphism, overriding, "object-oriented programming"}}

When you call the `String` function (which converts a value to a
string) on an object, it will call the `toString` method on that
object to try to create a meaningful string from it. I mentioned that
some of the standard prototypes define their own version of `toString`
so they can create a string that contains more useful information than
`"[object Object]"`. You can also do that yourself.

```{includeCode: "top_lines: 3"}
Rabbit.prototype.toString = function() {
  return `a ${this.type} rabbit`;
};

console.log(String(blackRabbit));
// → a black rabbit
```

{{index "object-oriented programming", [interface, object]}}

This is a simple instance of a powerful idea. When a piece of code is
written to work with objects that have a certain interface—in this
case, a `toString` method—any kind of object that happens to support
this interface can be plugged into the code, and it will just work.

This technique is called _polymorphism_. Polymorphic code can work
with values of different shapes, as long as they support the interface
it expects.

{{index "for/of loop", "iterator interface"}}

I mentioned in [Chapter ?](data#for_of_loop) that a `for`/`of` loop
can loop over several kinds of data structures. This is another case
of polymorphism—such loops expect the data structure to expose a
specific interface, which arrays and strings do. And we can also add
this interface to your own objects! But before we can do that, we need
to know what symbols are.

## Symbols

It is possible for multiple interfaces to use the same property name
for different things. For example, I could define an interface in which
the `toString` method is supposed to convert the object into a piece
of yarn. It would not be possible for an object to conform to both
that interface and the standard use of `toString`.

That would be a bad idea, and this problem isn't that common. Most
JavaScript programmers simply don't think about it. But the language
designers, whose _job_ it is to think about this stuff, have provided
us with a solution anyway.

{{index "Symbol function", [property, naming]}}

When I claimed that property names are strings, that wasn't entirely
accurate. They usually are, but they can also be _((symbol))s_.
Symbols are values created with the `Symbol` function. Unlike strings,
newly created symbols are unique—you cannot create the same symbol
twice.

```
let sym = Symbol("name");
console.log(sym == Symbol("name"));
// → false
Rabbit.prototype[sym] = 55;
console.log(blackRabbit[sym]);
// → 55
```

The string you pass to `Symbol` is included when you convert it to a
string and can make it easier to recognize a symbol when, for
example, showing it in the console. But it has no meaning beyond
that—multiple symbols may have the same name.

Being both unique and usable as property names makes symbols suitable
for defining interfaces that can peacefully live alongside other
properties, no matter what their names are.

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

It is possible to include symbol properties in object expressions and
classes by using ((square bracket))s around the property name.
That causes the property name to be evaluated, much like the square
bracket property access notation, which allows us to refer to a
binding that holds the symbol.

```
let stringObject = {
  [toStringSymbol]() { return "a jute rope"; }
};
console.log(stringObject[toStringSymbol]());
// → a jute rope
```

## The iterator interface

{{index "iterable interface", "Symbol.iterator symbol", "for/of loop"}}

The object given to a `for`/`of` loop is expected to be _iterable_.
This means it has a method named with the `Symbol.iterator`
symbol (a symbol value defined by the language, stored as a property
of the `Symbol` function).

{{index "iterator interface", "next method"}}

When called, that method should return an object that provides a
second interface, _iterator_. This is the actual thing that iterates.
It has a `next` method that returns the next result. That result
should be an object with a `value` property that provides the next value,
if there is one, and a `done` property, which should be true when there
are no more results and false otherwise.

Note that the `next`, `value`, and `done` property names are plain
strings, not symbols. Only `Symbol.iterator`, which is likely to be
added to a _lot_ of different objects, is an actual symbol.

We can directly use this interface ourselves.

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

Let's implement an iterable data structure. We'll build a _matrix_
class, acting as a two-dimensional array.

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

The class stores its content in a single array of _width_ × _height_
elements. The elements are stored row by row, so, for example, the third
element in the fifth row is (using zero-based indexing) stored at
position 4 × _width_ + 2.

The constructor function takes a width, a height, and an optional
`element` function that will be used to fill in the initial values.
There are `get` and `set` methods to retrieve and update elements in
the matrix.

When looping over a matrix, you are usually interested in the position
of the elements as well as the elements themselves, so we'll have our
iterator produce objects with `x`, `y`, and `value` properties.

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

The class tracks the progress of iterating over a matrix in its `x`
and `y` properties. The `next` method starts by checking whether the
bottom of the matrix has been reached. If it hasn't, it _first_
creates the object holding the current value and _then_ updates its
position, moving to the next row if necessary.

Let's set up the `Matrix` class to be iterable. Throughout this book,
I'll occasionally use after-the-fact prototype manipulation to add
methods to classes so that the individual pieces of code remain small
and self-contained. In a regular program, where there is no need to
split the code into small pieces, you'd declare these methods directly
in the class instead.

```{includeCode: true}
Matrix.prototype[Symbol.iterator] = function() {
  return new MatrixIterator(this);
};
```

{{index "for/of loop"}}

We can now loop over a matrix with `for`/`of`.

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
