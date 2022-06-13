---
title: axios
date: 2022-04-09 22:38:13
tags: [axios,webpack,node]
categories: [面试]
---

##### 配套使用json-server [官网](https://www.npmjs.com/package/json-server)

1. 安装npm install -g json-server

2. json-server --watch db.json

3. 例子

   ```js
   axios({
       method: 'get',
       url: 'http://localhost:3000/posts/2'})
       .then(v => console.log(v))
         
   axios.get('http://localhost:3000/posts/2')
       .then(v => console.log(v))
   ```

   

4. 创建实例

   ```js
   const instance=axios.create({
               baseURL:'https://autumnfish.cn/api/',//大写
               tiemout:2000
           })
   ```

   

5. 请求拦截器响应拦截器,多个的话,请求后进先执行,响应相反,config配置对象

   


##### webpack

yarn init

yarn add webpack webpack-cli -D

新建文件夹src----index.js      build

vue中的eslint检查

lintOnSave: false

