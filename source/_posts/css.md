---
title: css
date: 2022-04-12 19:13:49
tags: css
categories: [面试,css]
---

##### 盒模型: 内容宽度是否含有border,padding>>>box-sizing:border-box



###### 权重: important(比内联样式高)>>>id>class>tag   (10)



###### 新增伪类:first-of-type,,,,,,,



###### 居中元素



###### display:inline/none/block/table/inline-block



###### position:static(默认)/relative/                           absolute/fixed(脱离文档流)





###### css3特性:

1. 透明度
2. word-wrap不可分割的单词换行
3. 文字阴影:text-shadow
4. border-radius
5. box-shadow
6. 媒体查询



###### 用css画三角形:

1. 宽高为零

2. border设长度透明度(transparent),某一个设置颜色

   ```css
   div {
               width: 0;
               height: 0;
               border-top: 40px solid transparent;
               border-left: 40px solid black;
               border-right: 40px solid transparent;
               border-bottom: 40px solid transparent;
           }
   ```

   

###### display:none(回流+重绘)不保留空间     与visbility:hedden(重绘)保留空间

重绘:一部分改变不影响其他的

重排(回流): 其他的被影响



###### BFC:块级格式上下文;(BFC是一个完全独立的空间（布局环境），让空间里的子元素不会影响到外面的布局)

1. float脱离文档流高度塌陷
2. 外边距重叠

形成BFC条件:

* 浮动元素
* 绝对定位元素absolute/fixed
* display:inline-blox
* 块级元素  overflow

```css
/*添加类名*/
.bfc {
    overflow: hedden
}
```



###### 清楚浮动:   父元素高度无法被撑开//同级元素跟在后面      浮动后的元素是display: block

1. 在最底下创建块级元素clear:both

2. 父元素bfc

3. 伪元素

   ```CSS
   .box::after {
       content:'';
       display: block;
       clear: both
   }
   ```







圣杯布局

```css
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        .header,
        .footer {
            height: 100px;
            background-color: pink;
        }

        .main {
            height: 100%;
            padding: 0 140px 0 100px;
        }
      /*将所有的都左浮动;左边的position relative/右边的*/
        .middle {
            float: left;
            width: 100%;
            background-color: cadetblue;
        }
        .left {
            float: left;
            background-color: darkblue;
            width: 100px;
            margin-left: -100%;
            position: relative;
            left: -100px;
        }

        .right {
            margin-right: -140px;
            float: left;
            background-color: chocolate;
            width: 140px;
        }
        .main::after{
            display: block;
            content: '';
            clear: both;
        }
    </style>
</head>

<body>
    <div class="header">header</div>
    <div class="main">
        <div class="middle">middle</div>
        <div class="left">left</div>
        <div class="right">right</div>
    </div>
    <div class="footer">footer</div>

</body>
```





双飞翼

```css
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        /* .wrap {
            min-width: 600px;
        } */

        #header,
        #footer {
            height: 50px;
            width: 100%;
            background: grey;
        }

        #left,
        #right {
            width: 200px;
            height: 200px;
            background: green;
        }

        #middle {
            background: pink;
            width: 100%;
            float: left;
            height: 200px;
        }

        #content {
            overflow: hidden;
        }

        #left {
            float: left;
            margin-left: -100%;
        }

        #right {
            float: left;
            margin-left: -200px;
        }

        .middle-inner {
            margin: 0 200px;
        }
    </style>
</head>

<body>
    <div class="wrap">
        <div id="header">header</div>
        <div id="content">
            <div id="middle">
                <div class="middle-inner">
                    middle
                </div>
            </div>
            <div id="left">left</div>
            <div id="right">right</div>
        </div>
        <div id="footer">footer</div>
    </div>
</body>
```

left、center、right三种都设置左浮动
设置center宽度为100%
设置负边距，left设置负边距为100%，right设置负边距为自身宽度
设置content的margin值为左右两个侧栏留出空间，margin值大小为left和right宽度









| 值          | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| flex-grow   | 一个数字，规定项目将相对于其他灵活的项目进行扩展的量。       |
| flex-shrink | 一个数字，规定项目将相对于其他灵活的项目进行收缩的量。       |
| flex-basis  | 项目的长度。合法值："auto"、"inherit" 或一个后跟 "%"、"px"、"em" 或任何其他长度单位的数字。 |
| auto        | 与 1 1 auto 相同。                                           |
| none        | 与 0 0 auto 相同。                                           |



* 对齐方向:	flex-direction: row|       row-reverse|          column|          column-reverse|                    initial|inherit

* 对齐方向和是否换行缩写:  flex-flow: *flex-direction* *flex-wrap*|initial|inherit;
  	
  * justify-content属性定义了项目在主轴上的对齐方式。
    justify-content: flex-start | flex-end | center | space-between | space-around;
  * align-items属性定义项目在交叉轴上如何对齐。
    align-items: flex-start | flex-end | center | baseline | stretch;
  * align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch

* padding单位为%,是基于父元素的百分比填充



##### 垂直居中

```css
.content {
            position: relative;
            width: 400px;
            height: 400px;
            background-color: pink;
        }
        .center{
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translateX(-50%) translateY(-50%);
        }
```

```css
 .content {
            position: relative;
            width: 400px;
            height: 400px;
            background-color: pink;
        }

        .center {
            position: absolute;
            background-color: greenyellow;
            width: 100px;
            height: 100px;
            top: 0;
            bottom: 0;
            right: 0;
            left: 0;
            margin: auto;
        }
```

```css
.content {
            display: flex;
            width: 400px;
            height: 400px;
            background-color: pink;
            justify-content: center;
            align-items: center;
        }
        .center {
            background-color: greenyellow;
            width: 100px;
            height: 100px;
        }
```

一行文字居中,多行文字左对齐

```css
div {
    width: 80vw;
    text-align: center;
    background-color:pink;
}
p {
    width: auto;
    display: inline-block;
    text-align: left;
}
```







