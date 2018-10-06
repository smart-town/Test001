在JS中所有代码都是单线程执行的。由于这个缺陷导致JS中所有的网络操作、浏览器事件都必须是异步执行。
## 异步执行
异步执行可以使用**回调函数**来实现：
```
function callback(){
    console.log("Done") ;
}
console.log("Before set Timeout") ;
setTimeout(callback, 2000) ;
console.log("After set Tiemout") ;
```
即，异步操作会在将来的某个时间点触发一个函数调用。AJAX就是典型的异步操作。

## Promise对象
“承诺在将来执行”的对象，在js中被称为Promise对象。Promise有各种开源实现，在ES6中被统一规范，由浏览器直接支持。
### 认识
所谓Promise，字面为“承诺”，即A调用B，B返回一个承诺给A，然后A就可以在写计划时这样写：当B返回结果给我的时候，A执行方案S1,反之如果B因为某些原因没有给A想要的结果时，那么A执行应急方案S2。这样一来，所有潜在的风险都在A的可控范围之内了。

Promise 规范如下：
- 一个promise可能有三种状态：等待pending、已完成fulfilled、已拒绝rejected
- 一个promise的状态只能从“等待”转换为“完成”或“拒绝”，不能逆向转换。同时完成和拒绝不能相互转换
- promise必须实现then方法，可以说，then就是promise的核心。而且then**必须返回一个Promise**，同一个Promise的then可以调用多次，并且回调的执行顺序跟它们被定义时的顺序一致。
- then方法接受两个参数，第一个参数是成功时的回调，在promise由等待转换为完成时调用；另一个是失败的回调，在promise由等待转换为拒绝时使用。同时，then可以接收另一个promise传入，也接受一个“类then”的对象或方法，即thenable对象。
### 原理
可以看到Promise的规范并不是很多，下面进行分析promise和实现。大致思路如下：构造函数Promise接受一个函数resolver，可以理解为传入一个异步任务，resolver接受两个参数，一个是成功时的回调，一个是失败时的回调。这两个参数和通过then传入的参数是对等的。其次是then的实现，由于Promise要求then必须返回一个promise，所以在then调用的时候会生成一个新的promise，挂在当前的promise中的_next上。同一个promise多次调用都只会返回之前生成的_next。

### 标准使用
从之前中的概念中可以看到，一个Promise调用另一个Promise。每个Promise会执行失败或执行成功，对应的处理为指定的回调函数。

**一、一个Promise方法，即包含成功或失败时的具体处理方法**：
```
function test(resolve, reject){
    var timeout = Math.random() * 2 ;
    console.log("set timeout to:" + timeout ) ;
    setTimeout(function(){
        if(timeout < 1){
            console.log("call resolve...") ;
            resolve(" 200 ok ") ;
        } else {
            console.log("call reject ... ") ;
            reject("timeout in " + timeout) ;
        }
    }, timeout * 1000) ;
}
//这里可以看到test函数只关注自身的逻辑执行，并不关心具体的resolve和reject将如何处理结果
```
**二、根据promise方法生成一个Promise对象，并可以指定具体的执行成功或失败时的函数**
```
var p1 = new Promise(test) ;
var p2 = p1.then(function(result){console.log("SUCCESS! " + result)}) ;
var p3 = p2.catch(function(reason){console.log("Fail:" + reason);}) ;
变量 p1 为一个Promise对象，它具体负责执行test函数，由于test函数是在内部异步执行的，当test函数成功时，Promise对象执行then中指定的方法，执行失败时则执行catch中的方法。
```
**Promise对象可以串联起来，上述代码可以简化为**
```
new Promise(test).then(...).catch(...) ;
```

### 总结及注意
可以看到Promise对象最大的好处是在异步执行的流程中，将执行代码和执行逻辑分离开来了。它还可以做更多的事情，如若干个任务，需要先做任务1，在做任务2，任何任务失败则不再继续并执行错误处理函数。要执行这样的异步任务，不用promise需要一层一层写嵌套代码， 而有了promise则只要写：job1.then(job2).then(job3).catch(handleError) ;其中job1、job2、job3都是Promise对象

并行执行异步任务。**Promise.all**

多个异步任务是为了容错，只需要得到其中一个返回值即可时，**Promise.race**