---
title: 异步
date: 2022-04-05 14:11:18
tags: [event loop,promise,async,await,防抖节流]
categories: [面试,手写]
---

##### 调用栈  消息队列  微任务队列  宏任务  微任务

>宏任务---->微任务---->渲染(alert阻塞)---->宏任务

1. 单线程:防止多线程同时操作dom,排队
2. 异步:防止代码堵塞,eventloop

##### promise手写要素(特点)

1. resolve=>fulfilled, reject=>rejected
2. 只能一次改变, throw===reject
3. resolve,reject函数绑定this
4. then方法传入参数是否是函数
5. 定时器导致的pending状态callback属性保存
6. then方法返回promise,状态由 then方法返回结果决定,
7. 用定时器模拟微任务状态

```js
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        setTimeout(() => {
            self.callbacks.forEach(item => {
                item.onResolved(data);
            });
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        setTimeout(() => {
            self.callbacks.forEach(item => {
                item.onRejected(data);
            });
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    const self = this;
    //判断回调函数参数
    if(typeof onRejected !== 'function'){
        onRejected = reason => {
            throw reason;
        }
    }
    if(typeof onResolved !== 'function'){
        onResolved = value => value;
        //value => { return value};
    }
    return new Promise((resolve, reject) => {
        //封装函数
        function callback(type){
            try{
                //获取回调函数的执行结果
                let result = type(self.PromiseResult);
                //判断
                if(result instanceof Promise){
                    //如果是 Promise 类型的对象
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{
                    //结果的对象状态为『成功』
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        //调用回调函数  PromiseState
        if(this.PromiseState === 'fulfilled'){
            setTimeout(() => {
                callback(onResolved);
            });
        }
        if(this.PromiseState === 'rejected'){
            setTimeout(() => {
                callback(onRejected);
            });
        }
        //判断 pending 状态
        if(this.PromiseState === 'pending'){
            //保存回调函数
            this.callbacks.push({
                onResolved: function(){
                    callback(onResolved);
                },
                onRejected: function(){
                    callback(onRejected);
                }
            });
        }
    })
}
//添加 catch 方法
Promise.prototype.catch = function(onRejected){
    return this.then(undefined, onRejected);
}

//添加 resolve 方法
Promise.resolve = function(value){
    //返回promise对象
    return new Promise((resolve, reject) => {
        if(value instanceof Promise){
            value.then(v=>{
                resolve(v);
            }, r=>{
                reject(r);
            })
        }else{
            //状态设置为成功
            resolve(value);
        }
    });
}

//添加 reject 方法
Promise.reject = function(reason){
    return new Promise((resolve, reject)=>{
        reject(reason);
    });
}

//添加 all 方法
Promise.all = function(promises){
    //返回结果为promise对象
    return new Promise((resolve, reject) => {
        //声明变量
        let count = 0;
        let arr = [];
        //遍历
        for(let i=0;i<promises.length;i++){
            //
            promises[i].then(v => {
                //得知对象的状态是成功
                //每个promise对象 都成功
                count++;
                //将当前promise对象成功的结果 存入到数组中
                arr[i] = v;
                //判断
                if(count === promises.length){
                    //修改状态
                    resolve(arr);
                }
            }, r => {
                reject(r);
            });
        }
    });
}

//添加 race 方法
Promise.race = function(promises){
    return new Promise((resolve, reject) => {
        for(let i=0;i<promises.length;i++){
            promises[i].then(v => {
                //修改返回对象的状态为 『成功』
                resolve(v);
            },r=>{
                //修改返回对象的状态为 『失败』
                reject(r);
            })
        }
    });
}
```



------



1. catch方法调用then方法只传入onrejected方法
2. resolve自身属性返回promise状态由传入状态决定
3. reject返回失败promise
4. all方法数组,count++时候等于length
5. race直接then方法,无参数是成功的promise

###### async,,await

1. await后面如过不接promise可能会不按照顺序,结果按照执行快慢
1. await 后面接promise且没有返回值,则后面的代码不会执行 

