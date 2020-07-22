{{meta {load_files: ["code/chapter/07_robot.js", "code/animatevillage.js"], zip: html}}}

# Project: A Robot

{{quote {author: "Edsger Dijkstra", title: "The Threats to Computing Science", chapter: true}

[...] 关于机器能否思考的问题 [...] 和潜水艇能否游泳的问题如出一辙。

quote}}

{{index "artificial intelligence", "Dijkstra, Edsger"}}

{{figure {url: "img/chapter_picture_7.jpg", alt: "Picture of a package-delivery robot", chapter: framed}}}

{{index "project chapter", "reading code", "writing code"}}

在“项目”章节中，我不会介绍新的知识点。相反，我们会一起创建一个程序。尽管学习编程需要理论，但阅读理解真正的程序一样重要。

我们本章的目的是创建一个((自动机器))，一个可以在((虚拟世界))执行任务的小程序。我们的自动机器将是一个接送包裹的((机器人))快递员。

## Meadowfield

{{index "roads array"}}

((Meadowfield))这个村庄不大。共有 11 个地方，由 14 条路组成。如下面这个道路数组所示：

```{includeCode: true}
const roads = [
  "Alice 家 - Bob 家",   "Alice 家 - 小屋",
  "Alice 家 - 邮局",   "Bob 家 - 市府",
  "Daria 家 - Ernie 家", "Daria 家 - 市府",
  "Ernie 家 - Grete 家", "Grete 家 - 农场",
  "Grete 家 - 商场",          "集市 - 农场",
  "集市 - 邮局",     "集市 - 商场",
  "集市 - 市府",       "商场 - 市府"
];
```

{{figure {url: "img/village2x.png", alt: "The village of Meadowfield"}}}

这个村庄的道路网形成了一个_((图))_。一个图是一个由线（道路）相连的点（村庄中的地点）组成的集。这个图将是我们的机器人可活动的世界。

{{index "roadGraph object"}}

字符串数组不容易使用。我们感兴趣的是从一个地点能抵达的目的地。所以让我们先把这些道路转换成一个数据结构，以便我们更容易找到所有相连接的地点。

```{includeCode: true}
function buildGraph(edges) {
  let graph = Object.create(null);
  function addEdge(from, to) {
    if (graph[from] == null) {
      graph[from] = [to];
    } else {
      graph[from].push(to);
    }
  }
  for (let [from, to] of edges.map(r => r.split(" - "))) {
    addEdge(from, to);
    addEdge(to, from);
  }
  return graph;
}

const roadGraph = buildGraph(roads);
```

已知一组边，`buildGraph` 会创建一个 map 对象，其每个点都是一组与它相连的点。

{{index "split method"}}

它利用 `split` 方法从路线字符串，`"起始地 - 目的地"` 格式，变成两个元素的数组，包含起始地和目的地两个分开的字符串。

## The task

我们的((机器人))会在村中行走。在不同的地方会有需要被寄往其他地方的包裹。机器人沿途拿起遇到的包裹，并把他们运送到指定地点。

我们的自动机器需要决定，在每个点，它下一个需要去的地方。当所有包裹都寄到后，它的使命也就完成了。

{{index simulation, "virtual world"}}

为了模拟这个过程，我们需要定义一个能描述这个的虚拟世界。这个模型会告诉我们机器人的地点和所有包裹的位置。当机器人决定移动到其他地方时，我们需要更新该模型来反映新的情况。

{{index [state, in objects]}}

你如果在考虑((面向对象程序设计))，你的第一反应也许是为该世界中的元素定义对象：一个((类))给机器人、给包裹、也许也有地点。可以建些属性存他们当前的((状态))，比如包裹的地点可以随着更新世界而改变。

不过这是错误的。

至少，在通常情况下。一件东西听上去像是一个对象，不代表它应该是一个对象。条件反应的为每一个事物写一个类会让你的程序拥有一堆互联的对象，每个都有自己的可变动状态。这类程序往往难以理解且容易出错。

{{index [state, in objects]}}

相反的，我们把这个村庄的状态压缩到最小一组能定义它的值。也就是机器人当前地点和一个尚未邮寄的包裹的集，每个元素都有一个当前地点和目的地。

