---
title: "JS数组的四种循环"
pubDate: 2021-03-26
description: "js数组的四种循环：forEach、map、filter和reduce"
categories: ["Javascript"]
---

js数组的四种循环：forEach、map、filter和reduce


---

### forEach

forEach没有返回值。我们在回调函数中直接修改arr的值。

```JavaScript
let arr = [1, 2, 3, 4, 5];
let result = arr.forEach((item, index) => {
  return (arr[index] = item * 2);
});
console.log(result); 
console.log(arr); 
```
执行返回结果
```
undefined
[2, 4, 6, 8, 10]
```
### map
map 会返回一个新的数组，并且执行的效率会比forEach高
```JavaScript
let arr = [1, 2, 3, 4, 5];
let result = arr.map((item, index) => {
  return item * 2;
});
console.log(result);
```
执行返回结果
```JavaScript
[2, 4, 6, 8, 10]
```
### filter
filter会在循环中`return true || false`来生成我们想要的新数组，**没有return默认为false**
```JavaScript
let arr = [1, 2, 3, 4, 5];
let result = arr.filter((item, index) => {
  return item > 3
});
console.log(result);
```
执行返回结果
```
[4, 5]
```
### reduce
reduce循环也有两个参数，两个参数分别代表上一次`return`的结果和数组的当前值。
```JavaScript
let arr = [1, 2, 3, 4, 5];
let result = arr.reduce((pre, cur) => {
    console.log(pre,cur);
    return pre + cur;
});
console.log(`result：`, result)
```
执行返回结果
```
1 2
3 3
6 4
10 5
result 15
```