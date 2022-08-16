---
title: vue3.0发布后的一点总结
date: 2020-09-20 16:30:06
tags:
- vue
- vue3.0
categories:
- vue
---


### 分离内部模块
- vue3.0的核心仍然是通过一个简单的`<script />`标签来使用，但其内部结构已经被重写，并解耦成一个个模块的集合
- 允许终端用户通过tree-shaking的形式将减少一半运行时的体积
> - [tree shaking](https://webpack.docschina.org/guides/tree-shaking/) ，通常用于描述移除 JavaScript 上下文中的未引用代码(dead-code)
> - 运行时[rumtime](https://webpack.docschina.org/concepts/manifest/)
- 这些模块还暴露了底层api，解锁了许多高级用法

### 引入Composition API
> [Composition API RFC传送门](https://vue-composition-api-rfc.netlify.app/zh/#%E6%A6%82%E8%BF%B0)

 #### 什么是Composition API
 - 一套全新的 API，旨在解决 Vue 在大规模应用场景中的痛点。
 - Composition API 构建于响应式 API 之上，实现了类似于 React hook 的逻辑组成与复用，相较于 2.x 基于对象的 API 方式来说，拥有更加灵活的代码组织模式，以及更为可靠的类型推断能力。

### 性能提升
#### Vue 3 与 Vue 2 相比有着显著的性能提升
- 在 **bundle 包大小**方面（tree-shaking 减少了 41%的体积）
- **初始渲染速度**方面（快了 55%）
- **更新速度**方面（快了 133%）
- **内存占用**方面（减少了 54%）

#### 编译信息虚拟 DOM
- 在 3.0 中，采取了 “编译信息虚拟 DOM” 的方式：针对模板编译器进行了优化，并生成渲染函数代码，以提升静态内容的渲染性能，为绑定类型留下运行时提示，最为重要的是，模板内部的动态节点进行了扁平化处理，以减少运行时遍历的开销。
- 因此，用户可以获得两全其美的效果，从模板中获得编译器优化后的性能，或在需要时通过手动渲染函数直接控制。

### 改进与 TypeScript 的兼容
- Vue 3 的代码库完全采用 TypeScript 编写，自动生成、测试并构建类型声明，因此它们总为最新。
- Composition API 可以很好的进行类型推断。Vetur，官方推出的 VSCode 插件，现已支持对模板表达式和 props 的类型检查
- 同时，Vue 3 已全面支持 TSX

### 为单文件组件提出了两个新特性
> `SFC（.vue文件）`

- `<script setup>`：在 SFC 内使用 Composition API 的语法糖
> 这个上文已经有提及

- `<style vars>`：在 SFC 中支持将状态作为 CSS 变量注入到样式
> - CSS本身有很多缺陷，所以才会有后面各种预处理器的出现，这些预处理器中有个非常重要的特性就是`变量`
> - 这次版本更新之前，我们更多的是直接来操作dom或者直接通过变量来控制内联样式去动态改变样式，这样处理并不优雅，也会浪费一些性能
> - 提示一下，这个新特性暂时还不兼容IE11，但是指日可待啦：）

版本更新之后我们就能在style里通过vars=""来获取我们在js中声明的变量

```
//比较简单的玩法

<template>
  <div>{{ color }}</div>
</template>

<script>
export default {
  data () {
    return {
      color: 'red'
    }
  }
}
</script>

<style vars="{ color ,..... }">
h1 {
  color: var(--color);
}
</style>

```




