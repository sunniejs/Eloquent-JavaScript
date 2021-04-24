{{meta {load_files: ["code/crow-tech.js", "code/chapter/11_async.js"]}}}

# 异步编程Asynchronous Programming

{{quote {author: "Laozi", title: "Tao Te Ching", chapter: true}

孰能浊以静之徐清？\
孰能安以动之徐生？

quote}}

{{index "Laozi"}}

{{figure {url: "img/chapter_picture_11.jpg", alt: "Picture of two crows on a branch", chapter: framed}}}

一个计算机的核心部分，也就是执行组成我们的程序的每个步骤的部分，被称为 _((处理器))_。我们目前所接触到的程序在它们完成工作之前都会让处理器保持繁忙。对于操纵数字的循环这样的程序，它的执行速度几乎完全取决于处理器的速度。

{{index [memory, speed], [network, speed]}}

但是，很多程序于处理器之外的事物进行交互。比如说，它们可能通过一个计算机网络进行沟通，或者向一个((硬盘))请求数据 —— 这比从内存获取数据要慢得多。

当这样的事情发生之际，让处理器处于闲置的状态是很可惜的 —— 可能它同时还有别的可以做的事情。在某种程度上，这是你的操作系统要处理的事情，它会在多个运行着的程序之间切换处理器。但是当我们只有 _单个_ 程序在等待网络请求并需要有进展的时候，这就帮不到忙了。

## 异步性Asynchronicity

{{index "synchronous programming"}}

在 _同步_ 的编程模型中，事情会依次发生。当你调用一个会执行长时间操作的函数，它只会在操作结束时才会返回结果。在执行操作的这段时间里你的程序将会中断。

{{index "asynchronous programming"}}

一个 _异步_ 的模型则允许多件事情同时发生。当你开启一项操作，你的程序会持续运行。当操作完成时，程序会收到通知并且可以访问结果(例如从硬盘读取的数据)。

我们可以用一个小小的示例来比较同步编程与异步编程：一个从((网络))中获取两个资源并合并其结果的程序。

{{index "synchronous programming"}}

在一个同步的环境中，请求函数只会在它完成工作之后才会返回，执行此任务最简单的方法是一个接一个地发出请求。这样做有一个缺点，即第二个请求只有在第一个请求完成时才会开启。总共花费的时间至少会是两段响应时间之和。

{{index parallelism}}

在同步系统中，解决此问题的方法是开启额外的控制((线程))。一个 _线程_ 是另一个运行着的程序，它的执行可能会由操作系统对其他程序进行交错 —— 由于大多数现代计算机含有多个处理器，多个线程甚至可能同时运行于不同的处理器上。第二个线程可以开启第二个请求，然后两个线程都等待它们的结果返回，在那之后它们重新同步以合并其结果。

{{index CPU, blocking, "asynchronous programming", timeline, "callback function"}}

在下图中，粗线表示程序在正常运行时所花的时间，而细线表示等待网络所花费的时间。在同步模型中，对于一个给定的控制线程来说，网络所花费的时间是时间线的 _一部分_。在异步模型中，启动一个网络操作在概念上会导致时间线出现一个 _分岔_。启动该操作的程序会继续运行，而发生的操作会与其并行，且在操作完成时通知该程序。

{{figure {url: "img/control-io.svg", alt: "Control flow for synchronous and asynchronous programming",width: "8cm"}}}

{{index ["control flow", asynchronous], "asynchronous programming", verbosity}}

另一种阐明两者区别的方式在于，在同步模型中，等待操作完成是 _隐性的_。而在异步模型中，它是 _显性的_、可以被我们控制的。

异步性是一把双刃剑，它使得表达不符合直线控制模型的程序更加简洁，然而它也会让符合直线模型的程序表达起来更加尴尬。我们会在本章稍后的内容中介绍一些应对这种尴尬处境的办法。

两个重要的 JavaScript 编程平台 —— ((浏览器)) 和 ((Node.js)) —— 都能将需要花上一段时间的操作异步化，而不是依赖于((线程))。由于线程编程是出了名的困难(要弄懂一个同时在做很多件事的程序是难上加难)，这通常被认为是一件好事。

## 乌鸦技术

大多数人都明白((乌鸦))是很聪明的鸟这一事实。它们会利用工具、提前计划、记住事情，甚至是彼此之间沟通这些事物。

然而大多数人不知道的是，它们其实还可以做很多事情，只是将其很好地隐藏了起来。一个有名望(或者说有点古怪)的((乌鸦))专家曾经告诉过我，乌鸦的技术并非远远落后于人类，而且它们正在迎头赶上我们。

比方说，很多乌鸦文化拥有构建计算设备的能力。这些不像人类的电子计算设备，而是通过一种微型昆虫的动作进行操作，这类昆虫在物种上与((白蚁))很接近，它们与乌鸦发展出了((共生关系))。乌鸦给它们提供食物，作为回报，昆虫们则建立起它们复杂的殖民地，并借助居住其中的活物的帮助来执行计算。

这样的殖民地通常位于庞大的、长存的鸟巢中。乌鸦和昆虫同心协力建起了一个以鳞茎状的粘土为结构的网络，隐匿于鸟巢的细枝之间，昆虫就在这地方生活和工作。

为了与其他设备进行通信，这些机器使用光信号。乌鸦把部分反光材料嵌入了专用的通信杆中，而昆虫则瞄准这些通信杆来把光反射至另一个鸟巢，并以快速闪烁的序列为数据编码。这意味着只有在视线没有被挡住的鸟巢之间才可以进行通信。

我们的朋友乌鸦专家已经将 ((Hières-sur-Amby)) 这个村子里的乌鸦巢的网络给映射了出来，就在 Rhône 河的岸边。这个映射展现了这些鸟巢和它们彼此之间的连结：

{{figure {url: "img/Hieres-sur-Amby.png", alt: "A network of crow nests in a small village"}}}

