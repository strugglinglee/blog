---
title: vue3.0发布后的一点总结
date: 2022-08-19 19:22:00
tags:
- vue
- vue3.0
- 响应式原理
categories:
- vue3源码学习
---

## 前言

上一次我们讨论了[Vue2.x的响应式原理](https://juejin.cn/post/7125379985946181668)。
主要的步骤是遍历对象每个property，通过Object.defineProperty()方法进行数据劫持，给每一个property加上getter/setter，getter进行依赖收集，setter中通知订阅者执行副作用。

### Vue2.x Object.defineProperty()的不足

主要有以下：

1.  无法检测属性的添加和删除；

2.  无法检测数组索引和长度的变更

3.  不支持 Map、Set、WeakMap 和 WeakSet。

### 为什么vue3.x要使用Proxy进行数据劫持？

1.  Proxy 可以直接监听对象和数组的变化，有多达 13 种拦截方法，这是 Object.defineProperty 不具备的；
2.  Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；

3.  Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利；

## 知识补充

> 本篇文章主要介绍vue3.0响应式原理，涉及一些es6+的知识，在此做一些简单的补充，想深入了解的知识可以再看完本篇文章之后再继续研究。
>
> 1.  https://es6.ruanyifeng.com/#docs/reflect
> 2.  https://es6.ruanyifeng.com/#docs/proxy
> 3.  https://es6.ruanyifeng.com/#docs/set-map

### Reflect

简单来说，`Reflect`就是`Object`的未来形式。

之前将在`Object`对象的一些明显属于语言内部的方法（比如`defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上。

```
// old
Object.defineProperty(target, property, attributes);
// new
Reflect.defineProperty(target, property, attributes)
```

#### Reflect.get(target, name, receiver)

获取属性值的方法，target表示对象，name是访问的属性名。

> receiver中接收对象，访问`proxy`对象会优先访问receiver，以下的receiver均为此概念。

```
var myObject = {
  foo: 1
}
Reflect.get(myObject, 'foo') // 1
```

#### Reflect.set(target, name, value, receiver)

设置属性值的方法，target表示对象，name是要设置的属性名，value是要设置的值。

```
var myObject = {
  foo: 1
}
myObject.foo // 1

Reflect.set(myObject, 'foo', 2);
myObject.foo // 2
```

### Proxy

Proxy 可以理解成在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。

比如，访问以下obj中的某个属性，无论存不存在，都会触发get函数；如果设置以下obj的某个属性，就会触发set函数。

> **注意**：proxy只能代理一层，如果某个属性值仍然是对象，这个对象内部的改变是无法监测到的。

> target:代理的对象、 propKey：访问的属性名、value：属性值

```
var obj = new Proxy({}, {
    get: function (target, propKey, receiver) {
            return Reflect.get(target, propKey, receiver);
         },
    set: function (target, propKey, value, receiver) {
            return Reflect.set(target, propKey, value, receiver);
         }
});
```

### WeakMap

`WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合。

#### `WeakMap`与`Map`的区别

1.  `WeakMap`只接受对象作为键名（`null`除外）

<!---->

2.  不接受其他类型的值作为键名。

#### 使用WeakMap的优点

`WeakMap`的专用场合就是，它的键所对应的对象，可能会在将来消失。`WeakMap`结构有助于防止内存泄漏。

## 简单理解：什么是响应式？

> 以下例子来源于[vue官网-深入响应式系统](https://cn.vuejs.org/guide/extras/reactivity-in-depth.html#what-is-reactivity)

在JS中，我们无法简单地通过表达式来创建一个值与另外的值的关联

```
let A0 = 1
let A1 = 2
let A2 = A0 + A1

console.log(A2) // 3

A0 = 2
console.log(A2) // 仍然是 3
```

当一个值依赖的值变化时，我们需要通过一个update方法来更新这个值

```
let A2
function update() {
  A2 = A0 + A1
}
// 手动触发
A0 = 2 
update()
```

这里我们刚好通过这个例子解释下我们常说的一些名词：

### **副作用**

update函数里面执行的代码就是副作用，或者更可以简称为作用，其可以改变程序中的状态。

### **依赖**

这个副作用改变程序里的状态是依赖于A0或A1的改变，所以A0和A1就是这个副作用的依赖

### **订阅者**

A2订阅了A0和A1的改变，在这里就A2相当于是一个订阅者


## reactive建立响应式全过程

以上的例子，当依赖改变的时候我们需要手动触发副作用，当依赖渐渐变多或者更多的改变时我们将难以进行追踪，所以在vue中，我们需要一种响应性系统可以自动跟踪依赖关系。

首先，我们来了解下reactive是如何建立响应式系统的。
### 通过reactive建立proxy代理

以下写法是我们vue3.x中的常见写法，我们会在reactive函数中传入一个对象，接下来我们需要探索在这个函数中发生了什么

``` js
// 第一步：通过reactive建立proxy代理
const state = reactive({
  a: 1,
  b: 2,
});
```

### proxy代理

#### get：

当访问对象中的某个属性的时候，会触发track函数，这个track函数是用来收集依赖的，也就是收集被访问的key相关的副作用的，这个函数后面再深入介绍。

#### set：

当对象中某个属性值发生变化的时候，就会触发trigger函数，这个函数主要是用来触发每个依赖对应副作用的执行，我们也会在后面深入介绍这个函数。

```
const reactive = (target) => {
  const handler = {
    get(target, key, receiver) {
      // 相当于 target[key]
      const result = Reflect.get(target, key, receiver);
      // 收集依赖（通过key来收集依赖对应的副作用）
      track(target, key);
      return result;
    },
    set(target, key, value, receiver) {
      const oldValue = target[key];
      // target[key]=value
      const result = Reflect.set(target, key, value, receiver);
      if (oldValue != result) {
        // 触发每个依赖对应的副作用的执行
        trigger(target, key);
      }
      return result;
    },
  };

  return new Proxy(target, handler);
};
```

### Track

#### 原理

音译为跟踪，追踪。我们可以理解为一个追踪依赖相关的所有副作用集合的过程。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff19a33c439640ceaea2bdfc4f985db6~tplv-k3u1fbpfcp-watermark.image?)

我们可以通过上图看到我们收集effect的中还要根据对象，以及对象的属性来进行分类，并不是简单的一层收集，这样收集的好处是，后期某个属性值发生改变的时候，我们就可以根据这个层级一级一级的找出它对应的副作用了。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6566c8e9622349cc9a9c3c6e00ab7e7f~tplv-k3u1fbpfcp-watermark.image?)

