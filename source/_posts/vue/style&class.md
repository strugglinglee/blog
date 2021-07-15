---
title: vue-class与style绑定
date: 2020-06-14 20:33:06
tags:
- vue
categories:
- 进阶
- vue
banner_img: https://pic4.zhimg.com/80/v2-deb9de10f2504896d6574f8f16c8bd6e_1440w.jpg
index_img: https://pic4.zhimg.com/80/v2-deb9de10f2504896d6574f8f16c8bd6e_1440w.jpg
---


## vue-class与style绑定
### class
#### 对象语法
- 普通写法
```
<div v-bind:class="{ active: isActive }"></div>
```
- 多字段控制
```
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```
- 绑定的数据对象不必内联定义在模板里
```
//js-data
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
//js-计算属性
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
<!-- html -->
<div v-bind:class="classObject"></div>
```

#### 数组语法
- 普通用法
```
//js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
<!-- html -->
<div v-bind:class="[activeClass, errorClass]"></div>
```
- **`三元表达式`**
```
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```
- **`在数组语法中也可以使用对象语法`**
```
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

#### 用在组件上
- 当在一个自定义组件上使用 class property 时，这些 class 将被添加到该组件的根元素上面
- 这个元素上已经存在的 class 不会被覆盖。

### 绑定内联样式
#### 对象语法
- 普通用法
```
data: {
  activeColor: 'red',
  fontSize: 30
}
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
- 直接绑定到一个样式对象通常更好,模版更清晰
```
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
<div v-bind:style="styleObject"></div>
```

#### 数组语法
- v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上
```
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

#### 多重值
```
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```