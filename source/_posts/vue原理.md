---
layout: vue
title: 原理
date: 2022-04-25 15:24:45
tags: [响应式原理,diff算法,vue路由]
categories: vue原理
---

### 响应式原理

> 对象响应式
>
> 通过入口函数observe判断是否是对象,--->new Observe()循环递归对象的属性调用defineReactive(obj,key)--->进入函数的时候会调用observe判断obj[key]是不是对象,三则之间循环调用//   在observe函数中会判断是不是数组如果是.则会改写原型,并且调用observeArray方法判断子项是不是数组

------

> 收集依赖派发更新
>
> 在模板引擎运行时,当用户使用插值语法,此时new Watcher(),然后会触发watcher的get方法,重写get方法让他获取自己收集的数据,在获取收集数据时,就会触发defineReactive中的get方法,所以我们重写他的get方法让他可以将自身 (在Watcher中的get我们用window.target=this(实例)命名)   收集到dep中,(_ _ob_ _他身上储存着自己的依赖数组)//此时Watcher方法get还没有走完,所以需要在最后把他为null,其他的数据也需要进行收集依赖,(抢夺window.target)///   window.target就是Watcher实例(this//此时并未完成实例化无new Watcher)
>
> _ _ob_ _的存在让他们可以简单的获取Observe的方法 ob = new Observer(value)

------

> 数组重写
>
> 创造一个中介arrayMethods=Object.create(ArrayPrototype)    再将数据转化为响应式的时候(Observer)判断传入的不是数组,如果是强制改变原型Object.setPrototypeOf(value, arrayMethods)
>
> 重写方法就是添加更新视图操作,并且判断是不是添加数据splice,unshift,push,添加的数据也可能是响应式的.所以调用_ _ob_ _中的observeArray方法循环监视(若在此通知更新视图,则需要dep.notify())

------

> 工具函数的使用
>
> def调用此函数可以改写数据并且让数据无法被遍历def(obj,value,false)
>
> parsePath调用此函数可以识别点语法,obj.name.firstName

```js
//数组的操作
        let arrayPrototype = Array.prototype
        const arrayMethods = Object.create(arrayPrototype)
        MethodsNeedChange = [
            'push',
            'pop',
            'unshift',
            'shift',
            'splice',
            'sort',
            'reverse'
        ]


        //将一个属性变化为响应式
        function defineReactive(obj, key, val = obj[key]) {
            const dep = new Dep()//闭包//让get.set调用

            //观察新增的和修改的val//子元素形成递归
            let childOb = observe(val)
            Object.defineProperty(obj, key, {
                get() {
                    console.log('监视', key)
                    //判断是否是依赖收集阶段
                    if (Dep.target) {
                        dep.depend()
                        if (childOb) {
                            childOb.dep.depend()
                        }
                    }
                    return val
                },
                set(newVal) {
                    console.log('修改', key, newVal)
                    val = newVal
                    childOb = observe(newVal)
                    dep.notify()
                }
            })
        }

        //将对象转化为响应式
        class Observer {
            constructor(value) {
                this.dep = new Dep()
                //给内部添加__obj__属性,并且不可枚举
                def(value, '__ob__', this, false)
                if (Array.isArray(value)) {
                    Object.setPrototypeOf(value, arrayMethods)
                    this.observeArray(value)
                } else {
                    this.walk(value)
                }
            }
            //遍历对象的属性
            walk(value) {
                for (let key in value) {
                    defineReactive(value, key)
                }
            }
            //数组的特殊操作
            observeArray(value) {//防止数组长度发生变化let len
                for (let i = 0, len = value.length; i < len; i++) {
                    observe(value[i])
                }
            }
        }

        //入口函数,判断,递归的开始
        function observe(value) {
            if (typeof value !== 'object') return
            var ob//存储实例属性
            if (typeof value.__ob__ !== 'undefined') {
                ob = value.__ob__
            } else {
                ob = new Observer(value)
            }
            return ob
        }

        //工具函数让属性不被遍历
        function def(obj, key, value, enumerable) {
            Object.defineProperty(obj, key, {
                value,
                enumerable
            })
        }


        //数组方法的改写
        MethodsNeedChange.forEach(methodName => {
            const original = arrayPrototype[methodName]

            def(arrayMethods, methodName, function () {
                const ob = this.__ob__//这里面有很多方法用于响应式
                //push/splice/unshift
                let inserted = []
                switch (methodName) {
                    case 'push':
                    case 'unshift':
                        inserted = [...arguments]
                        break
                    case 'splice':
                        inserted = [...arguments].slice(2)
                        break;
                }
                if (inserted.length) {
                    ob.observeArray(inserted)
                }
                console.log('来来来')
                let res = original.apply(this, arguments)
                ob.dep.notify()//ob中保存dep属性
                return res
            }, false)
        });

        //收集依赖
        let uid1 = 0
        class Dep {
            constructor() {
                //存储自己的订阅则
                this.subs = []//存放watcher实例
                this.id = uid1++
            }
            //添加订阅
            addSub(sub) {
                this.subs.push(sub)
            }
            //添加依赖//window全局位置
            depend() {
                if (Dep.target) {
                    this.addSub(window.target)
                }
            }
            //通知更新
            notify() {
                console.log(111)
                const subs = this.subs.slice()
                for (let i = 0, len = subs.length; i < len; i++) {
                    suns[i].update()
                }
            }
        }
        //观察模板
        let uid2 = 0
        class Watcher {
            constructor(target, expression, callback) {//监听对象//表达式//回调函数
                this.id = uid2++
                this.target = target
                this.getter = parsePath(expression)
                this.callback = callback
                this.value = this.get()
            }
            update() {
                this.run()
            }
            //进入依赖收集阶段
            get() {
                Dep.target = this
                const obj = this.target
                let value
                try {
                    value = this.getter(obj)
                } finally {
                    Dep.target = null
                }
                return value
            }
            //运作
            run() {
                this.getAndInvoke(this.callback)
            }
            //刺激一下
            getAndInvoke(callback) {
                const value = this.get()
                if (value !== this.value || typeof value === 'object') {
                    const oldValue = this.value
                    this.value = value
                    callback.call(this.target, value, oldValue)
                }
            }
        }

        //工具函数辨别点语法找到数据
        function parsePath(str) {
            let segments = str.split('.')
            return obj => {
                for (let i of segments) {
                    if (!obj) return
                    obj = obj[i]
                }
                return obj
            }
        }
        // console.log(parsePath('a.c.d')({a:{c:{d:1}}}))

        let obj = {
            id: 001,
            identity: {
                name: 'lilei',
                age: 17
            },
            grades: [1, 2, 3],

        }
        observe(obj)
        // obj.grades.push(12)
        // console.log(obj.__ob__)
        obj.id = 111
        // obj.identity.age=12
        // console.log(obj.identity.age)
        // obj.grades.push(4)
        // obj.grades.splice(2, 1, 5, 6)

        // console.log(obj.grades)
        // obj.id=0
```



