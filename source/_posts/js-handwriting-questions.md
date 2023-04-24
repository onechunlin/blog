---
title: 常见 JS 代码编程题
date: 2023-04-24 16:09:23
tags:
---

## 目的

JS 手写题是一些常见的算法或原生 API 的实现，是对 JS 基础知识的综合考察和对实际工作的应用。
在日常开发中，我们能够根据实际业务需求或者遇到性能问题等原因出现各种手写题。
而在面试中，手写题也是占据了很大的比例，主要是考察应聘者对于问题的分析与解决能力，以及对基础知识的熟练程度。

为此，本文着重介绍了一些常见的手写题，以帮助读者在实际开发和面试中更加得心应手。

## 题目

### 防抖

防抖函数常用于处理高频触发的事件，将多次触发的回调函数合并成一次执行，减少性能消耗

```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => {
      // 注意使用箭头函数，否则需要在外层临时存储 this 指向
      fn.apply(this, args);
    }, delay);
  };
}

// 测试
const debounceFn = debounce(() => {
  console.log("你点击了 window");
}, 1000);
window.addEventListener("click", debounceFn);
```

### 节流

节流函数常用于处理高频触发的事件，将多次触发的回调函数合并成固定时间间隔内执行一次，减少性能消耗。

```js
function throttle(fn, delay) {
  let lastTime = 0;
  return function (...args) {
    const nowTime = Date.now();
    if (nowTime - lastTime > delay) {
      fn.apply(this, args);
      lastTime = nowTime;
    }
  };
}

const throttleFn = throttle(() => {
  console.log("不论你点多快，1s 我只执行一次");
}, 1000);
window.addEventListener("click", throttleFn);
```

### 数据类型判断

一般来说我们使用 `typeof` 可以判断基本数据类型，使用 `instance of` 判断复杂对象类型。但是有时候在系统里我们需要一个统一的方法，那么可以理由 Object 原型上的 toString 方法来实现。

```js
function typeOf(obj) {
  // 去掉 [object Xxx] 中的无关字符并且最终结果转为小写
  return Object.prototype.toString.call(obj).slice(8, -1).toLowerCase();
}

console.log(typeOf([])); // array
console.log(typeOf({})); // object
console.log(typeOf(1)); // number
console.log(typeOf(true)); // boolean
console.log(typeOf(new Date())); // date
```

### 发布订阅模式

发布订阅模式是一种消息通信机制，其中发布者将消息发送到“主题”，而订阅者通过“订阅”该主题来接收消息。Node 中的 EventEmitter 是一种实现发布订阅模式的内置模块，可用于在不同组件之间通信，如在服务器端与客户端之间发送实时通知或在应用程序内的组件之间发送事件。它具有多个方法，包括 on、emit 和 once，可用于注册监听器、触发事件和单次监听事件。

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(name, listener) {
    if (!this.events[name]) {
      this.events[name] = [];
    }
    this.events[name].push(listener);
  }

  once(name, listener) {
    // 单次监听器，即执行一次后删除监听器
    const onceListener = (...args) => {
      listener.apply(this, args);
      this.removeListener(name, onceListener);
    }
    this.on(name, onceListener);
  }

  emit(name, ...args) {
    if (!this.events[name]) {
      return;
    }
    this.events[name].forEach(listener => {
      listener.apply(this, args);
    })
  }

  removeListener(name, fnHandle) {
    if (!this.events[name]) {
      return;
    }
    this.events[name] = this.events[name].filter(listener => listener !== fnHandle);
  }
}


// 创建事件对象
const em = new EventEmitter()
// 注册一个监听器
em.on('greet', function(name) {
  console.log(`Hello, ${name}!`)
})
// 注册一个单次监听器
em.once('bye', function() {
  console.log('Goodbye!')
})
// 发送一个事件
em.emit('greet', 'Alice')
// 发送一个单次事件
em.emit('bye')
// 再次发送单次事件 (不会触发)
em.emit('bye')
```

### 字符串模板

ES6 引入了的字符串模板，那么来看看是如何自己实现的吧。

```js
function render(template, context) {
  // 通过正则匹配出符合规则的变量组（看不懂这个正则的话可以了解一下 JS 正则括号的分组作用）
  const reg = /\$\{(\w+)\}/g;
  return template.replaceAll(reg, function(...args) {
    // 第 2 项为匹配到的变量值
    const [, variableName] = args;
    // 函数返回值即为要替换的值
    return context[variableName];
  })
}

