---
title: 数组方法整理
date: 2020-09-07 16:26:51
tags:
- Array
categories:
- 基础巩固
- js
---

### 构造函数

Array是 JavaScript 的原生对象，同时也是一个构造函数，可以用它生成新的数组
```
const arr = new Array(2); // 构造函数生成数组
arr.length // 2
arr // [ empty x 2 ]
```
##### 创建数组，直接使用数组字面量是更好的做法。
```
let arr = [1,3,44] // 字面量生成数组
```
> Array作为构造函数，行为很不一致。因此，不建议使用它生成新数组，直接使用数组字面量是更好的做法。

### 静态方法

#### Array.isArray()
返回一个布尔值，表示参数是否为数组，可以弥补typeof运算符的不足

### 实例方法

#### valueOf(),toString()

- 所有对象都拥有的方法,数组的valueOf方法返回数组本身
- toString方法返回数组的字符串形式
```
var arr = [1, 2, 3];
arr.toString() // "1,2,3"

var arr = [1, 2, 3, [4, 5, 6]];
arr.toString() // "1,2,3,4,5,6"
```

#### push()，pop()

- push方法用于在数组的末端添加一个或多个元素，返回添加新元素后的数组长度，该方法会改变原数组
- pop方法用于删除数组的最后一个元素，并返回该元素。注意，该方法会改变原数组。

#### unshift() ，shift()

- unshift()方法用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组。
- shift()方法用于删除数组的第一个元素，并返回该元素。注意，该方法会改变原数组。

#### join()

```
var a = [1, 2, 3, 4];

a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"
```

- 如果数组成员是undefined或null或空位，会被转成空字符串。

- 巧用call方法：以指定参数作为分隔符，将所有数组成员连接为一个字符串返回，可以用于字符串或类似数组的对象。

```
Array.prototype.join.call('hello', '-')
// "h-e-l-l-o"

var obj = { 0: 'a', 1: 'b', length: 2 };
Array.prototype.join.call(obj, '-')
// 'a-b'
```

#### concat()

- concat方法用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，原数组不变。
- 除了数组作为参数，concat也接受其他类型的值作为参数，添加到目标数组尾部。

```
[1, 2, 3].concat(4, 5, 6)
// [1, 2, 3, 4, 5, 6]
```

#### reverse()

- 用于颠倒排列数组元素，返回改变后的数组。
- 注意，该方法将改变原数组。

#### slice()

- 用于提取目标数组的一部分，返回一个新数组，原数组不变。
arr.slice(start, end);
- 它的第一个参数为起始位置（从0开始），第二个参数为终止位置（但该位置的元素本身不包括在内）。如果省略第二个参数，则一直返回到原数组的最后一个成员。
- 如果slice方法的参数是负数，则表示倒数计算的位置

```
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
// -2表示倒数计算的第二个位置，-1表示倒数计算的第一个位置。
```

- 如果第一个参数大于等于数组长度，或者第二个参数小于第一个参数，则返回空数组

##### 重要应用:将类似数组的对象转为真正的数组。
```
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```
> 上面代码的参数都不是数组，但是通过call方法，在它们上面调用slice方法，就可以把它们转为真正的数组

#### splice()

用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素。注意，该方法会改变原数组。
```
arr.splice(start, count, addElement1, addElement2, ...);
```
splice的第一个参数是删除的起始位置（从0开始），第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。

##### 一般用法

```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2) // ["e", "f"]
a // ["a", "b", "c", "d"]
```
**起始位置如果是负数，就表示从倒数位置开始删除**

```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(-4, 2) // ["c", "d"]
````

**单纯地插入元素，splice方法的第二个参数可以设为0。**

**如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。**

#### sort()
sort方法对数组成员进行排序，默认是按照字典顺序排序。排序后，原数组将被改变。
```
['d', 'c', 'b', 'a'].sort()
// ['a', 'b', 'c', 'd']

[4, 3, 2, 1].sort()
// [1, 2, 3, 4]

[11, 101].sort()
// [101, 11]