------

------



### diff算法

#### diff算法(dom数据化)

Vue2递归+双指针  

1. 判断是不是同一元素,不是则替换.
2. 是同一元素>>比对属性>>儿子节点(都有儿子节点则双指针遍历)

Vue3最长递增子数列

```js
//模板解析语法
class Vue {
    constructor(options) {
        this.$el = document.querySelector(options.el)
        this.$data = options.data//将配置对象传到自身
        this.compile(this.$el)
    }
    compile(node) {
        node.childNodes.forEach(item => {
            if(item.nodeType===1){
                this.compile(item)
            }
            if(item.nodeType===3){
                let reg=/\{\{(.*?)\}\}/g
                let text=item.textContent
                item.textContent=text.replace(reg,(match,vmKey)=>{
                    vmKey=vmKey.trim()
                    return this.$data[vmKey]
                })
            }
        });
    }
}
```

------

------



## hash

> 可以改变URL，但不会触发页面重新加载（hash的改变会记录在window.hisotry中）因此并不算是一次http请求，所以这种模式不利于SEO优化
> 只能修改#后面的部分，因此只能跳转与当前URL同文档的URL
> 只能通过字符串改变URL
> 通过window.onhashchange监听hash的改变，借此实现无刷新跳转的功能。

```js
 const btn =document.querySelector('#btn')
 //点击改变
 btn.addEventListener('click',()=>{
     location.href='#/222'
 })
 //通过前进后退和地址栏改变
 window.onhashchange=(e)=>{
     console.log(e.oldURL)
     console.log(e.newURL)
     console.log(location.hash)
 }
 //dom加载完毕打印
 window.addEventListener('DOMContentLoaded',()=>{
     console.log(location.hash)
 })
```

------

## history

> 新的URL可以是与当前URL同源的任意 URL，也可以与当前URL一样，但是这样会把重复的一次操作记录到栈中
> 通过参数stateObject可以添加任意类型的数据到记录中
> 可额外设置title属性供后续使用
> 通过pushState、replaceState实现无刷新跳转的功能(go)
>
> 缺点
>
> 当应用通过vue-router跳转到某个页面后，因为此时是前端路由控制页面跳转，虽然url改变，但是页面只是内容改变，并没有重新请求，所以这套流程没有任何问题。但是，如果在当前的页面刷新一下，此时会重新发起请求，如果nginx没有匹配到当前url，就会出现404的页面。那为什么hash模式不会出现这个问题呢?因为hash虽然可以改变URL，但不会被包括在HTTP请求中。它被用来指导浏览器动作，并不影响服务器端，因此，改变hash并没有改变url，所以页面路径还是之前的路径，nginx不会拦截。
> 因此，切记在使用history模式时，需要服务端允许地址可访问，否则就会出现404的尴尬场景。

```js
 const btn = document.querySelector('#btn')
 //点击改变
 btn.addEventListener('click', () => {
     const state = { name: 'user' }
     history.pushState(state, '', 'user')
 })
 //dom加载完毕打印
 window.addEventListener('DOMContentLoaded', () => {
     console.log('path:', '@@@', location.pathname)
 })
 //监听浏览器前进后退
 window.onpopstate = (e) => {
     console.log('onpopstate', '@@@', e.state, location.pathname)
 }
```

------

