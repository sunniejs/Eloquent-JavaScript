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

Still, I wouldn't really call blindly following a fixed route
intelligent behavior. The ((robot)) could work more efficiently if it
adjusted its behavior to the actual work that needs to be done.

{{index pathfinding}}

To do that, it has to be able to deliberately move toward a given
parcel or toward the location where a parcel has to be delivered.
Doing that, even when the goal is more than one move away, will
require some kind of route-finding function.

The problem of finding a route through a ((graph)) is a typical
_((search problem))_. We can tell whether a given solution (a route)
is a valid solution, but we can't directly compute the solution the
way we could for 2 + 2. Instead, we have to keep creating potential
solutions until we find one that works.

The  number of possible routes through a graph is infinite. But
when searching for a route from _A_ to _B_, we are interested only in
the ones that start at _A_. We also don't care about routes that visit
the same place twice—those are definitely not the most efficient route
anywhere. So that cuts down on the number of routes that the route
finder has to consider.

In fact, we are mostly interested in the _shortest_ route. So we want
to make sure we look at short routes before we look at longer ones. A
good approach would be to "grow" routes from the starting point,
exploring every reachable place that hasn't been visited yet, until a
route reaches the goal. That way, we'll only explore routes that are
potentially interesting, and we'll find the shortest route (or one of the
shortest routes, if there are more than one) to the goal.

{{index "findRoute function"}}

{{id findRoute}}

Here is a function that does this:

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

The exploring has to be done in the right order—the places that were
reached first have to be explored first. We can't immediately explore
a place as soon as we reach it because that would mean places reached
_from there_ would also be explored immediately, and so on, even
though there may be other, shorter paths that haven't yet been
explored.

Therefore, the function keeps a _((work list))_. This is an array of
places that should be explored next, along with the route that got us
there. It starts with just the start position and an empty route.

The search then operates by taking the next item in the list and
exploring that, which means all roads going from that place are
looked at. If one of them is the goal, a finished route can be
returned. Otherwise, if we haven't looked at this place before, a new
item is added to the list. If we have looked at it before, since we
are looking at short routes first, we've found either a longer route
to that place or one precisely as long as the existing one, and we
don't need to explore it.

You can visually imagine this as a web of known routes crawling out
from the start location, growing evenly on all sides (but never
tangling back into itself). As soon as the first thread reaches the
goal location, that thread is traced back to the start, giving us our
route.

{{index "connected graph"}}

Our code doesn't handle the situation where there are no more work
items on the work list because we know that our graph is _connected_,
meaning that every location can be reached from all other locations.
We'll always be able to find a route between two points, and the
search can't fail.

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

This robot uses its memory value as a list of directions to move in,
just like the route-following robot. Whenever that list is empty, it
has to figure out what to do next. It takes the first undelivered
parcel in the set and, if that parcel hasn't been picked up yet, plots a
route toward it. If the parcel _has_ been picked up, it still needs to be
delivered, so the robot creates a route toward the delivery address instead.

{{if interactive

Let's see how it does.

```{test: no, startCode: true}
runRobotAnimation(VillageState.random(),
                  goalOrientedRobot, []);
```

if}}

This robot usually finishes the task of delivering 5 parcels in about
16 turns. That's slightly better than `routeRobot` but still definitely not optimal.

## Exercises

### Measuring a robot

{{index "measuring a robot (exercise)", testing, automation, "compareRobots function"}}

It's hard to objectively compare ((robot))s by just letting them solve
a few scenarios. Maybe one robot just happened to get easier tasks or
the kind of tasks that it is good at, whereas the other didn't.

Write a function `compareRobots` that takes two robots (and their
starting memory). It should generate 100 tasks and let each of
the robots solve each of these tasks. When done, it should output the
average number of steps each robot took per task.

For the sake of fairness, make sure you give each task to both
robots, rather than generating different tasks per robot.

{{if interactive

```{test: no}
function compareRobots(robot1, memory1, robot2, memory2) {
  // Your code here
}

compareRobots(routeRobot, [], goalOrientedRobot, []);
```
if}}

{{hint

{{index "measuring a robot (exercise)", "runRobot function"}}

You'll have to write a variant of the `runRobot` function that,
instead of logging the events to the console, returns the number of
steps the robot took to complete the task.

Your measurement function can then, in a loop, generate new states and
count the steps each of the robots takes. When it has generated enough
measurements, it can use `console.log` to output the average for each
robot, which is the total number of steps taken divided by the number
of measurements.

hint}}

### Robot efficiency

{{index "robot efficiency (exercise)"}}

Can you write a robot that finishes the delivery task faster than
`goalOrientedRobot`? If you observe that robot's behavior, what
obviously stupid things does it do? How could those be improved?

If you solved the previous exercise, you might want to use your
`compareRobots` function to verify whether you improved the robot.

{{if interactive

```{test: no}
// Your code here

runRobotAnimation(VillageState.random(), yourRobot, memory);
```

if}}

{{hint

{{index "robot efficiency (exercise)"}}

The main limitation of `goalOrientedRobot` is that it considers only
one parcel at a time. It will often walk back and forth across the
village because the parcel it happens to be looking at happens to be
at the other side of the map, even if there are others much closer.

One possible solution would be to compute routes for all packages and
then take the shortest one. Even better results can be obtained, if
there are multiple shortest routes, by preferring the ones that go to
pick up a package instead of delivering a package.

hint}}

### Persistent group

{{index "persistent group (exercise)", "persistent data structure", "Set class", "set (data structure)", "Group class", "PGroup class"}}

Most data structures provided in a standard JavaScript environment
aren't very well suited for persistent use. Arrays have `slice` and
`concat` methods, which allow us to easily create new arrays without
damaging the old one. But `Set`, for example, has no methods for
creating a new set with an item added or removed.

Write a new class `PGroup`, similar to the `Group` class from [Chapter
?](object#groups), which stores a set of values. Like `Group`, it has
`add`, `delete`, and `has` methods.

Its `add` method, however, should return a _new_ `PGroup` instance
with the given member added and leave the old one unchanged.
Similarly, `delete` creates a new instance without a given member.

The class should work for values of any type, not just strings. It
does _not_ have to be efficient when used with large amounts of
values.

{{index [interface, object]}}

The ((constructor)) shouldn't be part of the class's interface
(though you'll definitely want to use it internally). Instead, there
is an empty instance, `PGroup.empty`, that can be used as a starting
value.

{{index singleton}}

Why do you need only one `PGroup.empty` value, rather than having a
function that creates a new, empty map every time?

{{if interactive

```{test: no}
class PGroup {
  // Your code here
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

The most convenient way to represent the set of member values
is still as an array since arrays are easy to copy.

{{index "concat method", "filter method"}}

When a value is added to the group, you can create a new group with a
copy of the original array that has the value added (for example, using
`concat`). When a value is deleted, you filter it from the array.

The class's ((constructor)) can take such an array as argument and
store it as the instance's (only) property. This array is never
updated.

{{index "static method"}}

To add a property (`empty`) to a constructor that is not a method, you
have to add it to the constructor after the class definition, as a
regular property.

You need only one `empty` instance because all empty groups are the
same and instances of the class don't change. You can create many
different groups from that single empty group without affecting it.

hint}}