作为一个惊人的((趋同进化))的示例，乌鸦计算机使用 JavaScript 来运行。在本章中我们会为它们编写一些基本的联网功能。

## 回调

{{indexsee [function, callback], "callback function"}}

((异步编程))的一个做法是让执行一个缓慢操作的函数接受一个额外的参数，这个参数也就是 _((回调函数))_。当启动的操作完成之时，回调函数就会与结果一起被调用。

{{index "setTimeout function", waiting}}

举例来说，在 Node.js 以及浏览器中都可以用的 `setTimeout` 函数，会先等待给定的毫秒数(一秒是一千毫秒)，然后调用一个函数。

```{test: no}
setTimeout(() => console.log("Tick"), 500);
```

通常来说，等待并不是很重要的工作，但是有时候却很有用，比如说在更新动画的时候，或是查看某件事是否超过了给定的((时间))。

使用回调来连续执行多个异步操作意味着你必须不断传递新的函数，以应对每一个操作之后((连续))的计算。

{{index "hard disk"}}

大多数鸦巢计算机拥有长期的数据存储鳞茎，信息碎片被啄刻在细枝上，以便它们之后被检索。啄刻，或者说寻找一段数据需要一些时间，所以长期存储的接口是异步的，而且使用回调函数。

存储鳞茎藏有 ((JSON)) 片段 —— 以名称为主的可编码的数据。一只((乌鸦))可能会将它贮藏食物的地点信息以 `"food caches"` 的名称给存起来，这个名称会存放一个包含指向其他数据段的地名的数组，这些地名用于描述真实的仓库。一只乌鸦要想在 _Big Oak(译者注：大橡树)_ 鸟巢的存储鳞茎中寻找一个粮((仓))的话，它可以运行如下代码：

{{index "readStorage function"}}

```{includeCode: "top_lines: 1"}
import {bigOak} from "./crow-tech";

bigOak.readStorage("food caches", caches => {
  let firstCache = caches[0];
  bigOak.readStorage(firstCache, info => {
    console.log(info);
  });
});
```

(所有的绑定名和字符串已经从乌鸦语被翻译成英语了。)

这种编程风格是行得通的，然而缩进的层级会随着每一次异步操作而增加，因为你终将处于另一个函数中。如果做更为复杂的事情，比如同时运行多项操作，可能会变得有些尴尬。

鸦巢计算机是为了以((请求))-((响应))模式来进行通信而建造的。这意味着一个鸟巢对另一个鸟巢发送一条讯息，另一个鸟巢立即发回一条确认收到、有可能还包含对之前讯息中的问题的回复的讯息。

每条讯息都被标上了一个 _type(译者注：类型)_，以决定该讯息是如何被处理的。我们的代码可以定义特殊的请求类型的处理器，当这个类型的请求出现时，相应的处理器会被调用，从而生成一个响应。

{{index "crow-tech module", "send method"}}

`"./crow-tech"` 模块导出的接口提供了基于回调的函数来进行通信。鸟巢拥有一个可以发送请求的 `send` 方法。该方法预期一个目标鸟巢的名称、请求的类型，以及请求的内容作为它的前三个参数，还预期一个在收到响应时就调用的函数作为其第四个(也是最后一个)参数。

```
bigOak.send("Cow Pasture", "note", "Let's caw loudly at 7PM",
            () => console.log("Note delivered."));
```

然而，要使得鸟巢可以接收这个请求，我们必须首先定义一个名为 `"note"` 的((请求类型))。处理这个请求的代码必须不仅仅在这个鸦巢机算计上运行，而是在所有接收到此类讯息的鸟巢上运行。我们将假定一只乌鸦飞过了所有的鸟巢，并将我们的处理器代码进行了安装。

{{index "defineRequestType function"}}

```{includeCode: true}
import {defineRequestType} from "./crow-tech";

defineRequestType("note", (nest, content, source, done) => {
  console.log(`${nest.name} received note: ${content}`);
  done();
});
```

`defineRequestType` 函数定义了新的请求类型。以上示例为 `"note"` 请求增添了支持，该请求仅向一个给定的鸟巢发送一段笔记。我们的代码调用了 `console.log` 以便确认请求是否到达。鸟巢有一个含有它们名称的 `name` 属性。

{{index "asynchronous programming"}}

传递给处理器的第四个参数 `done` 是一个回调函数，处理器在处理完请求之后必须调用该函数。如果我们将处理器的((返回值))作为响应的值，那意味着一个请求处理器本身无法执行异步操作。一个做异步操作的函数通常在工作结束之前就返回，且已安排了一个在结束时就调用的回调函数。因此，我们需要一些异步的机制 —— 在这个情况下，再来一个((回调函数)) —— 以在响应就绪的时候发出信号。

从某种程度上来说，异步性是 _可传染的_。对于一个以异步方式工作的函数，任何一个调用它的函数自身也必须是异步的，且使用一个回调或类似的机制来呈现其结果。与只是简单地返回一个值相比，调用一个回调要更为复杂，也更容易出错。因此，需要以这种方式构建你大部分的程序并不是一个好方法。

## Promise

在与抽象的概念打交道的时候，当这些概念可以被((值))所表示的话，通常会变得更为简单。在异步操作的情况里，与其说安排一个在未来某个时间点可以被调用的函数，你可以返回一个代表这种未来事件的对象。

{{index "Promise class", "asynchronous programming"}}

这正是标准类 `Promise` 的用途。一个 _promise(译者注：承诺)_ 是一个可能在未来的某个时间点完成并返回一个值的异步操作。它有能力在其值可用时通知所有对此感兴趣的程序。

{{index "Promise.resolve function", "resolving (a promise)"}}

想要创建一个 promise，最简单的办法是调用 `Promise.resolve`。这个函数会确保你给它的值被包装在一个 promise 中。如果该值已经是一个 promise，那么它会被直接返回 —— 否则，你将会得到一个立即完成的新 promise，它的结果即是你的值。

