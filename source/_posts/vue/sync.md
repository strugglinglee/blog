---
title: vue2.X自定义组件v-model和.sync修饰符的深入理解
date: 2020-06-07 16:30:06
tags:
- vue
categories:
- 进阶
- vue
banner_img: https://pic1.zhimg.com/80/v2-fd6dfd69ca93171e109e1c71892dce40_1440w.jpg
index_img: https://pic1.zhimg.com/80/v2-fd6dfd69ca93171e109e1c71892dce40_1440w.jpg
---

## v-model在自定义组件中的应用
### v-model的作用
- 前提是props是单向数据流，我们不能直接在子组件去改变父组件传过来的props，v-model是可以方便我们来改变该值；
- 无需在父组件去监听函数去改变v-model改变的值；
- 父组件仍可以监听发送的事件；
- 比较适用于表单值修改比较频繁的时候；

### v-model使用方法
- 在子组件中定义model和props
- 通过发送在model下定义的事件名通知v-model绑定值的改变

```
  model: {
    prop: 'value',
    event: 'change'//自定义改变值的事件，之后我们可以通过发送该事件通知v-model绑定值的改变
  },
  props: {
    /**
     * 价格设置值
     * @model
     */
    value: {
      type: Number,
      default: 0
    }
  },
  methods:{
    change (value) {
      //这样可以直接将父组件v-model绑定的值改变
      this.$emit('change', value * 1)
    }
  }
```

## .sync修饰符的实际应用
### .sync的作用
- 通常我们需要通过事件机制，子组件发送一个事件，父组件监听修改值
- .sync可以省去监听这一步

### .sync的用法
- 在父组件传props的时候后面添加一个.sync修饰符
- 通过在子组件中发送"update:prop"事件直接更新prop


```
//子组件
this.$emit('update:prop', prop)
//父组件
<div :prop.sync="prop" />
//可以实现prop的联动
```