---
title: Vue异步更新机制
date: 2022-01-21 09:40:45
tags: 异步更新机制
---
## Vue的异步更新机制是如何实现的？
Vue的异步更新机制的核心是利用了**浏览器异步任务队列**来实现的，首选微任务队列，宏任务队列次之

党响应式数据更新后，会调用dep.notify方法，通知dep中收集的watcher去执行update方法，watcher.update将watcher自己放入一个watcher队列（全局的queue数组）

然后通过nextTick方法将一个刷新watcher队列的方法(flushSchedulerQueue)放入一个全局的callbacks数组中

如果此时浏览器的异步任务队列中没有一个叫flushCallbacks的函数，则执行timerFunc函数，将flushCallbacks函数放入异步任务队列。如果异步任务队列中已经存在flushCallbacks函数，等待其执行完成以后再放入下一个flushCallbacks函数

flushCallbacks函数负责执行callbacks数组所有的flushSchedulerQueue函数

flushSchedulerQueue函数负责刷新watcher队列，即执行queue数组中每一个watcher的run方法，从而进入更新阶段，比如执行组件更新函数或者执行用户watch的回调函数

## Vue的nextTick API是如何实现的？
Vue.nextTick或者vm.$nextTick的原理，其实就是做了两件事：
- 将传递的回调函数用try catch包裹然后放入callbacks数组
- 执行timerFunc函数，在浏览器的异步任务队列放入一个刷新callbacks数组的函数

$nextTick首选微任务
  - promise
  - MutationObserver
  - setTmmediate
  - setTimeout