```
let fifteen = Promise.resolve(15);
fifteen.then(value => console.log(`Got ${value}`));
// → Got 15    获取 15
```

{{index "then method"}}

你可以使用 promise 的 `then` 方法来获取其结果。在 promise 被兑现并生成一个值的时候，由该方法注册的((回调函数))将会被调用。你可以向单个 promise 添加多个回调，即使你是在该 promise _resolved_ (已兑现)之后才添加的回调，它们也仍会被调用。

但那并非 `then` 方法的全部所作所为，它还返回另外一个 promise。这个 promise 会兑现成处理器函数所返回的值，如果处理器函数返回的是一个 promise，那么它将等待这个 promise 并将其结果给兑现。

我们不妨将 promise 看成是一个可以将值带入异步现实的设备。一个普通的值是存在的，一个 promise 值则 _可能_ 已经存在，或者可能会在未来的某一时间点才出现。基于 promise 而定义的计算会在此类被包装的值上运作，并且在其值可用的时候被异步执行。

{{index "Promise class"}}

你可以把 `Promise` 作为一个构造器来创建 promise。它的接口有些古怪 —— 该构造器预期接受一个会立即调用的函数作为参数，这个函数被用来兑现 promise。这个构造器以这种方式工作，而不像 `resolve` 方法那般。因此，只有创建该 promise 的代码才可以兑现它。

{{index "storage function"}}

你应该这样为 `readStorage` 函数创建一个基于 promise 的接口：

```{includeCode: "top_lines: 5"}
function storage(nest, name) {
  return new Promise(resolve => {
    nest.readStorage(name, result => resolve(result));
  });
}

storage(bigOak, "enemies")
  .then(value => console.log("Got", value));
```

这个异步函数会返回一个有意义的值。这是 promise 的一个主要优势 —— 它们简化了异步函数的使用方法。与其必须通过回调来传递，基于承诺的函数与正常函数看起来很像：它们接受输入作为参数，并且返回其输出。唯一的区别在于其输出在当下或许还不可用。

## 故障

{{index "exception handling"}}

正常的 JavaScript 计算会在出故障时会抛出异常，异步计算通常需要类似的事物。一个网络请求可能会故障，或者部分属于异步计算的代码可能会抛出异常。

{{index "callback function", error}}

具有回调风格的异步编程最严重的问题之一在于，它极难确保故障会被妥当地报告给回调。

一个被普遍采用的传统做法是，回调的第一个参数被用于表明操作故障，而第二个参数会包含在操作成功的情况下生成的值。这样的回调函数必须总是检查它们是否收到了异常，以及确保由它们引发的问题(包含被它们调用的函数抛出的异常)被捕获，并传递给正确的函数。

{{index "rejecting (a promise)", "resolving (a promise)", "then method"}}

Promises 使事情变得更简单了。它们既可能兑现(操作成功完成)，也可能会拒绝(操作故障)。兑现处理器(以 `then` 注册)只有在操作成功的情况下才会被调用，而拒绝会被自动传播到被 `then` 返回的新的 promise 中。而且，当一个处理器抛出异常的时候，这将自动导致它拒绝 `then` 的调用所产生的 promise。因此，如果在一连串异步操作中的任何一个元素出故障了，整串操作的结果将被标记为已拒绝，并且在出故障的点之后不会有兑现处理器被调用。

{{index "Promise.reject function", "Promise class"}}

就像兑现 promise 会提供一个值那样，当 promise 拒绝时也会提供一个值，它通常被称为拒绝的 _理由_。当一个处理器函数中的异常导致了拒绝，那么该异常的值就被用作理由。与之相似的是，当一个处理器返回一个拒绝的 promise，那个拒绝会流向下一个 promise。`Promise.reject` 函数会创建一个新的、立即拒绝的 promise。

{{index "catch method"}}

为了特别处理这种拒绝的情况，promises 有一个 `catch` 方法。该方法会注册一个在 promise 拒绝时被调用的处理器，与 `then` 处理器对正常的兑现情况的处理方式类似。这个方法与 `then` 另外一个非常相似的地方在于，它返回一个新的 promise，在正常完成的情况下兑现为原本 promise 的值，否则会兑现为 `catch` 处理器的结果。如果 `catch` 处理器抛出一个错误，那么这个新的 promise 也会拒绝。

{{index "then method"}}

为了简写方便，`then` 也接受一个拒绝处理器作为其第二个参数，所以你可以在一个方法调用中将两种处理器都进行安装。

除了兑现函数之外，传递给 `Promise` 构造器的函数可接受第二个参数，该参数被用来处理新的 promise 拒绝时的情况。

通过对 `then` and `catch` 的调用而生成的 promise 链值可以被视为异步值或故障通过的管道。由于这样的链是由对处理器的注册而创建的，每一个链接都有一个与之关联的成功处理器或一个拒绝处理器(或两者都有)。与结果类型(成功或故障)不匹配的处理器将会被忽略。但是那些匹配的处理器将被调用，而它们的结果决定哪种值会出现 —— 返回非 promise 值的时候是成功，当它抛出异常的时候则是拒绝，在该值被返回的时候还伴有 promise 的结果。

```{test: no}
new Promise((_, reject) => reject(new Error("Fail")))
  .then(value => console.log("Handler 1"))
  .catch(reason => {
    console.log("Caught failure " + reason);
    return "nothing";
  })
  .then(value => console.log("Handler 2", value));
// → Caught failure Error: Fail   捕获故障错误：故障
// → Handler 2 nothing            第二个处理器  空
```

{{index "uncaught exception", "exception handling"}}

就像未被捕获的异常会被环境给处理掉那样，当一个 promise 的拒绝没有被处理时，JavaScript 环境会检测到这一点，并将其报告为一个错误。

## 网络可难了

{{index [network, reliability]}}

有的时候，((乌鸦))用于传输信号的反射系统会光线不足，抑或是信号路径被遮挡住了。一个信号在被发送之后可能永远也不会被接收。

