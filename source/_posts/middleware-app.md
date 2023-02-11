---
title: 用 JS 实现一个简单支持中间件的 APP
date: 2023-02-11 16:19:08
tags:
---

# 题目要求

先说一下要求，app 实例能够添加中间件，并按顺序执行。示例如下：

```js
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
- 第一个中间件是一个计时器，它将等待 1 秒后调用 next 函数。
- 第二个中间件将在第一个中间件完成后立即运行，并在控制台中打印 hello。
- 最后，调用 app.run() 将启动应用程序。由于存在两个中间件，因此调用 1s 后输出 'hello'。

# 代码实现

这是在一次面试中遇到的一道题，其实实现起来很简单，我们先以简单的方式来实现一下，直接上代码：

## 数组实现

```js
class App {
    constructor() {
        // 初始化一个数组，用于存放中间件
        this.queue = [];
    }

    use(middleware) {
        // 直接将中间件插入数组
        this.queue.push(middleware);
    }

    run() {
        const runMiddleware = index => {
            const middleware = this.queue[index];
            // 如果当前数组没有中间件了，则返回
            if (!middleware) {
                return;
            }
            // 中间件中调用的 next 函数就是下面的 () => runMiddleware(index + 1)
            middleware(() => runMiddleware(index + 1));
        }
        runMiddleware(0);
    }
}
```

简单分析一下这里的关键点，`next` 函数如何注入，首先 `next` 函数调用没有参数，所以上面的 `() => runMiddleware(index + 1)` 中 `()` 没有参数，调用 `next` 函数时需要调用进行下一个中间件的执行，所以需要自调用。

我们来看一下效果：
![](/assets/result.gif)

## 队列实现

我们来看看另一种实现，可能通过上面的变量的命名就能够看出来我们要用队列，其实也很简单，我们只需要将 `runMiddleware` 中的 `index` 去掉，改为使用队列出队即可：

```js
class App {
    constructor() {
        // 初始化一个队列，用于存放中间件
        this.queue = [];
    }

    use(middleware) {
        // 直接将中间件插入队列
        this.queue.push(middleware);
    }

    run() {
        const runMiddleware = () => {
        const middleware = this.queue.shift();
        // 如果当前队列没有中间件了，则返回
        if (!middleware) {
            return;
        }
        // 中间件中调用的 next 函数就是下面的 () => runMiddleware()
        middleware(() => runMiddleware());
        };
        runMiddleware();
    }
}
```

# 总结

这道题的关键点在于 `next` 函数（即上文的 `runMiddleware`）如何注入以及如何通过链式调用下一个中间件。