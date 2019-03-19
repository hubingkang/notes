## 事件循环Event Loop

### 进程和线程
**进程**：描述了 CPU 在运行指令及加载和保存上下文所需的时间，放在应用上来说就代表了一个程序

**线程**：是进程中的更小单位，描述了执行一段指令所需的时间

### 浏览器的Event Loop

```js
console.log('script start')

async function async1() {
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end')
}
async1()

setTimeout(function() {
  console.log('setTimeout')
}, 0)

new Promise(resolve => {
  console.log('Promise')
  resolve()
})
  .then(function() {
    console.log('promise1')
  })
  .then(function() {
    console.log('promise2')
  })

console.log('script end')
// script start => async2 end => Promise => script end => promise1 => promise2 => async1 end => setTimeout
```

<!-- script start: 最先进入。

async2 end: async2被调用，返回了一个 `Promise.resolve(undefined)`，此时，该回调被推入 microtask ，`async1 函数中的执行权被让出，等待主线程空闲` -->

1. 打印 `script start`
 
2. 解析引擎解析至 async1() ，async1 执行环境被推入执行栈，解析引擎进入 async1 内部
  
3. 引擎发现 async1 内部调用了 async2，于是继续进入 async 2，并将 async 2 执行环境推入执行栈
   
4. 引擎碰到 console.log，于是打印 `async2 end`
   
5. async2 函数执行完成，`返回了一个 Promise.resolve(undefined)，此时，该回调被推入 microtask` ，async1 函数中的执行权被让出，等待主线程空闲
   
6. 引擎解析至 setTimeout，`等待 0ms 后将其回调推入 macrotask`，执行权继续让出
   
7. 引擎指针继续下移，直到碰到了 Promise，解析进入注入函数的内部，打印 Promise，再往下，`碰到了 resolve，此时，该回调被推入 microtask` ，执行权被让出
   
8. 引擎继续往下，打印 `script end`
   
9.  至此，`主线程空闲，Event Loop 事件循环启动`，开始从 microtask 里拿出 promise 回调，放入主线程执行，首先拿出最早注入的 async2 的 Promise.resolve(undefined)执行，此时 await 操作符解析该表达式，得到结果 undefined，并将 async1 [Promise] 函数 标志为 resolve 状态，将 await 后面的代码作为回调，继续推入 microtask，等待执行，执行权被让出
    
10. 此时主线程没有可执行的代码，再次空闲，Event Loop 启动，去 microtask 中拿到之前的 new Promise 回调，放入主线程执行，打印结果 promise1 和 promise2
    
11. 主线程空闲，Event Loop 去 microtask 里拿 aysnc1 的回调，打印出 async1 end
    
12. 最后，主线程空闲，microtask 队列空，Event Loop 去 macrotask 里拿到 setTimeout 的回调，放入主线程，打印最后的 setTimeout

> 注意：新的浏览器中不是如上打印的，因为 await 变快了，async1 end 优先于promise1 和 promise2打印

### Node事件循环

[Node事件循环](https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick/)