{{index "send method", error, timeout}}

事实上，这将会导致传递给 `send` 的回调永远也不会被调用，从而可能会导致程序在意识到出现问题之前就停止运行。如果一个请求在一段给定的时间内没有收到响应，它可以 _超时_ 并报告故障的话，那将是挺好的。

通常来说，传输故障是随机的事故，比如汽车大灯干扰光信号，只需重试请求就可以致使请求成功。所以，我们使得请求函数在放弃之前自动重发几次请求。

{{index "Promise class", "callback function", [interface, object]}}

另外，由于我们已经确立了 promise 是个好东西，我们也来让请求函数返回一个 promise。就它们所能表达的东西而言，回调和 promise 是相同的。基于回调的函数可以被包装成公开基于 promise 的接口，反之亦然。

即使一个((请求))和它的((响应))都传递无误，其响应可能会指示故障 —— 比方说，如果该请求试图使用一个未被定义的请求类型，或者处理器抛出一个异常的情况。为了应对这样的情况，`send` 和 `defineRequestType` 会遵循之前提到的传统，即如果有故障的原因的话，传递给回调的第一个参数是故障原因，而第二个参数是实际的结果。

这些可以被我们的包装器转译成 promise 的兑现和拒绝。

{{index "Timeout class", "request function", retry}}

```{includeCode: true}
class Timeout extends Error {}

function request(nest, target, type, content) {
  return new Promise((resolve, reject) => {
    let done = false;
    function attempt(n) {
      nest.send(target, type, content, (failed, value) => {
        done = true;
        if (failed) reject(failed);
        else resolve(value);
      });
      setTimeout(() => {
        if (done) return;
        else if (n < 3) attempt(n + 1);
        else reject(new Timeout("Timed out"));
      }, 250);
    }
    attempt(1);
  });
}
```

{{index "Promise class", "resolving (a promise)", "rejecting (a promise)"}}

由于 promises 只能被兑现(或拒绝)一次，这将会行得通。在 `resolve` 或 `reject` 第一次被调用时，该 promise 的结果就会被决定，而在另一个请求完成之后返回的请求引起的进一步调用将被忽略。

{{index recursion}}

为了构建一个用于重试的异步((循环))，我们需要用一个递归函数 —— 一个普通的循环不会允许我们停下来等待一个异步操作。`attempt` 函数尝试发送请求一次。它也设定了一个 timeout，如果超过 250 毫秒还没有响应返回的话，就再次尝试，倘若这已经是第三次尝试的话，则用一个 `Timeout` 的实例作为原因来拒绝该 promise。

{{index idempotence}}

每四分之一秒重试一次、且在超过四分之三秒没有收到响应便放弃 —— 这确实多少有些随意。甚至还有这样一种可能：如果请求送到了，但处理器要花多一点的时间的话，请求可能会被多次传递。我们写处理器的时候会将这个问题纳入考虑 —— 重复的消息应是无害的。

总而言之，我们今天并不会构建一个世界级的、强大的网络。然而这没有关系 —— 在计算方面乌鸦并没有报以很高的期望。

{{index "defineRequestType function", "requestType function"}}

为了让我们彻底摆脱回调，我们将更进一步，为 `defineRequestType` 定义一个包装器。这个包装器允许处理器函数返回一个 promise 或者纯值，并且为我们将其连接到回调上。

```{includeCode: true}
function requestType(name, handler) {
  defineRequestType(name, (nest, content, source,
                           callback) => {
    try {
      Promise.resolve(handler(nest, content, source))
        .then(response => callback(null, response),
              failure => callback(failure));
    } catch (exception) {
      callback(exception);
    }
  });
}
```

{{index "Promise.resolve function"}}

`Promise.resolve` 用来将 `handler` 返回的值转化成一个 promise(如果它还不是 promise 的话)。

{{index "try keyword", "callback function"}}

要注意对 `handler` 的调用必须被包装在一个 `try` 块中，以确保任何由它直接引发的异常会被传递给回调。这很好地说明了使用原生回调妥当处理错误的难处 —— 很容易忘记对异常进行这样妥当的路由，而你要是不这么做的话，故障并不会被报告至正确的回调中。Promises 将这一步很大程度上给自动化了，因此更不容易出错。

## Promises 的集合

{{index "neighbors property", "ping request"}}

每一台巢机都把含有传输距离内的其他鸟巢的一个数组存在 `neighbors` 属性中。为了查验哪些鸟巢当下可以访问，你可以写一个函数，该函数尝试对每一个距离内的鸟巢发送一个 `"ping"` 请求(一个单单寻求响应的请求)，看看哪一个鸟巢会发来响应。

{{index "Promise.all function"}}

当处理同时运行的 promise 集合的时候，`Promise.all` 函数会有帮助。它返回一个 promise，这个 promise 会等待数组中所有的 promise 都兑现之后，兑现成这些 promise 所产生的值的一个数组(与原数组顺序相同)。如果任何的 promise 被拒绝，那么 `Promise.all` 的结果本身也会被拒绝。

```{includeCode: true}
requestType("ping", () => "pong");

function availableNeighbors(nest) {
  let requests = nest.neighbors.map(neighbor => {
    return request(nest, neighbor, "ping")
      .then(() => true, () => false);
  });
  return Promise.all(requests).then(result => {
    return nest.neighbors.filter((_, i) => result[i]);
  });
}
```

{{index "then method"}}

当一个邻居鸟巢不可访问时，我们并不想让整个组合起来的 promise 出故障，因为那样的话我们什么信息也不会知道。因此，映射到邻居集合并把它们转化为请求 promises 的函数会附有处理器，该处理器使成功的请求生成 `true`， 被拒绝的请求则生成 `false`。

{{index "filter method", "map method", "some method"}}