{{index "VillageState class", "persistent data structure"}}

与此同时，我们顺便确保在机器人移动时，我们不_改变_它的状态，却重新计算一个_新_状态描绘新的情况。

```{includeCode: true}
class VillageState {
  constructor(place, parcels) {
    this.place = place;
    this.parcels = parcels;
  }

  move(destination) {
    if (!roadGraph[this.place].includes(destination)) {
      return this;
    } else {
      let parcels = this.parcels.map(p => {
        if (p.place != this.place) return p;
        return {place: destination, address: p.address};
      }).filter(p => p.place != p.address);
      return new VillageState(destination, parcels);
    }
  }
}
```

`move` 方法是核心。它先查看是否有一条从当前点到目的地的路，如果没有，它返回原先的状态，因为当前路不通。

{{index "map method", "filter method"}}

它在创建一个新的状态，其目的地是机器人的新地点。但它也需要创建一个新的包裹集，机器人拥有的需要被寄到新地点的包裹（在机器人当前位置）。以及本就寄往新地点的包裹，这些包裹需要从为寄包裹集中移除。调用的 `map` 负责移动，而随后调用的 `filter` 负责邮寄。

包裹对象在移动中并没有被改变，他们被重新创建了。`move` 方法给我们一个新的村庄状态，并确保原先的状态完好无损。

```
let first = new VillageState(
  "邮局",
  [{place: "邮局", address: "Alice 家"}]
);
let next = first.move("Alice 家");

console.log(next.place);
// → Alice 家
console.log(next.parcels);
// → []
console.log(first.place);
// → 邮局
```

移动促使包裹被寄到，并在下一个状态中反映出来。但起始状态始终显示着机器人在邮局，而且包裹尚未被邮寄。

## Persistent data

{{index "persistent data structure", mutability, ["data structure", immutable]}}

不被改变的数据结构叫做_((不可变))_或者_一致性_。它们类似字符串和数字，因为它们始终保持自己，并不会因为时间而改变。

在 JavaScript 中，几乎所有东西都_能_被改变，所以和一致性数据打交到时，需要一些约束。有一个 `Object.freeze` 函数，会改变一个对象，忽视对其属性的更改。小心起见，你可以用它来确保对象不被改写。这会让电脑额外做些工作，而且无视更新在某种程度上和做错误的事一样让人迷糊。我通常喜欢直接告诉大家一个对象不应该被改变，并希望他们会记住。

```
let object = Object.freeze({value: 5});
object.value = 10;
console.log(object.value);
// → 5
```

我为什么要做额外的工作来确保对象不被改变，当语言本身希望我改变其属性？

因为这会帮助我了解我的程序。这又于复杂度管理相关。当程序中的对象为不可变时，我可以单独对其进行操作 —— 从一个起点移到 Alice 家永远会返回相同的新路线。当对象为可变时，路线的不确定性会增加程序的复杂的。

对于类似本章的小程序而言，我们可以接受略微的复杂度。但一个程序最重要的限制取决于我们的理解力。任何可以让代码更简单易懂的方法都会让程序更出彩。

可惜的是，虽然理解一个一致性数据结构的程序很容易，但_设计_一个，尤其在编程语言不配合的情况下，并不容易。本书中我们在找寻可以用一致性数据结构的机会的同时，也会用到可变形数据。

## Simulation

{{index simulation, "virtual world"}}

A delivery ((robot)) looks at the world and decides in which
direction it wants to move. As such, we could say that a robot is a
function that takes a `VillageState` object and returns the name of a
nearby place.

{{index "runRobot function"}}

Because we want robots to be able to remember things, so that they can
make and execute plans, we also pass them their memory and allow them
to return a new memory. Thus, the thing a robot returns is an object
containing both the direction it wants to move in and a memory value
that will be given back to it the next time it is called.

```{includeCode: true}
function runRobot(state, robot, memory) {
  for (let turn = 0;; turn++) {
    if (state.parcels.length == 0) {
      console.log(`Done in ${turn} turns`);
      break;
    }
    let action = robot(state, memory);
    state = state.move(action.direction);
    memory = action.memory;
    console.log(`Moved to ${action.direction}`);
  }
}
```