#### 代码实现

##### Effect

首先我们需要有一个effect方法来书写副作用，也就类似于我们前文提到的update函数。

``` js
const { reactive, effect } = require ( './reactivity.js' )

const state = reactive ({  a : 1 ,  b : 2  })

let c = 0  
effect(() => {
    c = state.a + state.b 
})
```

接下来我们一起看看触发effect函数的时候究竟发生了什么。

首先，你访问了state.a和 state.b，自然就会触发get中的track函数进行依赖追踪。

过程中，第一步就是检测此时有没有正在执行的副作用，也就是activeEffect。

> **这里稍微解释下activeEffect存在的必要性：**
> 你要知道不管是在effect方法中，还是在effect外访问对象属性都会触发track函数。
> 因为我们是收集依赖对应的副作用，activeEffect是为了确认是在effect方法中访问对象属性。

剩下的步骤可以对照原理的图一一对应，就是收集以对象为维度，收集依赖对应的副作用的过程。

##### track主要代码


```js
const track = (target, key) => {
    if (!activeEffect) return
    let depsMap = targetMap.get(target);
    if (!depsMap) {
        targetMap.set(target, (depsMap = new Map()));
    }
    let dep = depsMap.get(key);
    if (!dep) {
        depsMap.set(key, (dep = new Set()));
    }
    // 添加 activeEffect 依赖
    dep.add(activeEffect);
    console.log('targetMap',targetMap)
}
```