```js
 function wait(num) {
     setTimeout(() => {
         console.log(num * 2);
     }, 1000);
 }
 async function fn() {
     await wait(11)
     await wait(22)
 }
 fn()
```

2. async函数返回结果看函数return结果决定
2. await可以用try catch包裹

###### 实现

```js
 function* gen() {
     yield 1//yield+函数执行函数,并且函数返回值做value||接promise,value为pending状态的promiseA,调用then方法
     return 2
 }
 // console.dir(gen);//generator//产生器//suspended//暂停
 const g = gen()
 console.log(g.next());
 console.log(g.next());//走完了next方法返回值
 console.log(g.next());
 console.log(g.next());
 // { value: 1, done: false }
 // { value: 2, done: true }是否有return
 // { value: undefined, done: true }
 // { value: undefined, done: true }
```

next传值被yield接收 ,第二个开始传

```js
function* gen() {
    const res1 = yield 1
    console.log(res1);
    const res2 = yield 2
    console.log(res2);
    return 3
}
const g = gen()
console.log(g.next());
console.log(g.next(111));
console.log(g.next(222));
// { value: 1, done: false }
// 111
// { value: 2, done: false }
// 222
// { value: 3, done: true }
```

###### 防抖:停止动作后（时间点）+ 自定义时间（延迟）=> 触发事件  || 多变一   (形成闭包)

```js
function debounce(fn, delay) {
            let timer = null
            return function () {
                if (timer) {
                    clearTimeout(timer)
                }
                timer = setTimeout(() => {
                    fn.apply(this,arguments)
                    timer=null
                }, delay);
            }
        }
```

节流:多次操作变成少量操作(timer为true  异步队列)

```js
function throttle(fn, delay) {
            let timer = true
            return function () {
                if (timer) {
                    setTimeout(() => {
                        fn.apply(this,arguments)
                        timer = true
                    }, delay);
                }
                timer = false
            }
        }
```

###### 定时器误差

```js
setInterval(()=>{
    let i=0
    while(i++>1000000){

    }
},1000)

let now = Date.now()
let count = 0
let delay = 1000
let offset=0
let nextTime=delay-offset
let timer = setTimeout(fn, nextTime)
function fn() {
    count++
    console.log(Date.now() - (now + count * delay));
    offset=Date.now() - (now + count * delay)
    nextTime=delay-offset
    if (nextTime < 0) { nextTime = 0; }
    if (count < 10) {
        timer = setTimeout(fn, nextTime);
    }
}
```







settimeout: 误差HTML5规范规定最小延迟时间不能小于4ms，即x如果小于4，会被当做4来处理。 不过不同浏览器的实现不一样，比如，Chrome可以设置1ms，IE11/Edge是4ms



#### promise并发

###### 啥也不用

> while循环负责开始时填满并发池,入口触发addTask函数;;  request负责处理请求并且返回是否处理成功;;;  addTask函数触发request处理请求,并且往pool里面添加task任务状态,通过task成功状态,先将pool中的当前请求删除,将任务队列弹出,对弹出的判断是否不断往里面添加请求,
>
> while--->addTask---->request--->addTask---->task.then---->删掉当前task---->判断是否接着加task

```js
function request(url) {
            return new Promise(resolve => {
                setTimeout(() => {
                    resolve(`任务${url}完成`)
                }, 1000);
            }).then(res => {
                console.log(res)//业务逻辑
            })
        }
        //添加任务
        function addTask(url) {
            let task = request(url)
            pool.push(task)//填入
            task.then(res => {
                pool.splice(pool.indexOf(task), 1)
                // console.log(`${url}结束;;当前并发数${pool.length}`)
                url = urls.shift()
                if (url !== undefined) {
                    addTask(url)
                }
            })
        }
        let urls = ['00000', '11111', '22222', '33333', '44444', '55555', '66666', '77777']
        let pool = []//并发池
        let max = 3//最大并发数
        //循环塞满并发池
        while (pool.length < max) {
            let url = urls.shift()
            addTask(url)
        }
```













