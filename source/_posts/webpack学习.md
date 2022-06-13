---
title: webpack学习
date: 2022-04-25 19:44:47
tags: webpack
categories: webpack学习
---

##### 暴露语法

1. ### commonjs

```js
module.exports = function add (a, b) { return a + b; }
module.exports = {
		........
}
////////////////
const result = require('./test')
```

2. es6模块化暴露

* 分别暴露

```js
// 分别暴露//
export const a = 10
export const getData = () => { }
// 对外暴露的实质是 {a,getData}
```

```js
// 按需引入【逐个引入】
import {a,getData} from './test'
// 全部引入【统一引入】
import * as Dome from './test'
```



```js

//引入暴露
export {a,getData} from './test' 【暴露的为多个单个数据，模块化不合适】

export * as Dome from './test' 【暴露的为一个模块】

// 对外暴露的实质是 ： {Dome:{a,getData}}
```

------

* 统一暴露

```js
const a = 10
const getApp = () => { }
export {
	a,
    getApp
}
// 对外暴露的实质是 ： {a,getApp}
```

```js
// 按需引入【逐个引入】
import {a,getApp} from './test'
// 全部引入【统一引入】
import * as Dome from './test'
```

```js
export {a,getAPP} from './test' 【暴露的为多个单个数据，模块化不合适】

export * as Dome from './test' 【暴露的为一个模块，模块化常用】

// 对外暴露的实质是： {Dome:{a,getApp}}
```

* 默认暴露

```js
=========== 书写方法一 ==========
export default {
    getBpp(){
        
    },
    a:20
}
========== 书写方法二 ==============
const a = 10,
const getBpp = () => {}
export default {
    a,
    getBpp
}
// 对外暴露的实质是 ： {default:{a,getBpp}}
```

```js
// 简写形式
import Dome from './test'
// 完整形式
import {default as Dome} from './test'
```

```js
export {default as Dome} from './test'
// 暴露的实质是：{Dome:{a,getBpp}}
```

* 对外暴露出的都是一个对象，但是不同的暴露方法实际暴露的对象有所不同
* 默认并暴露的实质就是使用 export ➕ 去除import关键字后的引入写法
* 默认暴露在进行引入并暴露时不能进行简写
* import引入的文件会自动收集在文件的最上方，并按照引入的顺序执行

------

------

## webpack构建步骤(命令行)

1. npm init -y
2. npm install webpack webpack-cli --save-dve
3. npx webpack --watch(自动更新)
4. npx webpack-dev-server(npx自身项目webpack)自动页面刷新,,,为输出物理文件,将打包的文件放在了内存里面
5. 插件安装   npm install html-webpack-plugin -D
6. 浏览器自动更新     npm install webpack-dev-server -D



> npm代表下载包
>
> npx是从node_nodules中的.bin中当作环境变量进行运行
>
> npx webpack是从webpack文件中读取webpack.config.js,按照他打包

开发模式:1. 编译代码;2. 规范代码

### 基本目录

```js
module.exports = {
  // 入口
  entry: "",
  // 输出
  output: {},
  // 加载器
  module: {
    rules: [],
  },
  // 插件
  plugins: [],
  // 模式
  mode: "",
};

```



### 处理css资源

根据规则自下向上打包

先将css文件加载到common.js中再动态生成style标签

```shell
npm i css-loader style-loader -D
npm i less-loader -D
npm i sass-loader sass -D
npm i stylus-loader -D
```



```js
 {
     test: /\.css$/,//检测开发文件
     use: [
         "style-loader",//将js从CSS通过style标签添加到html文件
         "css-loader"//将CSS资源编译到commonjs的模块的js中
     ],
     
 },
 {
     test: /\.less$/,
     use: [
         // compiles Less to CSS
         'style-loader',
         'css-loader',
         'less-loader',
     ],
 },
 {
     test: /\.s[ac]ss$/,
     use: [
         // 将 JS 字符串生成为 style 节点
         'style-loader',
         // 将 CSS 转化成 CommonJS 模块
         'css-loader',
         // 将 Sass 编译成 CSS
         'sass-loader',
     ],
 },
```



### 处理图片资源

小图片转化为base64