##### activeEffect

前文已经说明了activeEffect存在的必要性，这个过程很巧妙，在这里我稍微梳理下流程。

首先我们都知道js是单线程的脚本语言，我们调用effect方法时会按照以下步骤执行：

1.  将副作用赋值给 activeEffect
2.  执行 activeEffect，这个过程中如果访问了对象属性，就会触发track进行依赖收集
3.  重置 activeEffect为null

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/236e5b929d454be683076315d6e408c6~tplv-k3u1fbpfcp-watermark.image?)
``` js
// reactivity.js
// 存储正在执行的副作用
let activeEffect = null; // 引入 activeEffect 变量

const effect = eff => {
    // 1. 将副作用赋值给 activeEffect
    activeEffect = eff;
    // 2. 执行 activeEffect
    activeEffect();
    // 3. 重置 activeEffect
    activeEffect = null;
}

```

### Trigger

#### 原理

实际上就是递归遍历targetMap的过程，根据target和key找出改变的属性有无对应的副作用，如果有的话就执行。

1.  target对应的属性变化，触发set中的trigger

2.  寻找targetMap有无target对应的depsMap

3.  寻找depsMap中有无key对应的dep

4.  如果有dep,就依次执行dep中存储的副作用

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d422a3c777547cfa2b46bda2ae0ed11~tplv-k3u1fbpfcp-watermark.image?)

#### 代码实现

``` js
const trigger = (target, key) => {
    const depsMap = targetMap.get(target);
    if (!depsMap) return;
    let dep = depsMap.get(key);
    if (dep) {
        dep.forEach(effect => effect());
    }
};
```

### 注意

因为proxy只能代理一层，所以如果属性值对应的依旧是一个对象，那需要把这个对象也包装为一个响应式对象。

```
const reactive = (target) => {
    const handler = {
        get(target, key, receiver) {
            const result = Reflect.get(target, key, receiver);
            // 收集依赖对应的副作用
            track(target, key);
            /**
             * 因为proxy只能代理一层，所以
             * 如果属性值对应的依旧是一个对象，那需要把这个对象也包装为一个响应式对象
             */
            if (isObject(result)) return reactive(result);
            return result;
        },
        set(target, key, value, receiver) {
            const result = Reflect.set(target, key, value, receiver);
            // 触发依赖对应副作用
            trigger(target, key);
            return result;
        }
    }

    return new Proxy(target, handler);
}
```

``` js
const state = reactive({
    e: {
        f: 1
    }
})
effect(() => {
    c = state.e.f
    console.log(c)//执行两次 第一次1 第二次2
})
state.e.f = 2
```

### 线上地址

全文涉及的代码都上传到了git仓库，欢迎fork、star，提issue~

https://github.com/strugglinglee/reactivity.git

### 代码调试

代码调试可以让我们更清楚的了解其中的过程，如有需要请参考这篇文章。

https://juejin.cn/post/7030584939020042254

## 后记

看过很多文章，包括自己写过的水文笔记，有的时候看完还不如不看，对知识的了解更加模糊，赶紧关闭寻找下一篇。

自己很佩服的一位大佬就是阮一峰，他写的技术文给我帮助很大，由浅入深，让你很容易的就接受了新的知识，换句话说，就是在写你我都能够看得懂的文章。

文章写出来就是给人看的，好的文章是值得反复观看推敲的，所以近期定下自己的目标：输出我们都能看得懂的文章，把艰涩的文字变成跳动的语言。

创作不易，如果你觉得不错，欢迎点赞收藏，有什么问题也可以评论交流讨论。最后，非常感谢你的阅读！

## 参考

1.  [探索 Vue3 响应式原理](https://juejin.cn/post/6977513378256257031)
2.  [vue官网-深入响应式系统](https://cn.vuejs.org/guide/extras/reactivity-in-depth.html#what-is-reactivity)
3. [Vue响应式原理探究之“发布-订阅”模式](https://juejin.cn/post/7125379985946181668)