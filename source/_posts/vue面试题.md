---
title: vue面试题
date: 2022-04-11 12:58:01
tags: vue
categories: [面试,vue总结知识点]
---

##### 初始化项目 vue create



##### 语法检测eslint关闭   lintOnSave: false,



##### 监听属性的深度监听因为handle函数拿不到对象(引用类型)的oldvalue,立即监听    watch监听路由数据变化

computed和methods相比前者有缓存,后者无缓存

computed和watch相比计算一个,监控多个个时候比较麻烦



###### v-if,不去渲染(一次性改变),v-show设置dispaly:none/block(频繁改变),会增加渲染



##### 绑定样式类名 :style class对象数组形式



##### v-for 遍历对象的时候拿到的值(value,key,index) 

不能和v-if连用,v-for优先级高,导致已经渲染出来了v-if判断又要删除掉,浪费性能,可以在v-for外面增加一层template将其设置为v-if,,,,,, 优先级源码判断if条件的时候once->for->if



##### methods方法里面的e,e.target,原生事件,e.target.constructor,,,,$event(传多个参数)



##### 组件传信props,$emit,$bus(mounted挂载,beforeDetroy解绑)



##### 生命周期钩子,beforeUpdated先父后子update先子后父



##### $nextTick,vue渲染方式是异步的,会在下一个tick渲染会导致慢一步,,这个方法会在异步渲染完毕后执行(批量进行渲染),在轮播图组件////vue异步更新原因是减少频繁更新性能浪费,在本轮操作结束后再更新///nextTick 采用宏任务微任务即promise,settimeout,setInmediate回调

原因:到这里，整体nextTick的代码都分析完毕了，总结一下它的流程就是：

把回调函数放入callbacks等待执行

将执行函数放到微任务或者宏任务中

事件循环到了微任务或者宏任务，执行函数依次执行callbacks中的回调

再回到我们开头说的setTimeout，可以看出来nextTick是对setTimeout进行了多种兼容性的处理，宽泛的也可以理解为将回调函数放入setTimeout中执行；不过nextTick优先放入微任务执行，而setTimeout是宏任务，因此nextTick一般情况下总是先于setTimeout执行，我们可以在浏览器中尝试一下：

[相关链接](https://blog.csdn.net/ych6666/article/details/112428797)

```js
this.$nextTick(()=>{
    //函数体
})
```

##### 插槽slot/作用域插槽,父组件引用子组件,在父组件的子组件填东西,子组件挖坑(可以设置默认内容),,,,作用域插槽子中有数据给父亲传,父亲解构接受

```js
//父
<Child>
         <template v-slot:default="{user}">
              {{user.age}}
         </template>
    </Child>
//子
<slot :user='user'> {{user.name}}</slot>
```

##### 动态渲染组件再父组件中设置

```js
<component :is="data"></component>
//data最终结果要和组件名相同,则展示
```

##### 异步组件, 组件加上v-if,在页面开始挂载的时候将所有需要的东西下载了,即使v-if为false,即显示组件时不在重新发送网络请求,,,, 会导致首次加载影响性能,import语法,

```js
components:{
    Child:()=>import ('./components/Child')
}
//动态引入返回promise对象//路由懒加载,组件懒加载
//webpack会将懒加载的路由分块打包到一个单独的js中去，只有加载该路由的时候，才会加载这个chunk文件。
```

##### keep-alive,(提升性能),   组件来回切换加载一次后则不会销毁keep-alive,而尽量不要使用v-show因为一次性加载太多东西影响性能,组件简单可以使用,,,, 多两个钩子activated,deactivated   

如果要在进入缓存的组件 发送数据需要在activated钩子中发送请求



##### 生命周期 create,mounte,updated,destroied,,,, 加入keep-alive包裹标签,多两个钩子activated,deactivated(每次离开都会执行)      

```js
beforeCreate//啥也没有
create//data没有$el
beforeMount//data没有$el
mounted//都有
```



##### scoped样式独立,,,样式穿透某些属性需要让别的生效,sass/less     ::v-deep/子元素,,,,    css     >>> 



##### props和data优先级谁高props->methods->data->computed->watch

------



#### vuex 状态管理(state,getters,mutations(同步操作),actions(异步操作))(mapstate)

单向数据流组件不能改变/不是持久化存储(保存到cookie,localstorage)

#####　vue代理

```js 
//跨域问题:vue.config.js文件中
module.exports = {
  devServer: {
    proxy: 'http://localhost:4000'
  }
}
```

##### vue打包后空白页问题   1. 路径问题 (publicPath:'./')2. 路由模式(hash模式有#出现问题)[后端重定向]  3. 跨域问题模式和环境变量



###### vue-router 模式history,hash#       history会发送请求,hash不会(找不到页面的时候)



##### SPA单页面应用:SEO优化不好,性能不好



vue路径传值:    会在路径上显示出来query

```js
1. 显式
this.$router.push({
    path:'/about',
    query:{
        a:1
    }
})
//接this.route.query
2. 隐式
this.$router.push({
    path:'/about',
    params:{
        a:1
    }
})
//接this.$route.params
```



##### 路由导航守卫:  全局  路由独享  组件内

全局路由守卫   

1. 全局守卫：前置守卫：beforeEach 后置钩子：afterEach

2. 单个路由守卫：独享守卫：beforeEnter

3. 组件内部守卫：beforeRouteEnter beforeRouteUpdate beforeRouteLeave 

```js
 beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

路由独享守卫:beforeEnter

组件内守卫:beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave





## 

##### 数据劫持

```js
proxyData(){
    for(let key in this.$data){
        Object.defineProperty(this,key,{
            get(){
                return this.$data[key]
            },
            set(val){
                this.$data[key]=val
            }
        })
    }
}
//在渲染的时候将元素存储到对象中


let input=document.querySelector('input')
        let p=document.querySelector('#p')//展示内容
        let obj={}
        Object.defineProperty(obj,'name',{
            set(val){
                p.innerHTML=val
            },
            get(){
                
            }
        })
        input.addEventListener('input',function(e){
            obj.name=e.target.value
  
```





##### 视图更新

```js
class Watch{
    constructor(vm,key,node,attr){
        this.vm=vm//对象
        this.key=key//属性名
        this.node=node//节点
        this.att=attr//改变文本内容的字符串
    }
    update(){
        this.node[this.attr]=this.vm[this.key]//赋值
    }
}
```

##### 数据双向绑定





##### Vue中key的作用

可以在patch过程中判断新老加点是否相同,能否复用(不为index因为有时不在列表最后新增元素)





##### proxy

```js
//proxy
const p=new Proxy(target,handler)
```







是Writable：决定是否可以修改属性的值；
另一个是Configurable：决定是否可以使用Object.defineProperty()来修改属性描述符，并且也会禁止删除这个属性；
当这两个描述符同时为false时，意味着这个属性完全是一个常量，Vue不能通过Object.defineProperty()来添加双向数据绑定。收集不了依赖，也就做不了双向数据绑定，就少添加很多watcher实例





#### 优化

1. 编码优化

* 不要把太多的数据放入data中,发布订阅模式会收集多个watcher
* v-for的时候使用事件代理
* 拆分组件复用
* 多用v-if,
* 路由懒加载

2. 加载优化

* 按需加载第三方模块
* 图片懒加载

3. 打包优化

* cdn加载第三方模块(防止代码体积过大,防止服务器不稳定)



#### 防止xss攻击

不使用v-html和innerhtml

解决方法: 可以使用lodash里面的escape方法对客户信息进行转译



