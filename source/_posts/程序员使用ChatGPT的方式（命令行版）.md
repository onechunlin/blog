---
title: 程序员使用ChatGPT的方式（命令行版）
date: 2023-03-12 13:52:40
tags:
---

## 起因

ChatGPT 最近非常火，我也在第一时间注册了账号进行试用，作为学习和工作的辅助工具，在一些场景下确实可以给我们提效。

但是随着越来越火爆，ChatGPT 的网站经常挂掉，甚至在切换 Tab 或者页面长时间不使用时前端也会进行报错二次验证等。作为一个程序员，经常和终端接触，所以萌生了一个在终端做一个 ChatGPT 的想法。

## 效果展示

首先来看一下最终效果：
![效果展示](/assets/chatmate-result.gif)

项目最终源码：[https://github.com/onechunlin/side-projects/tree/master/packages/chatmate](https://github.com/onechunlin/side-projects/tree/master/packages/chatmate)

## 使用前提

1. 科学上网（需要可以切换节点到国外的）
2. 需要注册一个 ChatGPT 的账号，不会的可自行百度，很多教程。没有国外手机号也可以租~
3. 安装 Node 环境，详见[官网](https://nodejs.cn/download/)

## 配置终端代理

因为代理软件一般不能直接对终端发起的请求进行代理，所以这里可能需要配置终端的代理（如果你没问题可直接跳过）。

这里以我的代理 clash 在 zsh 终端配置为例：

首先，打开配置文件

```bash
open ~/.zshrc
```

然后添加对应的规则代理：

```bash
#  按需开启即可，这里只需要代理 https 请求，所以只配置 https 代理
export https_proxy=http://127.0.0.1:%mixedPort%
# export http_proxy=http://127.0.0.1:%mixedPort%
# export all_proxy=socks5://127.0.0.1:%mixedPort%
```

让配置生效

```bash
source ~/.zshrc
```

## 使用指引

方式 1：直接运行：

```bash
npx chatmate
```

方式 2：全局安装

```bash
npm install -g chatmate
```

然后运行

```bash
chatmate
```

更推荐方式 2，一次安装，后续即可多处使用。

至此，基本的使用过程就完成啦，开心在终端进行您的 ChatGPT 之旅吧！

## 总结

如果您对实现方式感兴趣的话，可以[查看源码](https://github.com/onechunlin/side-projects/blob/master/packages/chatmate/lib/index.ts)，全程使用 TypeScript 编写，且在关键的地方有注释，`Code will explain itself！`

**本工具如果对您有帮助的话，欢迎感兴趣的朋友一起共建！**

最后，如果本文对您有帮助的话，给我一个赞或 [Star](https://github.com/onechunlin/side-projects/tree/master/packages/chatmate) 将是我不断更新的动力


