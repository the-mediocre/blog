---
title: typescript_experise
date: 2022-05-03 21:52:45
tags: [typescript 练习]
---

# [type-challenges](https://github.com/type-challenges)

1. **00013-warm-hello-world**

```ts
type HelloWorld = string
```

2. **00004-easy-pick**

```ts
//联合类型:type union='title'|'title'
//遍历联合类型union=>    [key in k]
type MyPick<T, k extends keyof T> ={
  [key in k]:T[key]
}
```

3. **00007-easy-readonly**

```ts
//遍历接口用 keyof
type MyReadonly<T> = {
 readonly [k in keyof T]:T[k]
} 
```

4. **00011-easy-tuple-to-object**

```ts
//遍历数组用in T[number]
type TupleToObject <T extends readonly any[]> ={
   [k in  T[number]]: k
}
```

5. **00014-easy-first**

```ts
//判断空数组//never和T[0]取某一项
type First<T extends any[]> = 
T extends []?never:T[0]
//继承[]进行判断
T['length'] extends 0?never:T[0]
//数组的length属性判断是否为空
T[0] extends T[number]?T[0]:never
//如果为空,T[0]为undefined,T[number]为never
T[number] extends never?never:T[0]
//直接用T[number]进行判断
T extends[infer First,...infer Rest]?First:never
//infer表示推断,理解为设一个变量//看看能不能解构出First,进行判断
```

6. **00018-easy-tuple-length**

```ts
//对于传入变量的约束,以及length属性//typeof数组之后为只读类型,他只能分配给只读类型readonly
type Length<T extends readonly any[]> = T["length"]
```

7. **00043-easy-exclude**

```ts
//T和U是分布式的,对比继承会进行比较,(T是否在U中)一样的放在A里面,不一样的放在B里面,,最后返回的还是他俩同时的
type MyExclude<T, U> =T extends U?never:T
```

8. **00189-easy-awaited**

```ts
//infer进行设变量..联想数学等式//递归判断然后是需要类型限制,在进行判断
type MyAwaited<T extends Promise<unknown>> =T extends Promise<infer U>
?U extends Promise<unknown>
  ?MyAwaited<U>
  :U
:never
//限制类型之后进来判断的不属于promise就是返回值
```

9. **00268-easy-if**

```ts
//判断加类型限制
type If<C extends boolean, T, F> = C extends true?T:F
```

10. **00533-easy-concat**

```ts
//当成数组然后拓展运算符
type Concat<T extends Array<unknown>, U extends Array<unknown>> =[...T,...U]
```

11. **00898-easy-includes**

```ts
```