[10111, 1101, 111].sort()
// [10111, 1101, 111]
```
##### 自定义方式排序
```
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]
[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;
})
// [
//   { name: "李四", age: 24 },
//   { name: "王五", age: 28  },
//   { name: "张三", age: 30 }
// ]
```

##### 从小到大

```
[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o2.age - o1.age;
})
```

##### 从大到小
```
[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o2.age - o1.age;
})
```
##### 乱序
```
var arr = [1,2,3,4,5,6,7,8,9,],

　　r = arr.sort(function(){

　　return Math.random() > .5 ? -1:1;

});
```
#### map()
- 将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回
- map方法接受一个函数作为参数。该函数调用时，map方法向它传入三个参数：当前成员、当前位置和数组本身
```
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```
- map方法还可以接受第二个参数，用来绑定回调函数内部的this变量
- 如果数组有空位，map方法的回调函数在这个位置不会执行，会跳过数组的空位
```
var f = function (n) { return 'a' };
[1, undefined, 2].map(f) // ["a", "a", "a"]
[1, null, 2].map(f) // ["a", "a", "a"]
[1, , 2].map(f) // ["a", , "a"]
```
- map方法不会跳过undefined和null，但是会跳过空位

#### forEach()
forEach方法不返回值，只用来操作数据

如果数组遍历的目的是为了得到返回值，那么使用map方法，否则使用forEach方法。
- forEach的用法与map方法一致，参数是一个函数，该函数同样接受三个参数：当前值、当前位置、整个数组
- forEach方法也可以接受第二个参数，绑定参数函数的this变量
- forEach方法也会跳过数组的空位。
- 不会跳过undefined和null，但是会跳过空位

#### filter()
- 用于过滤数组成员，满足条件的成员组成一个新数组返回
```
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```
- filter方法的参数函数可以接受三个参数：当前成员，当前位置和整个数组
```
[1, 2, 3, 4, 5].filter(function (elem, index, arr) {
  return index % 2 === 0;
});
// [1, 3, 5]
```
- 还可以接受第二个参数，用来绑定参数函数内部的this变量

#### some(),every()
- some方法是只要一个成员的返回值是true，则整个some方法的返回值就是true，否则返回false。
- every方法是所有成员的返回值都是true，整个every方法才返回true，否则返回false。

#### reduce(),reduceRight()
reduce是从左到右处理（从第一个成员到最后一个成员）
```
[1, 2, 3, 4, 5].reduce(function (a, b) {
  console.log(a, b);
  return a + b;
})
// 1 2
// 3 3
// 6 4
// 10 5
//最后结果：15
```
##### 参数
1. 累积变量，默认为数组的第一个成员
2. 当前变量，默认为数组的第二个成员
3. 当前位置（从0开始）
4. 原数组
```
// 如果要对累积变量指定初值，可以把它放在reduce方法和reduceRight方法的第二个参数。
[1, 2, 3, 4, 5].reduce(function (a, b) {
  return a + b;
}, 10);
// 25
```
##### reduceRight则是从右到左（从最后一个成员到第一个成员），其他完全一样。
```
function subtract(prev, cur) {
  return prev - cur;
}

[3, 2, 1].reduce(subtract) // 0
[3, 2, 1].reduceRight(subtract) // -4
```
> reduce方法相当于3减去2再减去1，reduceRight方法相当于1减去2再减去3。

##### 还可以做一些遍历相关的操作：遍历数组，找出字符长度最长的数组
```
function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest;
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
```
#### indexOf(),lastIndexOf()
这两个方法不能用来搜索NaN的位置，即它们无法确定数组成员是否包含NaN。

这是因为这两个方法内部，使用严格相等运算符（===）进行比较，而NaN是唯一一个不等于自身的值。
- indexOf方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1

```
var a = ['a', 'b', 'c'];
a.indexOf('b') // 1
a.indexOf('y') // -1
```

- indexOf方法还可以接受第二个参数，表示搜索的开始位置。
```
['a', 'b', 'c'].indexOf('a', 1) // -1
```
- lastIndexOf方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1。
```
var a = [2, 5, 9, 2];
a.lastIndexOf(2) // 3
a.lastIndexOf(7) // -1
```
#### 链式使用
数组方法之中，有不少返回的还是数组，所以可以链式使用
```
var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(function (email) {
  console.log(email);
});
// "tom@example.com"
```