在组合之后的 promise 的处理器中，`filter` 用于将 `neighbors` 数组中相应值为 false 的元素剔除。这利用了 `filter` 传递给其过滤函数的第二个参数是当下元素的数组索引这个事实(`map`、`some` 和相似的高阶数组方法都是如此)。

## 网络泛洪

只有相邻鸟巢之间可以沟通的这个事实阻碍了这个网络的实用性。

为了向整个网络广播信息，一个解决方法是设置一种自动转发给其他邻居的请求种类。然后这些邻居会依次转发给它们的邻居，直到整个网络都收到讯息。

{{index "sendGossip function"}}

```{includeCode: true}
import {everywhere} from "./crow-tech";

everywhere(nest => {
  nest.state.gossip = [];
});

function sendGossip(nest, message, exceptFor = null) {
  nest.state.gossip.push(message);
  for (let neighbor of nest.neighbors) {
    if (neighbor == exceptFor) continue;
    request(nest, neighbor, "gossip", message);
  }
}

requestType("gossip", (nest, message, source) => {
  if (nest.state.gossip.includes(message)) return;
  console.log(`${nest.name} received gossip '${
               message}' from ${source}`);
  sendGossip(nest, message, source);
});
```

{{index "everywhere function", "gossip property"}}

为了避免永远在网络上发送同样的讯息，每一个鸟巢保留一个已经见过的流言字符串的数组。为了定义这个数组，我们使用 `everywhere` 函数 —— 它会在每个鸟巢上运行代码 —— 来给鸟巢的 `state` 对象增加一个属性，该属性用于保存鸟巢的本地状态。

当一个鸟巢接收到一条重复的流言讯息时(很可能是由于所有人都在盲目地重发)，它会将其忽略。但是当它收到一条新的讯息时，它会兴奋地告知它(除了发送这条讯息的鸟巢之外)的所有邻居。

这将会使得新的流言段子在网络中像落入清水中的一滴墨那样扩散开来。即使有时候有的连接当前不可用，如果存在一条通往给定鸟巢的替代线路，那么该流言就会通过这条线路传递过去。

{{index "flooding"}}

这种风格的网络通信被称为 _泛洪_ —— 它像洪水一样用一段信息冲洗网络，直到所有的节点都接收到为止。

{{if interactive

我们可以调用 `sendGossip` 来查看流经整个村庄的讯息。

```
sendGossip(bigOak, "Kids with airgun in the park");
```

if}}

## 讯息路由

{{index efficiency}}

如果一个特定的节点只想要与另一个节点进行沟通，洪发并不是一个很有效率的方式。尤其是当网络很庞大的时候，这会导致大量无用的数据传输。

{{index "routing"}}

一个替代方法是为讯息设置一种从节点对节点进行跳跃的方式，直到它们到达目的地为止。此方法的难点在于，它要求对网络的布局有所了解。为了将一个请求朝一个遥远的鸟巢的方向发送过去，了解发送至哪个邻近鸟巢可以更快到达目的地是必要的。朝错误的方向发送并没有多少用处。

由于每个鸟巢只知道与其直接相邻的鸟巢，它并没有计算路径所需要的信息。我们必须想办法将关于这些连接的信息传播到所有鸟巢，最好能允许信息随时间的推移而改变，比如说旧的鸟巢被遗弃或新鸟巢被建造起来。

{{index flooding}}

我们可以再次使用泛洪，但是与其说检查一条讯息是否已经被接收，我们现在要检查是否一个给定鸟巢新的邻居集合与其当前的邻居集合相匹配。

{{index "broadcastConnections function", "connections binding"}}

```{includeCode: true}
requestType("connections", (nest, {name, neighbors},
                            source) => {
  let connections = nest.state.connections;
  if (JSON.stringify(connections.get(name)) ==
      JSON.stringify(neighbors)) return;
  connections.set(name, neighbors);
  broadcastConnections(nest, name, source);
});

function broadcastConnections(nest, name, exceptFor = null) {
  for (let neighbor of nest.neighbors) {
    if (neighbor == exceptFor) continue;
    request(nest, neighbor, "connections", {
      name,
      neighbors: nest.state.connections.get(name)
    });
  }
}

everywhere(nest => {
  nest.state.connections = new Map;
  nest.state.connections.set(nest.name, nest.neighbors);
  broadcastConnections(nest, nest.name);
});
```

{{index JSON, "== operator"}}

我们使用 `JSON.stringify` 来进行比较，因为将 `==` 用于对象或数组的话，只有在两者的值完全相同时才会返回 true，而这并不是我们当下所需要的。通过比较 JSON 字符串来对其内容进行比较是一个原始但是有效的方法。

这些节点会立即开始广播它们的连接，这将会很快给予每一个鸟巢一个当前网络((图))的映射(除非某些鸟巢完全无法访问)。

{{index pathfinding}}

你可以对图做的一件事情是从它们之间寻找路径，像我们在 [第七章](robot) 中所看到的那样。如果我们有指向一条讯息目的地的路径，我们便知道该往哪个方向发送该讯息。

{{index "findRoute function"}}

这个 `findRoute` 函数与 [第七章](robot#findRoute) 中的 `findRoute` 很相像，它搜寻一条到达网络中的给定节点的路径。但是与其说返回整个路径，它只返回下一步而已。下一个鸟巢本身会利用它对网络当下的信息，从而决定 _它_ 朝哪里发送讯息。

```{includeCode: true}
function findRoute(from, to, connections) {
  let work = [{at: from, via: null}];
  for (let i = 0; i < work.length; i++) {
    let {at, via} = work[i];
    for (let next of connections.get(at) || []) {
      if (next == to) return via;
      if (!work.some(w => w.at == next)) {
        work.push({at: next, via: via || next});
      }
    }
  }
  return null;
}
```

现在我们可以构建一个可以发送长距离讯息的函数。如果讯息将被传达到一个直接邻居，那么它会被正常传达。如若不然，它将会被打包成一个对象发送至一个与目标更近的邻居那里，并使用 `"route"` 作为请求类型，使得那个邻居重复同样的行动。

{{index "routeRequest function"}}

```{includeCode: true}
function routeRequest(nest, target, type, content) {
  if (nest.neighbors.includes(target)) {
    return request(nest, target, type, content);
  } else {
    let via = findRoute(nest.name, target,
                        nest.state.connections);
    if (!via) throw new Error(`No route to ${target}`);
    return request(nest, via, "route",
                   {target, type, content});
  }
}

