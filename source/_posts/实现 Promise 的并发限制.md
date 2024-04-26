---
title: 实现 Promise 的并发限制
date: 2023-02-12 10:53:05
tags:
---

## 前言

JS 本身是单线程的一个语言，因此它不支持真正意义上的并发。即使在多个任务同时运行时，JavaScript 仍然只在单个线程中处理它们。

不过，JavaScript 支持异步编程，可以通过挂起执行主线程并在完成某些任务时再恢复它来模拟并发（事件循环机制）。例如，通过使用回调函数、Promise 和 async/await 等方式，可以在主线程上执行其他任务，而不会阻塞它。

## 实现目标

为了防止给下游造成突增 QPS，在咱们日常编程中经常有请求并发限制的需求，如果是实际开发可能直接使用类似 async 的一些异步库来进行限制。然而本文的目的是为了讲解限制 Promise 并发的原理，所以我们来实现一个简化版本，要求如下：
<span style="background-color: yellow">**同时添加 m 个 Promise 任务，限制同时并发 n 个**</span>，如以下示例：

```js
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

要求这 4 个 Promise 按顺序进行触发，但是同时只能运行 2 个，实现 PromiseLimit 类

## 代码实现

根据上面的实现目标，可以得到我们的类接收一个参数 `limit` 限制并发数量，还需要一个 `add` 函数用于添加 Promise。那么让我们来尝试实现它：

```js
class PromiseLimit {
    constructor(limit) {
        // 定义限制数量
        this.limit = limit;
        // 定义一个队列用于存储 Promise
        this.queue = [];
        // 用于记录目前在进行中的 Promise 数量
        this.pendingCount = 0;
    }

    add(fn) {
        // 需要执行的任务入队列
        this.queue.push(fn);
        this.run();
    }

    run() {
        while(this.pendingCount < this.limit && this.queue.length) {
            // 如果当前进行中的 Promise 没有达到限制并且队列不为空，则选出队头任务执行
            const fn = this.queue.shift();
            // 将进行中的数量加 1
            this.pendingCount++;
            // 这里函数执行 Promise，并且无论成功还是失败，都对当前正在进行的数量减 1，并实现自调用进入下一个 Promise 的执行
            fn().finally(() => {
                this.pendingCount--;
                this.run();
            })
        }
    }
}

```

这样就简单实现了 Promise 的并发控制，运行我们**【实现目标】**部分的代码可以得到如下效果：
![](/assets/promise-limit-result.gif)
可以看到最终实现了想要的效果。

## 总结

这个问题的核心问题是使用队列先入先出的特性将 Promise 任务入队，然后利用 Promise 之间的状态转化和 limit 参数限制来实现 Promise 同时执行的数量。
这里特别需要注意的是：<span style="color: red;">**`add` 函数不能直接接受一个 Promise 对象（如 new Promise()），需要传递一个函数，然后返回 Promise**</span>，否则 Promise 会立即执行，也就达不到限制并发的效果了

**温馨提示**：这是一道常见的面试题，建议彻底搞懂哦😋。