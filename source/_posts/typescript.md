---
title: typescript
date: 2022-05-03 16:47:18
tags: typescript
---

#### 基础知识点

###### 接口

> * 接口定义只读属性readonly
>
> readonly:变量作为属性
>
> const:变量使用时

```ts
interface Point{
    readonly x:number
    readonly y:number
}
let poin:Point={x:1,y:3}
poin.x=3//报错
```

> * 接口函数类型

```ts
interface SearchFunc{
    (source:string ,subString:string):boolean;
}
```

> 可索引类型接口
>
> * 共支持两种索引签名:字符串数字
> * 索引签名是type script里的一个语法糖
> * 所谓索引，就是根据一定的指向返回相应的值
> * 比如数组的索引就是下标0,1,2....
> * type script里的索引签名有两种：数字索引和字符串索引(数字索引是字符串的子类型,数组中数字当作字符串)

```ts
interface StringArray{
    [index:number]:string
}

let arr:StringArray=['a','b','c']
```

> * 实现接口

```ts
interface Point {
    currentTime: Date
    setTime(d:Date)
}
class Clock implements CalockInterface {
    currentTime: Date
    setTime(d:Date){
        this.currentTime=d
    }
    constructor(h:number){}
}//类描述公有部分
```

> * 接口继承

```ts
interface Shape {
    color:string
}
interface Square extends Shape {
    sideLength:number
}
let square:Square ={
    color:'rea',
    sideLength:100
}
```

> * 混合类型

```ts
interface Counter {
    (start:number):string
   	sinterval:number
    reset():void
}
function getCounter:Counter{
    let counter=<Counter>function (start:number){}///将let的counter断言为Counter
    counter.interval=123
    counter.reset=function (){}
    return Counter
}
```

###### 类class(可以当作接口)

> * 关键字public都能访问,, protected内部和类能访问, private ,内部能访问

```ts
///抽象类
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // 在派生类的构造函数中必须调用 super()
    }

    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // 允许创建一个对抽象类型的引用
department = new Department(); // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
department.generateReports(); // 错误: 方法在声明的抽象类中不存在
```

###### 映射类型

```ts
type Props={a:number,b:number,c:number}

type Type={
    [key in keyof Props]:boolean
}
//
type Type = {
    a: boolean;
    b: boolean;
    c: boolean;
}
```





























































































































































