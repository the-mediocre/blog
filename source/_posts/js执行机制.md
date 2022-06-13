---
title: AJAX
date: 2022-04-06 17:14:22
tags: [AJAX,浏览器]
categories: [面试,手写]
---

#### 测试地址 https://autumnfish.cn/api/joke          

 [链接大全](https://blog.csdn.net/IT_ZHONG/article/details/103005828)        

###### 原生js

```js
//get
function get(url) {
            const xhr = new XMLHttpRequest() //创建实例
            xhr.open('GET', url, true)
    		xhr.send()//发送
            xhr.onreadystatechange = function () {
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                        console.log(xhr.responseText);
                    } else {
                        throw 'error'
                    }
                }
            }
        }
```

```js
//post
function post(url, data) {
            const xhr = new XMLHttpRequest() //创建实例
            xhr.open('POST', url, true)
            xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
            xhr.send(`username=${data}`)
            xhr.onreadystatechange = function () {
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                        console.log(xhr.responseText);
                    } else {
                        throw 'error'
                    }
                }
            }
        }
```



###### promise

```js
get
function promiseGet(url) {
            return new Promise((resolve, reject) => {
                const xhr = new XMLHttpRequest()
                xhr.open('GET', url, true)
                xhr.send()
                xhr.onreadystatechange=function(){
                    if(xhr.readyState===4){
                        if(xhr.status>=200&&xhr.status<300||xhr.status==304){
                            resolve(xhr.responseText)
                        }else{
                            reject('error')
                        }
                    }
                }
            })
        }
```

```js
//post
function promisePost(url, data) {
            return new Promise((resolve, reject) => {
                const xhr = new XMLHttpRequest()
                xhr.open('POST', url, true)
                xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
                xhr.send(`username=${data}`)
                xhr.onreadystatechange = function () {
                    if (xhr.readyState === 4) {
                        if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                            resolve(xhr.responseText)
                        } else {
                            reject('error')
                        }
                    }
                }
            })
        }
```





> 什么是 DOM 和 BOM？

DOM 指的是文档对象模型，它指的是把文档当做一个对象，这个对象主要定义了处理网页内容的方法和接口。
BOM 指的是浏览器对象模型，它指的是把浏览器当做一个对象来对待，这个对象主要定义了与浏览器进行交互的法和接口。BOM的核心是 window，而 window 对象具有双重角色，它既是通过 js 访问浏览器窗口的一个接口，又是一个 Global（全局）对象。这意味着在网页中定义的任何对象，变量和函数，都作为全局对象的一个属性或者方法存在。window 对象含有 location 对象、navigator 对象、screen 对象等子对象，并且 DOM 的最根本的对象 document 对象也是 BOM 的 window 对象的子对象。