Consider what a robot has to do to "solve" a given state. It must pick
up all parcels by visiting every location that has a parcel and
deliver them by visiting every location that a parcel is addressed to,
but only after picking up the parcel.

What is the dumbest strategy that could possibly work? The robot could
just walk in a random direction every turn. That means, with
great likelihood, it will eventually run into all parcels and then
also at some point reach the place where they should be delivered.

{{index "randomPick function", "randomRobot function"}}

Here's what that could look like:

```{includeCode: true}
function randomPick(array) {
  let choice = Math.floor(Math.random() * array.length);
  return array[choice];
}

function randomRobot(state) {
  return {direction: randomPick(roadGraph[state.place])};
}
```

{{index "Math.random function", "Math.floor function", [array, "random element"]}}

Remember that `Math.random()` returns a number between zero and
one—but always below one. Multiplying such a number by the length of an
array and then applying `Math.floor` to it gives us a random index for
the array.

Since this robot does not need to remember anything, it ignores its
second argument (remember that JavaScript functions can be called with
extra arguments without ill effects) and omits the `memory` property
in its returned object.

To put this sophisticated robot to work, we'll first need a way to
create a new state with some parcels. A static method (written here by
directly adding a property to the constructor) is a good place to put
that functionality.

```{includeCode: true}
VillageState.random = function(parcelCount = 5) {
  let parcels = [];
  for (let i = 0; i < parcelCount; i++) {
    let address = randomPick(Object.keys(roadGraph));
    let place;
    do {
      place = randomPick(Object.keys(roadGraph));
    } while (place == address);
    parcels.push({place, address});
  }
  return new VillageState("Post Office", parcels);
};
```

{{index "do loop"}}

We don't want any parcels that are sent from the same place that they
are addressed to. For this reason, the `do` loop keeps picking new
places when it gets one that's equal to the address.

Let's start up a virtual world.

```{test: no}
runRobot(VillageState.random(), randomRobot);
// → Moved to Marketplace
// → Moved to Town Hall
// → …
// → Done in 63 turns
```

It takes the robot a lot of turns to deliver the parcels because it
isn't planning ahead very well. We'll address that soon.

{{if interactive

For a more pleasant perspective on the simulation, you can use the
`runRobotAnimation` function that's available in [this chapter's
programming environment](https://eloquentjavascript.net/code/#7).
This runs the simulation, but instead of outputting text, it shows
you the robot moving around the village map.

```{test: no}
runRobotAnimation(VillageState.random(), randomRobot);
```

The way `runRobotAnimation` is implemented will remain a mystery for
now, but after you've read the [later chapters](dom) of this book,
which discuss JavaScript integration in web browsers, you'll be able
to guess how it works.

if}}

## The mail truck's route

{{index "mailRoute array"}}

We should be able to do a lot better than the random ((robot)). An
easy improvement would be to take a hint from the way real-world mail
delivery works. If we find a route that passes all places in the
village, the robot could run that route twice, at which point it is
guaranteed to be done. Here is one such route (starting from the post
office):

```{includeCode: true}
const mailRoute = [
  "Alice's House", "Cabin", "Alice's House", "Bob's House",
  "Town Hall", "Daria's House", "Ernie's House",
  "Grete's House", "Shop", "Grete's House", "Farm",
  "Marketplace", "Post Office"
];
```

{{index "routeRobot function"}}

To implement the route-following robot, we'll need to make use of
robot memory. The robot keeps the rest of its route in its memory and
drops the first element every turn.

```{includeCode: true}
function routeRobot(state, memory) {
  if (memory.length == 0) {
    memory = mailRoute;
  }
  return {direction: memory[0], memory: memory.slice(1)};
}
```

This robot is a lot faster already. It'll take a maximum of 26 turns
(twice the 13-step route) but usually less.

{{if interactive

```{test: no}
runRobotAnimation(VillageState.random(), routeRobot, []);
```

if}}

## Pathfinding

不过我不认为盲目的追随一个固定路线是明智的。这个((机器人))的效益会提高，如果它根据所需工作而调节自己的路线。

{{index pathfinding}}

首先它需要先特意向一个包裹或一个包裹的目的地前行。因此，只要目标大于一步，就需要一些找寻路线的函数。

