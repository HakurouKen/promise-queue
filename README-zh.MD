# promise-queue
promise 队列

## Usage

利用队列，限制同一时间执行的 promise （返回promise 的异步函数）数量，例如多线程下载。

## Example

### 无穷队列
```javascript

// 用于测试的 promise 生成工厂
function PromiseGeneratorFactory(time){
    return function(){
        return new Promise(function(resolve,reject){
            setTimeout(function(){resolve(time)},time);
        });
    };
}

let Queue = require('promise-queue');
// 同一时间内，队列最多同时执行 3 个函数
let queue = new Queue(3);

queue.push(PromiseGeneratorFactory(100)).then(console.log);
queue.push(PromiseGeneratorFactory(200)).then(console.log);
queue.push(PromiseGeneratorFactory(300)).then(console.log);
// 下一个生成器不会被调用，会进入队列
queue.push(PromiseGeneratorFactory(400)).then(console.log);
// 下一个生成器将插入队列最前方
queue.unshift(PromiseGeneratorFactory(400)).then(console.log);
```

### 有限队列
```javascript

let Queue = require('promise-queue');
// 限制同一时间最多执行两个函数 ，队列中最多有两个函数在排队
let queue = new Queue(2,2);

queue.push(PromiseGeneratorFactory(100)).then(console.log);
queue.push(PromiseGeneratorFactory(200)).then(console.log);
// 下面两个 promise 生成器进入排队队列
queue.push(PromiseGeneratorFactory(300)).then(console.log);
queue.push(PromiseGeneratorFactory(400)).then(console.log);
// 下一个生成器会 reject， 错误信息为 `Maximum queue size exceeded`.
queue.push(PromiseGeneratorFactory(500)).catch(console.error);

```