因为图片资源会发送请求,转化为base64则是字符串,减少请求次数

```js
{
    test: /\.jpg/,
    type: 'asset',
    parser: {
        dataUrlCondition: {
            maxSize: 200 * 1024 // 小于10kb,减少发送请求次数(将他转化为了字符串)
        }
    },
    generator: {
    filename: 'static/images/[hash:10][ext][query]'//hash值生成唯一图片名称只取十位
		}
}
```

### 处理字体图标(音视频)

1. 阿里矢量图标库
2. 添加购物车
3. 创建文件
4. 下载解压
5. 打开index.html
6. 复制class名称

```js
  {
      test: /\.(ttf|woff2?|mp3|mp4|avi)$/,
      type: 'asset/resource',//不被改变为base64
      generator: {
          filename: 'static/media/[hash:10][ext][query]'
      }
  }
```



### 处理js资源eslint|bable

##### eslint:

```shell
//npm install eslint-webpack-plugin --save-dev
//npm install eslint --save-dev
```



```js
plugins: [
    new ESLintWebpackPlugin({
      // 指定检查文件的根目录
      context: path.resolve(__dirname, "src"),
    }),
```

```js
//.eslintrc.js
module.exports = {
    // 继承 Eslint 规则
    extends: ["eslint:recommended"],
    env: {
        node: true, // 启用node中全局变量
        browser: true, // 启用浏览器中全局变量
    },
    parserOptions: {
        ecmaVersion: 6,
        sourceType: "module",
    },
    rules: {
        "no-var": 2, // 不能使用 var 定义变量
    },
};
```

> .eslintignore文件,写入"dist"该文件不会被插件检查



babel

```shell
npm install -D babel-loader @babel/core @babel/preset-env
```

```js
{
    test: /\.js$/,
    exclude: /(node_modules|bower_components)/,
    use: {
        loader: 'babel-loader',
        options: {
            presets: ['@babel/preset-env']
        }
    }
}
```



### 处理html资源

```shell
npm install --save-dev html-webpack-plugin
```

```js
new HtmlWebpackPlugin({
    //找到模板
    template:path.resolve(__dirname,'public/index.html')
})
```



### 自动化打包

```shell
npm i webpack-dev-server -D
npx webpack serve
```

```js
  // 开发服务器
  devServer: {
    host: "localhost", // 启动服务器域名
    port: "3000", // 启动服务器端口号
    open: true, // 是否自动打开浏览器
  },
```



### 开发模式与生产模式

```json
"scripts": {
    "start":"npm run dev",
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev":"webpack serve --config ./config/webpack.dev.js",
    "build":"webpack --config ./config/webpack.prod.js"
},
```

> production
>
> development



### 提取css文件(MiniCssExtractPlugin)

> 传统方式是先将CSS引入到js文件中然后生成css标签,会有闪屏现象

```shell
npm install --save-dev mini-css-extract-plugin
```

```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  plugins: [new MiniCssExtractPlugin({
      filename:'static/css/main.css'
  })],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
};
```



### css兼容性处理

```shell
npm i postcss-loader postcss postcss-preset-env -D
```

> 配置写在css_loader下面,less_loader上面

```js
{
  loader: "postcss-loader",
  options: {
    postcssOptions: {
      plugins: [
        "postcss-preset-env", // 能解决大多数样式兼容性问题
      ],
    },
  },
},
```

```json
//pack.json文件
{
  // 其他省略
  "browserslist": ["last 2 version", "> 1%", "not dead"]
}
```



### 合并配置

```js
//CSS兼容性处理的时候可以写出函数复用
function getStyleLoader(pre){
    return [
        MiniCssExtractPlugin.loader,//提取CSS为单独文件
        "css-loader",//将CSS资源编译到commonjs的模块的js中
        {
            loader: "postcss-loader",
            options: {
                postcssOptions: {
                    plugins: [
                        "postcss-preset-env", // 能解决大多数样式兼容性问题
                    ],
                },
            },
        },
        pre
    ].filter(Boolean)
}
```



### CSS压缩

```shell
npm install css-minimizer-webpack-plugin --save-dev
```

```js
new CssMinimizerPlugin(),
```































































































































































































