从((图解))中找寻一个路线是一个很标准的_((搜索问题))_。我们可以知道一个答案（一个路线）是否正确，但我们无法以 2 + 2 的方法直接计算出答案。所以我们需要不断创建新的解，直到我们找到工作的那个。

一个图解有无数个路线可能性。但在找寻从 _A_ 到 _B_ 的路线时，我们只对以 _A_ 为起点的路线感兴趣。我们同样对多次访问同一个地方的路线没兴趣，毕竟这些路线肯定不是最佳的。因此这大大减少了该路线找寻者需要考虑的路线。

此外，我们只对_最短_路线感兴趣。所以我们要确保从短的路线看起。一个不错的方法是从起始点“增长”路线，探索每一个尚未访问的点，直到到达目的地。这样，我们只会访问那些我们有兴趣的路线。因此我们会找到最短的（或者最短之一，如果有几个的话）路线。

{{index "findRoute function"}}

{{id findRoute}}

下面是可达此目的的函数：

```{includeCode: true}
function findRoute(graph, from, to) {
  let work = [{at: from, route: []}];
  for (let i = 0; i < work.length; i++) {
    let {at, route} = work[i];
    for (let place of graph[at]) {
      if (place == to) return route.concat(place);
      if (!work.some(w => w.at == place)) {
        work.push({at: place, route: route.concat(place)});
      }
    }
  }
}
```

探索必须按正确顺序完成：先到达的地点必须先探索。我们不能立刻探索一个刚刚抵达的点，因为这意味着_从该点_到达的其他地方也会被立刻探索。即使还存在着其他更短且尚未探索的路线。

所以，这个函数记录着一个_((工作单))_。这是一个需要被探索的地点数组，以及一个带我们到达这个点的路线。它最开始只是起始点和一个空路线。

这个搜索再从这个工作单中提取下一个点进行探索。也就是说所有从该点出发的路线都会被探索到。如果其中一个是目标，一个完整的路线会被返回。反之，如果我们尚未探索过该点，它会被加入到这个工作单中。如果我们已经看过这个点，这意味着我们要么找到了一个更长的路线，要么找到了一个同样长度的路线。所以，我们不用再探索它。

你可以把它想象成一个已知路线网从起始点均匀的蔓延开来（不过永远不回到自身）。只要第一条线到达目的地，它就会原路返回到起始点，告知我们这个路线。

{{index "connected graph"}}

当工作单中没有任何东西时，我们的代码就会不知所措。我们知道我们的图解是_连接图_，也就是说每个点都可以从其他点到达。我们永远可以找到一个连接两点的路线，因此这个搜索不会失败。

```{includeCode: true}
function goalOrientedRobot({place, parcels}, route) {
  if (route.length == 0) {
    let parcel = parcels[0];
    if (parcel.place != place) {
      route = findRoute(roadGraph, place, parcel.place);
    } else {
      route = findRoute(roadGraph, place, parcel.address);
    }
  }
  return {direction: route[0], memory: route.slice(1)};
}
```

{{index "goalOrientedRobot function"}}

这个机器人，和那个跟随路线的机器人一样，用它的内存值作为一个搬运方向的清单。当这个清单为空时，它需要判断它的下一步。它需要从集中得到第一个未寄到的包裹，如果这个包裹尚未被提取，计算一个提取包裹的线路。如果这个包裹_已经_被提取，它依旧需要被寄往目的地，所以这个机器人需要策划一个前往目的地的路线图。

{{if interactive

让我们看看这是如何实现的。

```{test: no, startCode: true}
runRobotAnimation(VillageState.random(),
                  goalOrientedRobot, []);
```

if}}

这个机器人通常会在 16步中完成 5个包裹寄送。尽管这结果比 `routeRobot` 好，却依然不是最佳的。

## Exercises

### Measuring a robot

{{index "measuring a robot (exercise)", testing, automation, "compareRobots function"}}

很难客观比较只解决过几个情况的机器人。也许其中一个机器人恰巧得到一组简单的任务，或者一组适合它的任务。但另一个机器人没有。