requestType("route", (nest, {target, type, content}) => {
  return routeRequest(nest, target, type, content);
});
```

{{if interactive

我们现在可以向教堂钟楼上的鸟巢发送一条讯息，也就是去掉四个网络跃点。

```
routeRequest(bigOak, "Church Tower", "note",
             "Incoming jackdaws!");
```

if}}

{{index [network, abstraction], layering}}

我们在原始的通讯系统的基础上构建了数层功能，为了让它便于使用。这是一个描述真实计算机网络如何工作的、不错的(虽然是简化之后的)模型。

{{index error}}

计算机网络的一个显著特征在于，它们并不可靠 —— 建立在它们的基础之上的抽象层可以提供帮助，但是你不能将网络故障给抽象化。因此，网络编程通常有很大一部分是关于预判和处理故障。

## Async 函数

为了储存重要信息，((乌鸦))在鸟巢中复制信息的做法是出了名的。这样一来，当一只鹰摧毁了一个鸟巢时，其中的信息并不会丢失。

一台巢机为了获取它自己存储鳞茎中没有的一段信息时，它可能会随机咨询网络中其他的鸟巢，直到找到拥有这段信息的鸟巢为止。

{{index "findInStorage function", "network function"}}

```{includeCode: true}
requestType("storage", (nest, name) => storage(nest, name));

function findInStorage(nest, name) {
  return storage(nest, name).then(found => {
    if (found != null) return found;
    else return findInRemoteStorage(nest, name);
  });
}

function network(nest) {
  return Array.from(nest.state.connections.keys());
}

function findInRemoteStorage(nest, name) {
  let sources = network(nest).filter(n => n != nest.name);
  function next() {
    if (sources.length == 0) {
      return Promise.reject(new Error("Not found"));
    } else {
      let source = sources[Math.floor(Math.random() *
                                      sources.length)];
      sources = sources.filter(n => n != source);
      return routeRequest(nest, source, "storage", name)
        .then(value => value != null ? value : next(),
              next);
    }
  }
  return next();
}
```

{{index "Map class", "Object.keys function", "Array.from function"}}

由于 `connections` 是一个 `Map`，`Object.keys` 对其产生不了作用。它有一个 `keys` _方法_，然而那只会返回一个迭代器，而不是一个数组。一个迭代器(或可迭代的值)可以被 `Array.from` 函数转化为一个数组。

{{index "Promise class", recursion}}

即使用了 promise，这也是多少有些尴尬的代码。多个异步操作以一种不明显的方式被串联了起来。我们再次需要一个递归函数(`next`)来对鸟巢的循环进行建模。

{{index "synchronous programming", "asynchronous programming"}}

而且，这代码实际上做的事情完全是线性的 —— 它总会等先前的操作完成之后才开启下一项操作。这在同步编程模型中表达起来会更加容易。

{{index "async function", "await keyword"}}

好消息是 JavaScript 允许你写用于描述异步运算的伪同步代码。一个 `async` 函数是一个隐性返回一个 promise 的函数，而且它可以在其主体中以一种 _看似_ 同步的方式 `await`(译者注：期盼) 其他 promise。  

{{index "findInStorage function"}}

我们可以这样重写 `findInStorage`：

```
async function findInStorage(nest, name) {
  let local = await storage(nest, name);
  if (local != null) return local;

  let sources = network(nest).filter(n => n != nest.name);
  while (sources.length > 0) {
    let source = sources[Math.floor(Math.random() *
                                    sources.length)];
    sources = sources.filter(n => n != source);
    try {
      let found = await routeRequest(nest, source, "storage",
                                     name);
      if (found != null) return found;
    } catch (_) {}
  }
  throw new Error("Not found");
}
```

{{index "async function", "return keyword", "exception handling"}}

一个 `async` 函数由 `function` 关键词前面的 `async` 单词所标记。在方法前面加上 `async` 也会使得它们变得 `async`。当一个这样的函数或方法被调用的时候，它会返回一个 promise。其主体一旦有返回什么东西，该 promise 会立即兑现。如果它抛出了异常的话，则该 promise 被拒绝。

{{if interactive

```{startCode: true}
findInStorage(bigOak, "events on 2017-12-21")
  .then(console.log);
```

if}}

{{index "await keyword", ["control flow", asynchronous]}}

在一个 `async` 函数中，单词 `await` 可以被放置于一个表达式的前面，以等待一个 promise 兑现，并且仅在那之后继续执行该函数。

与普通的 JavaScript 函数不同，这样的函数不再从开启到结束一气呵成。相反，它在任何具有 `await` 的时间点都可以被 _冻结_，并且可以在之后的时间点恢复。

对于并不简易的异步代码来说，这个写法通常比直接使用 promise 更加方便。即使你需要做一些并不符合同步模型的事情，比如说同时执行多项操作，将 `await` 与 promise 的直接使用相结合也是容易的。

## 生成器

{{index "async function"}}

函数可以被暂停而后再次恢复的能力并不是 `async` 函数所独有的。JavaScript 函数还有一个名为 _((generator))(生成器)_ 函数的特性。两者类似，但生成器没有用到 promise。

当你用 `function*`(在单词 `function` 后面放一个星号) 定义一个函数的时候，它会成为生成器。当你调用一个生成器时，它返回一个我们在[第六章](object)已经见过的((迭代器))。

```
function* powers(n) {
  for (let current = n;; current *= n) {
    yield current;
  }
}

