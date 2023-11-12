```JavaScript
Array.prototype._myforEach = function(callback, thisBinding = globalThis) {
    if(typeof callback !== 'function') {
        throw new TypeError(`${callback} is not a function`);
    }
    if(this == null || typeof this[Symbol.iterator] !== 'function') {
        throw new TypeError(`${this} is not a iterator`);
    }
    const arr = [...this];
    for(let i = 0;i < arr.length;++i) {
	    // 之所以传递三个参数，是因为原生方法便是如此
        callback.call(thisBinding, arr[i], i, this);
    }
};
```

`callback` 是对数组每个元素进行操作的回调函数，`thisBinding` 是一个可选参数，用于指定回调函数中的 `this` 值。如果没有提供 `thisBinding`，它的默认值为全局对象（在浏览器中通常是 `window`，在 Node.js 中是 `global`）

使用 `call` 方法，我们可以指定回调函数中的 `this` 值。回调函数接受三个参数：当前元素、当前元素的索引和数组本身

当你在一个数组对象上调用这个 `_myforEach` 方法时，`this` 确实应该引用该数组对象。但在 JavaScript 中，函数的 `this` 值是可以被动态改变的。存在一些情况使这个 `this` 值不是一个数组或 `null` 或 `undefined`

###### 为什么一开始要初始化 `thisBinding = globalThis` ？

首先，当你在定义一个函数时，该函数内部的 `this` 是动态绑定的，除非你使用了箭头函数（箭头函数不绑定自己的 `this`）。这意味着 `this` 的值取决于如何调用该函数

```JavaScript
const obj = {
    name: 'Alice',
    sayHello: function() {
        console.log(`Hello, ${this.name}`);
    }
};

obj.sayHello();  // Hello, Alice
```

但如果你把这个函数取出并赋值给一个新的变量，然后调用这个新的变量，`this` 的行为就会改变：

```JavaScript
const greet = obj.sayHello;
greet();  // Hello, undefined (在某些环境中可能是 Hello, )
```

在这种情况下，因为 `greet` 是直接调用的（不是作为对象的方法），所以 `this` 默认绑定到全局对象（在非严格模式下）。在浏览器中是 `window`；在 Node.js 中是 `global`

初始化 `thisBinding` 为 `globalThis` 是为了模仿原生 JavaScript 函数（非严格模式下）在被直接调用时的默认行为。`globalThis` 是一个标准化的引用，无论在哪个环境下都指向全局对象

###### 为什么在调用 `callback` 时使用 `thisBinding` 作为 `this` 值，而不是直接使用 `this` ？

这样做可以为调用者提供更多的灵活性。想象一下，你有一个函数，你想在几个不同的上下文中重复使用该函数。通过允许调用者传入一个 `thisBinding`，你可以在不修改原始函数的情况下改变函数内部的 `this`

```JavaScript
const teamA = {
    members: ['Alice', 'Bob'],
    teamName: 'Team A'
};

const teamB = {
    members: ['Charlie', 'Dave'],
    teamName: 'Team B'
};

function listMembers(member, index) {
    console.log(`Member #${index + 1} of ${this.teamName}: ${member}`);
}

teamA.members._myforEach(listMembers, teamA);
// Member #1 of Team A: Alice
// Member #2 of Team A: Bob

teamB.members._myforEach(listMembers, teamB);
// Member #1 of Team B: Charlie
// Member #2 of Team B: Dave
```

在上面的例子中，我们使用相同的 `listMembers` 函数为两个不同的团队列出成员，只需通过改变 `thisBinding` 即可
