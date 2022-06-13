---
title: _call_bind_apply
date: 2022-04-04 12:21:36
tags: [js,call,bind]
categories: [面试,手写]
---



需要满足三点

> //改变this指向
> // 参数变化
> //返回结果

##### call手写(找出调用者,将方法变为自身)////   

  根据ECMAScript262规范规定：如果第一个参数传入的对象调用者是null或者undefined，call方法将把全局对象（浏览器上是window对象）作为this的值。所以，不管传入null 还是 undefined，其this都是全局对象window。所以，在浏览器上答案是输出 window 对象

要注意的是，在严格模式中，null 就是 null，undefined 就是 undefined：

```js
 Function.prototype.myCall = function () {
            let args =[...arguments]
            const thisVal = args.shift()
            thisVal.fn = this//将环境换成调用者内部改变函数this指向
            let res = thisVal.fn(...args)
            delete thisVal.fn
            return res
        }
```

###### apply(传两个参数给解开)

```js
 Function.prototype.myApply = function () {
            let args = [...arguments]
            const thisVal = args.shift()
            thisVal.fn = this//将环境换成调用者内部改变函数this指向
            let res = thisVal.fn(...args[0])
            delete thisVal.fn
            return res
        }
```



##### bind

> bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

```js
const people = {
            name: 'lilei',
            age: 14
        }
function sing(a) {
            console.log(this.name, a);
        }
        let say = sing.bind(people, 1)
        say()
```

###### 手写

```js
Function.prototype.myBind = function () {
    	// console.log(this);//this是使用者函数需要保存
    	const self=this
    	// console.log(arguments);//类数组转化为数组
    	//const args=Array.prototype.slice.call(arguments)//将数组方法用call借用,得到数组
        const args=[...arguments]
        // console.log(args);
        const thisVal=args.shift()//将需要改变的this和参数分开
            
            return function () {
                return self.apply(thisVal,args)//满足调用时有返回结果/是原函数的返回值
            }
        }
```