for (let power of powers(3)) {
  if (power > 50) break;
  console.log(power);
}
// → 3
// → 9
// → 27
```

{{index "next method", "yield keyword"}}

当你一开始调用 `powers` 的时候，函数会在其起点被冻结。你每次调用迭代器的 `next` 的时候，该函数会一直运行，直到碰上 `yield`(译者注：产出) 表达式，该表达式会暂停它，并且导致产出的值成为下一个迭代器所生成的值。当函数返回的时候(示例中的函数永远不会返回)，迭代器就结束了。

当你使用生成器函数的时候，编写迭代器通常会容易许多。用于 `Group` 类(来自[第六章](object#group_iterator)的练习题)的迭代器可以用这个生成器来编写：

{{index "Group class"}}

```
Group.prototype[Symbol.iterator] = function*() {
  for (let i = 0; i < this.members.length; i++) {
    yield this.members[i];
  }
};
```

```{hidden: true, includeCode: true}
class Group {
  constructor() { this.members = []; }
  add(m) { this.members.add(m); }
}
```

{{index [state, in iterator]}}

我们不再需要去创建一个保留迭代状态的对象 —— 生成器在每一次产出的时候会自动保存它们的本地状态。

这样的 `yield` 表达式可能只会直接在生成器函数之中出现，而不会出现在你所定义的一个内部函数里。当产出时，生成器所保存的状态只会是它的 _本地_ 环境以及它产出的位置。

{{index "await keyword"}}

一个 `async` 函数是一种特殊类型的生成器。它被调用时生成一个 promise，这个 promise 会在它返回(完成)时被兑现、在抛出异常时被拒绝。无论什么时候它产出(期盼)一个 promise，那个 promise 的结果(值或者是抛出的异常)就会是 `await` 表达式的结果。

## 事件循环The event loop

{{index "asynchronous programming", scheduling, "event loop", timeline}}

异步程序是逐段被执行的。每一段可能开启某些操作，且计划在这些操作完成或出故障时执行某些代码。在这些段之间，程序处于空闲状态，等待下一个操作。

{{index "setTimeout function"}}

所以说回调并不是被计划它们的代码直接调用的。如果我从一个函数里调用 `setTimeout` So callbacks are not directly called by the code that scheduled them.
If I call `setTimeout` from within a function, that function will have
returned by the time the callback function is called. And when the
callback returns, control does not go back to the function that
scheduled it.

{{index "Promise class", "catch keyword", "exception handling"}}

异步行为在它自己的空函数中发生(())Asynchronous behavior happens on its own empty function ((call
stack)). This is one of the reasons that, without promises, managing
exceptions across asynchronous code is hard. Since each callback
starts with a mostly empty stack, your `catch` handlers won't be on
the stack when they throw an exception.

```
try {
  setTimeout(() => {
    throw new Error("Woosh");
  }, 20);
} catch (_) {
  // This will not run
  console.log("Caught!");
}
```

{{index thread, queue}}

No matter how closely together events—such as timeouts or incoming
requests—happen, a JavaScript environment will run only one program at
a time. You can think of this as it running a big loop _around_ your
program, called the _event loop_. When there's nothing to be done,
that loop is stopped. But as events come in, they are added to a queue,
and their code is executed one after the other. Because no two things
run at the same time, slow-running code might delay the handling of
other events.

This example sets a timeout but then dallies until after the
timeout's intended point of time, causing the timeout to be late.

```
let start = Date.now();
setTimeout(() => {
  console.log("Timeout ran at", Date.now() - start);
}, 20);
while (Date.now() < start + 50) {}
console.log("Wasted time until", Date.now() - start);
// → Wasted time until 50
// → Timeout ran at 55
```

{{index "resolving (a promise)", "rejecting (a promise)", "Promise class"}}

Promises 总是作为一个新的事件来兑现或拒绝。即使一个 promise 已经兑现了，等待它将会导致你的回调在当前的脚本结束之后运行，而不是立刻运行。

```
Promise.resolve("Done").then(console.log);
console.log("Me first!");
// → Me first!
// → Done
```

在之后的章节中，我们会见到多种在事件循环上运行的其他类型的事件。

## 异步 bugs Asynchronous bugs

{{index "asynchronous programming", [state, transitions]}}

When your program runs synchronously, in a single go, there are no
state changes happening except those that the program itself
makes. For asynchronous programs this is different—they may have
_gaps_ in their execution during which other code can run.

Let's look at an example. One of the hobbies of our crows is to count
the number of chicks that hatch throughout the village every year.
Nests store this count in their storage bulbs. The following code tries to
enumerate the counts from all the nests for a given year:

{{index "anyStorage function", "chicks function"}}

```{includeCode: true}
function anyStorage(nest, source, name) {
  if (source == nest.name) return storage(nest, name);
  else return routeRequest(nest, source, "storage", name);
}

