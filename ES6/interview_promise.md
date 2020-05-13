## Promise
### 含义
- 异步编程解决方案，相比于传统的 回调函数与事件 更加合理强大
- 特点
    - 对象状态不受外部影响，有三种状态pending（进行中）、fulfilled（成功）、rejected（失败）
    - 一旦改变就不会再变，任何时候都能得到该结果，这与事件完全不同，事件特点是错过了，再监听是得不到结果的。

- Promise，可以将异步操作以同步操作的流程表达出来，避免层层嵌套
- 缺点
    - 无法取消promise 一旦建立会立即执行，无法中途取消
    - 若不设置回调，Promise内部抛出的错误，不会反应到外部
    - 处于pending时无法知道处于什么阶段

### 基本用法
- Promise 接收一个函数作为参数，这个函数的两个参数分别为 `resolve` 与 `reject`，分别表示成功与失败的回调
- Promise 实例生成后可以通过 then 分别指定 resolve 与 reject
```js
const promise = new Promise((resolve, reject)=>{
    // ... code
    if(success){
        resolve()
    }else{
        reject()
    }
})

promise.then((res)=>{
    // success
}, ()=>{
    // error
})
```

- Promise 新建后会立即执行（同步），then中执行的方法为 微任务
```js
let promise = new Promise((resolve, reject)=>{
    console.log(2)

    resolve()
})

console.log(1)

// 输出顺序 2  1
```