写一个函数 `compareRobots`，它的参数是两个机器人（和他们的起始内存）。它将产生 100个任务，并让每个机器人解答每一个任务。结束后，它会返回每个机器人的平均步数。

为了公平起见，确保每个机器人都拿到了相同的，而不是不同的，任务。

{{if interactive

```{test: no}
function compareRobots(robot1, memory1, robot2, memory2) {
  // 你的代码
}

compareRobots(routeRobot, [], goalOrientedRobot, []);
```
if}}

{{hint

{{index "measuring a robot (exercise)", "runRobot function"}}

你需要写一个类似 `runRobot` 的函数。与其用 console 记录事件，返回这个机器人完成任务所用的步数。

你的测量函数可以，在一个循环中，创建新的状态并记录每个机器人的所用步数。在它创建足够的状态后，它可以通过 `console.log` 返回每个机器人的平均值。也就是总步数除以测量数量。

hint}}

### Robot efficiency

{{index "robot efficiency (exercise)"}}

你能写一个比 `goalOrientedRobot` 更快捷的邮寄机器人么？如果你观察该机器人的行为，它做过什么特别愚蠢的事？如何改善它们呢？

如果你解答了上面的习题，你或许想用你的 `compareRobots` 函数来检测你是否改进了这个机器人。

{{if interactive

```{test: no}
// 你的代码

runRobotAnimation(VillageState.random(), yourRobot, memory);
```

if}}

{{hint

{{index "robot efficiency (exercise)"}}

`goalOrientedRobot` 的最大局限性是它每次只考虑一个包裹。它经常需要往返于村庄中，因为它需要提取的包裹在村庄的另一头，纵使它附近有其他包裹。

一个可能的解决方法是计算出每个包裹的路线，并取最短的。如果有多个最短路线，则可选择提取包裹而不是邮寄包裹，从而得到更优的结果。

hint}}

### Persistent group

{{index "persistent group (exercise)", "persistent data structure", "Set class", "set (data structure)", "Group class", "PGroup class"}}

标准 JavaScript 环境中，大部分的数据结构都不太适用于持久使用。数组有 `slice` 和 `concat` 方法，允许我们在不损坏原先数组的情况下创建新的数组。但 `Set` 并没有在添加或移除一个元素的情况下创建新的集的方法。

写一个新的类 `PGroup`，类似于[第六章](object#groups)中的 `Group` 类，储存一个值集。和 `Group` 一样，它有 `add`，`delete`，和 `has` 方法。

它的 `add` 方法会返回一个_新_ `PGroup` 实例，里面含有新加的元素。而不会改变原有的集。同样的，`delete` 创建一个新的没有给定元素的实例。

这个类应该适用于任何类型的值，而不仅是字符串。在庞大的值下，它_不_需要有效率。

{{index [interface, object]}}

((构造器))不应该在这个类的接口中（尽管你肯定会在内部用到它）。相反，有一个空的实例，`PGroup.empty`，可以用来做起始值。

{{index singleton}}

为什么你只需要一个 `PGroup.empty` 值，而不是一个每次创建一个新的空集的函数？

{{if interactive

```{test: no}
class PGroup {
  // 你的代码
}

let a = PGroup.empty.add("a");
let ab = a.add("b");
let b = ab.delete("a");

console.log(b.has("b"));
// → true
console.log(a.has("b"));
// → false
console.log(b.has("a"));
// → false
```

if}}

{{hint

{{index "persistent map (exercise)", "Set class", [array, creation], "PGroup class"}}

表示集的最方便的方法依旧是数组，因为数组容易被拷贝。

{{index "concat method", "filter method"}}

当一个值被加入到集中，你可以创建一个新的集，拷贝包含新值的原有的数组（比如利用 `concat`）。当一个值被移除时，你可以从数组中过滤掉它。

这个类的((构造器))可以以这样的数组作为参数，并储存它为该实例的（唯一）属性。这个数组永远不会被更改。

{{index "static method"}}

给一个构造器添加一个非方法的属性（`empty`），你需要在类定义只会，如一个正常属性般加入该构造器。

你只需要一个 `empty` 实例因为所以空集都是一样的，而且类的实例不会被改变。你可以在不改变这个空集的情况下，创建出许多不同的集。

hint}}