async function chicks(nest, year) {
  let list = "";
  await Promise.all(network(nest).map(async name => {
    list += `${name}: ${
      await anyStorage(nest, name, `chicks in ${year}`)
    }\n`;
  }));
  return list;
}
```

{{index "async function"}}

The `async name =>` part shows that ((arrow function))s can also be
made `async` by putting the word `async` in front of them.

{{index "Promise.all function"}}

The code doesn't immediately look suspicious...it maps the `async`
arrow function over the set of nests, creating an array of promises,
and then uses `Promise.all` to wait for all of these before returning
the list they build up.

But it is seriously broken. It'll always return only a single line of
output, listing the nest that was slowest to respond.

{{if interactive

```
chicks(bigOak, 2017).then(console.log);
```

if}}

Can you work out why?

{{index "+= operator"}}

The problem lies in the `+=` operator, which takes the _current_ value
of `list` at the time where the statement starts executing and then,
when the `await` finishes, sets the `list` binding to be that value
plus the added string.

{{index "await keyword"}}

But between the time where the statement starts executing and the time
where it finishes there's an asynchronous gap. The `map` expression
runs before anything has been added to the list, so each of the `+=`
operators starts from an empty string and ends up, when its storage
retrieval finishes, setting `list` to a single-line list—the result of
adding its line to the empty string.

{{index "side effect"}}

This could have easily been avoided by returning the lines from the
mapped promises and calling `join` on the result of `Promise.all`,
instead of building up the list by changing a binding. As usual,
computing new values is less error-prone than changing existing
values.

{{index "chicks function"}}

```
async function chicks(nest, year) {
  let lines = network(nest).map(async name => {
    return name + ": " +
      await anyStorage(nest, name, `chicks in ${year}`);
  });
  return (await Promise.all(lines)).join("\n");
}
```

Mistakes like this are easy to make, especially when using `await`,
and you should be aware of where the gaps in your code occur. An
advantage of JavaScript's _explicit_ asynchronicity (whether through
callbacks, promises, or `await`) is that spotting these gaps is
relatively easy.

## 摘要Summary

异步编程使得这样一种表达成为可能：即等待长时间运行的操作时无需在这些操作的过程中成为Asynchronous programming makes it possible to express waiting for
long-running actions without freezing the program during these
actions. JavaScript environments typically implement this style of
programming using callbacks, functions that are called when the
actions complete. An event loop schedules such callbacks to be called
when appropriate, one after the other, so that their execution does
not overlap.

Programming asynchronously is made easier by promises, objects that
represent actions that might complete in the future, and `async`
functions, which allow you to write an asynchronous program as if it
were synchronous.

## 练习题Exercises

### 追踪手术刀Tracking the scalpel

{{index "scalpel (exercise)"}}

The village crows own an old scalpel that they occasionally use on
special missions—say, to cut through screen doors or packaging. To be
able to quickly track it down, every time the scalpel is moved to
another nest, an entry is added to the storage of both the nest that
had it and the nest that took it, under the name `"scalpel"`, with its
new location as the value.

This means that finding the scalpel is a matter of following the
breadcrumb trail of storage entries, until you find a nest where that
points at the nest itself.

{{index "anyStorage function", "async function"}}

Write an `async` function `locateScalpel` that does this, starting at
the nest on which it runs. You can use the `anyStorage` function
defined earlier to access storage in arbitrary nests. The scalpel has
been going around long enough that you may assume that every nest has
a `"scalpel"` entry in its data storage.

Next, write the same function again without using `async` and `await`.

{{index "exception handling"}}

Do request failures properly show up as rejections of the returned
promise in both versions? How?

{{if interactive

```{test: no}
async function locateScalpel(nest) {
  // 在这里输入你的代码
}

function locateScalpel2(nest) {
  // 在这里输入你的代码
}

locateScalpel(bigOak).then(console.log);
// → Butcher Shop
```

if}}

{{hint

{{index "scalpel (exercise)"}}

This can be done with a single loop that searches through the nests,
moving forward to the next when it finds a value that doesn't match
the current nest's name and returning the name when it finds a
matching value. In the `async` function, a regular `for` or `while`
loop can be used.

{{index recursion}}

To do the same in a plain function, you will have to build your loop
using a recursive function. The easiest way to do this is to have that
function return a promise by calling `then` on the promise that
retrieves the storage value. Depending on whether that value matches
the name of the current nest, the handler returns that value or a
further promise created by calling the loop function again.

Don't forget to start the loop by calling the recursive function once
from the main function.

{{index "exception handling"}}

In the `async` function, rejected promises are converted to exceptions
by `await`. When an `async` function throws an exception, its promise
is rejected. So that works.

If you implemented the non-`async` function as outlined earlier, the way
`then` works also automatically causes a failure to end up in the
returned promise. If a request fails, the handler passed to `then`
isn't called, and the promise it returns is rejected with the same
reason.

hint}}

### 建立 Promise.all 

{{index "Promise class", "Promise.all function", "building Promise.all (exercise)"}}

Given an array of ((promise))s, `Promise.all` returns a promise that
waits for all of the promises in the array to finish. It then
succeeds, yielding an array of result values. If a promise
in the array fails, the promise returned by `all` fails too, with the
failure reason from the failing promise.

Implement something like this yourself as a regular function
called `Promise_all`.

Remember that after a promise has succeeded or failed, it can't
succeed or fail again, and further calls to the functions that resolve
it are ignored. This can simplify the way you handle failure of your
promise.

{{if interactive

```{test: no}
function Promise_all(promises) {
  return new Promise((resolve, reject) => {
    // 在这里输入你的代码
  });
}

// 测试代码
Promise_all([]).then(array => {
  console.log("This should be []:", array);
});
function soon(val) {
  return new Promise(resolve => {
    setTimeout(() => resolve(val), Math.random() * 500);
  });
}
Promise_all([soon(1), soon(2), soon(3)]).then(array => {
  console.log("This should be [1, 2, 3]:", array);
});
Promise_all([soon(1), Promise.reject("X"), soon(3)])
  .then(array => {
    console.log("We should not get here");
  })
  .catch(error => {
    if (error != "X") {
      console.log("Unexpected failure:", error);
    }
  });
```

if}}

{{hint

{{index "Promise.all function", "Promise class", "then method", "building Promise.all (exercise)"}}

The function passed to the `Promise` constructor will have to call
`then` on each of the promises in the given array. When one of them
succeeds, two things need to happen. The resulting value needs to be
stored in the correct position of a result array, and we must check
whether this was the last pending ((promise)) and finish our own
promise if it was.

{{index "counter variable"}}

The latter can be done with a counter that is initialized to the
length of the input array and from which we subtract 1 every time a
promise succeeds. When it reaches 0, we are done. Make sure you take
into account the situation where the input array is empty (and thus no
promise will ever resolve).

Handling failure requires some thought but turns out to be extremely
simple. Just pass the `reject` function of the wrapping promise to
each of the promises in the array as a `catch` handler or as a second
argument to `then` so that a failure in one of them triggers the
rejection of the whole wrapper promise.

hint}}
