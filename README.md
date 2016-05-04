# promise-queue
A queue use for promises.

## Usage

Use queue to limit the number of promise executing at the same time. For example, multi-thread download.

## Example

### Infinity Queue
```javascript

// A factory products promiseGenerator, use for test.
function PromiseGeneratorFactory(time){
    return function(){
        return new Promise(function(resolve,reject){
            setTimeout(function(){resolve(time)},time);
        });
    };
}

let Queue = require('promise-queue');
// set the max promise-resolver as 3 at the same time.
let queue = new Queue(3);

queue.push(PromiseGeneratorFactory(100)).then(console.log);
queue.push(PromiseGeneratorFactory(200)).then(console.log);
queue.push(PromiseGeneratorFactory(300)).then(console.log);
// the next generator will wait until one of promise above is resolved.
queue.push(PromiseGeneratorFactory(400)).then(console.log);
// the next generator will cut in the queue.
```

### Limited Queue
```javascript

let Queue = require('promise-queue');
// set the max promise-resolver as 3 at the same time.
let queue = new Queue(2,2);

queue.push(PromiseGeneratorFactory(100)).then(console.log);
queue.push(PromiseGeneratorFactory(200)).then(console.log);
// next two promiseGenerator will queue until promises above finished.
queue.push(PromiseGeneratorFactory(300)).then(console.log);
queue.push(PromiseGeneratorFactory(400)).then(console.log);
// the next promiseGenerator will reject with error `Maximum queue size exceeded`.
queue.push(PromiseGeneratorFactory(500)).catch(console.error);

```
