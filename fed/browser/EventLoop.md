# 事件循环小理解

[来搞一波测试题试试？](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)

js 作为一门单线程的编程语言，使用了“EVENT LOOP”这种模型，来保证正确的执行异步代码。

![v8](https://cdn-images-1.medium.com/max/800/1*7GXoHZiIUhlKuKGT22gHmA.png)

> 调用栈（call stack）

js 引擎实现的一种数据结构，它决定了哪些函数正在执行，并且之后哪些函数将要执行，执行完毕的函数，会被 pop 掉。

> Event Table & Event Queue

每当我们进行了异步的操作，这些异步操作对应的待执行函数会被添加到 Event Table 里面去，Event Tabel 也是一种数据结构，它的作用是确保在某些固定的 event 发生时，将对应的待执行的异步函数加入到 Event Queen。
Event Queen 是一个类似队列的数据结构，他的作用是存储正确的函数调用顺序，然后再由 Event Loop 将其中的函数推入到 call stack.

补充：js 中的任务概念 task,执行代码的过程中任务一般分为了两种，macro task & micro task
典型的宏任务 macro:script,setTimeout,setInterval,setImmediate,I/O,UI render
典型的微任务 micro:promise.then,MutationObserver,MessageChanel
在一次任务执行中 macro -> micro -> render...
当主线程的任务执行完毕，下一个宏任务会继续执行，当任务中有微任务微任务会先进入执行栈，当微任务队列没有任务时，才会执行宏任务的队列。
![marcro & micro](https://blog-assets.risingstack.com/2016/10/the-Node-js-event-loop.png)

> event loop

事件循环机制可以理解为一个 process，浏览器会定时的跑，当浏览器发现 call stack 空了的时候，它会去检查 event queen,如果 queen 里面不是空的，它会把里面的待执行函数推入到 call stack。

> some blog

[JSConf vedio about event loop](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
[blog1](https://blog.risingstack.com/node-js-at-scale-understanding-node-js-event-loop/)
[blog2](https://www.w3ctrain.com/2018/02/01/javascript-event-loop/)
