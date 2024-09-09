---
title: "let 还是 var ?"
pubDate: 2021-04-01
description: "let 还是 var ?"
categories: ["Javascript"]
---

es6 的 let 和我们之前一直使用的 var 到底有什么不同呢？

<!--more-->

---

let 为 ES6 新增的用来声明变量的命令，那么是否可以用 let 来全面替代 var 呢。我个人认为是可以的，而且在不考虑兼容方面的一些环境因素下我个人是极力推荐的。以下我就说说 let 的特点和一些与 var 的区别。

### let 不会被提前注册

```js
console.log(`var_a:`, var_a); //结果 → var_a: undefined
console.log(`let_a`, let_a); //结果直接报错 → let_a is not defined
var var_a = 1;
let let_a = 1;
```

var 声明的变量在页面解析时会被提前声明但并未赋值，所以打印出来为 undefind，但是 let 不会被提前声明直接报错。

### let 的块级作用域

在 let 未出世之前 js 只有函数作用域和全局作用域缺乏灵活性例如

```js
{
  var i = 1;
}
console.log(i); // 1
```

换成 es6 的 let 在 **{}** 中的都是 let 的块级作用域范围

```js
{
  let i = 1;
  console.log(i); // 1
}
console.log(i); // i is not defined
```

也许你觉得在正常书写代码的时候无伤大雅，但是在一些时候你会觉得 let 简直是天使。

```js
for (var i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  });
}

// 10 10 10 10 10 10 10 10 10 10
```

```js
for (let i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  });
}

// 0 1 2 3 4 5 6 7 8 9
```

看到上面的两段代码比较是不是感觉 let 还是很好用的呢。每次 for 循环的块级作用域都是不同的所以在每次循环中 let 声明的变量也是独一无二的不受外界影响。

### let 不可以重复声明

var 允许被多次声明覆盖这导致可能会出现维护大型代码的时候大意变量重名的现象，而且可覆盖导致出现各种 bug。

```js
var a = 1;
var a = 2;
console.log(a); //2
```

但是在 let 中重复声明是不被允许的，你敢重复声明变量，它就敢跟你报错

```js
let a = 1;
let a = 2;
//报错 Identifier 'a' has already been declared
```