const template = '我是${name}，今年 ${age} 岁了！';
// 变量上下文
const context = {
    name: '张三',
    age: 18
}
console.log(render(template, context)); // 我是张三，今年 18 岁了！
```

如果要实现类似 `{{variableName}}` 的模板，稍微更改一下正则即可实现。

### 函数柯里化

柯里化（Currying）是函数式编程的概念之一，指的是将一个接受多个参数的函数转化为一系列接受单一参数的函数的组合。

```js
function curry(fn) {
  return function curried (...args) {
    // 如果参数个数大于等于初始函数参数个数，则执行
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    // 如果参数少于初始函数格式，则返回当前函数
    return function(...args2) {
      return curried.apply(this, args.concat(args2));
    }
  }
}

function threeSum(a, b, c) {
  return a + b + c;
}
const cAdd = curry(threeSum);
console.log(cAdd(1)(2)(3)); // 6
console.log(cAdd(1, 2)(3)); // 6
console.log(cAdd(1, 2, 3)); // 6
```

### Promise Limit

为了防止给下游造成突增 QPS，在咱们日常编程中经常有请求并发限制的需求，如果是实际开发可能直接使用类似 async 的一些异步库来进行限制。

```js
class PromiseLimit {
  constructor(limit) {
    this.limit = limit;
    this.queue = [];
    this.pendingCount = 0;
  }

  add(fn) {
    this.queue.push(fn);
    this.run();
  }

  run() {
    while(this.pendingCount < this.limit && this.queue.length) {
      const fn = this.queue.shift();
      this.pendingCount++;
      fn().finally(() => {
        this.pendingCount--;
        this.run();
      })
    }
  }
}

const promiseLimit = new PromiseLimit(2);
promiseLimit.add(() => {
    console.log("promise 1");
    return new Promise((resolve) => {
        setTimeout(() => {
        resolve();
        }, 1000);
    });
});
promiseLimit.add(() => {
    console.log("promise 2");
    return new Promise((resolve) => {
        setTimeout(() => {
        resolve();
        }, 1000);
    });
});
promiseLimit.add(() => {
    console.log("promise 3");
    return new Promise((resolve) => {
        setTimeout(() => {
        resolve();
        }, 1000);
    });
});
promiseLimit.add(() => {
    console.log("promise 4");
    return new Promise((resolve) => {
        setTimeout(() => {
        resolve();
        }, 1000);
    });
});
```

详细的过程和解答可参考[实现 Promise 的并发限制](/2023/02/12/promise-limit/)

### 实现中间件效果

实现类似 express、koa 等洋葱🧅模型的效果，依次按顺序执行函数

```js
class App {
  constructor() {
    this.queue = [];
  }

  use(fn) {
    this.queue.push(fn);
  }

  run() {
    const fn = this.queue.shift();
    if (fn) {
      // 这里是一个了解 JS 中 this 的很好的场景，需要使用箭头函数或者 bind 改变 this 指向
      fn(() => this.run()) 
      // fn(this.run.bind(this))
    }
  }
}

// 测试
const app = new App();
app.use(next => {
    setTimeout(() => {
        next();
    }, 1000)
});
app.use(next => { 
    console.log("hello");
    next(); 
});
app.run(); // 调用后 1s 后输出 'hello'
```

详细的过程和解答可参考[用 JS 实现一个简单支持中间件的 APP](/2023/02/11/middleware-app/)

## 总结

本文介绍了一些常见的 JS 手写题，包括防抖、节流、数据类型判断、Promise 限制等。这些手写题和细节问题都是我们在日常开发中遇到的常见问题，在面试中也常常被问到。通过学习和掌握这些手写题，可以加深对 JS 基础知识的理解和应用，提高面试和实际开发的能力，帮助我们在实际工作中更加得心应手。
