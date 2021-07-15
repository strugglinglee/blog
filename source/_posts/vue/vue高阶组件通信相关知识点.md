---
title: vue高阶组件相关知识点（包含$attrs/$listener/依赖注入）
date: 2020-06-14 20:28:06
tags:
- vue
categories:
- 进阶
- vue
banner_img: https://pic2.zhimg.com/80/v2-c062c4d8d223c6f487026cf535274ba3_1440w.jpg
index_img: https://pic2.zhimg.com/80/v2-c062c4d8d223c6f487026cf535274ba3_1440w.jpg
---

### vm.$attrs
#### $attrs的官方定义：
> 包含了`父作用域`中`不作为prop`被识别 (且获取) 的 attribute 绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。

了解了attrs的官方定义后我们可以提取两个比较重要的观点，即为：

##### 一、包含了`父作用域`中`不作为prop`被识别的attribute`(属性)`

- 理解：传向一个组件，但是该组件**没有相应prop定义**的attribute
    > 显式定义的prop适用于向一个子组件传入信息，然而组件库的作者并不总能预见组件会被用于怎样的场景。所以组件可以接受任意的 attribute，而这些 attribute 会被添加到这个组件的根元素上
```
<!--父组件-->
 <template>
  <div>
    <!--这里 :child1和:child2是向后代组件传递数据-->
    <child1 :child1="child1"
            :child2="child2"></child1>
  </div>
</template>

<!--子组件 child1-->
<template>
    <div class="child-1">
        <!--注意这里的$attrs是父组件传入除被props接收外的参数-->
       <p>$attrs:{{$attrs['child1']}}</p>
    </div>
</template>
```
- 如果父组件传入class，将会和子组件的class合并起来


##### 二、在创建多级组件时非常有用
> 主要在`父组件`和`孙组件`之间通信时$attrs属性非常有用

- 如果不希望组件的根元素继承attribute可以设置inheritAttrs:false
    - 这样就可以手动的决定这些attrs会被赋予在哪个元素上
    - inheritAttrs:false不会影响style/class的绑定
```
<!-- 子组件 child1,将attrs赋予给孙元素 -->
<template>
    <div class="child-1">
       <p>$attrs:{{$attrs['child1']}}</p>
       <hr>
        <!-- 孙组件中通过v-bind='$attrs'接受数据 -->
        <child2 v-bind="$attrs"></child2>
    </div>
</template>
```

### vm.$listeners
#### $listeners官方定义
> 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。

.native修饰符扩展
> 你可能有很多次想要在一个组件的根元素上直接监听一个原生事件。这时，你可以使用 v-on 的 .native 修饰符
> <base-input v-on:focus.native="onFocus"></base-input>


#### 用法
- 中间组件（子组件）绑定 v-on="$listeners" 将所有的事件监听器指向这个组件的某个特定的子元素
- 子/孙组件均可通过形如this.$listeners.test2()调用父组件的方法

```
<!--父组件 -->
<div>
    <!--这里 :one和:two是向后代组件传递数据-->
    <child1 :child1="child1"
            :child2="child2"
            @test1="onTest1"
            @test2="onTest2"></child1>
</div>

<!-- 子组件 child1 -->
<div class="child-1">
    <!-- 子组件中通过v-bind='$attrs'接受数据，通过$listeners接收事件 -->
    <child2 v-bind="$attrs" v-on="$listeners"></child2>
</div>
```
### 依赖注入
> 使用场景：$parent无法很好的扩展到更深层级的嵌套组件上。这也是依赖注入的用武之地，它用到了两个新的实例选项：`provide` 和 `inject`
> provide 和 inject 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。
#### provide
- 允许我们指定我们想要提供给后代组件的数据/方法
#### inject
- 任何后代组件里，我们都可以使用 `inject` 选项来接收指定的我们想要添加在这个实例上的 property

> 提示：provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的 property 还是可响应的。

#### 应用
- 一般应用
```
// 父级组件提供 'foo'
var Provider = {
  provide: {
    foo: 'bar'
  },
  // ...
}

// 子组件注入 'foo'
var Child = {
  inject: ['foo'],
  created () {
    console.log(this.foo) // => "bar"
  }
  // ...
}

//使用一个注入的值作为一个 property 的默认值
const Child = {
  inject: ['foo'],
  props: {
    bar: {
      default () {
        return this.foo
      }
    }
  }
}
// 使用一个注入的值作为数据入口：
const Child = {
  inject: ['foo'],
  data () {
    return {
      bar: this.foo
    }
  }
}
// 在 2.5.0+ 的注入可以通过设置默认值使其变成可选项：
const Child = {
  inject: {
    foo: { default: 'foo' }
  }
}
//如果它需要从一个不同名字的 property 注入，则使用 from 来表示其源 property：
const Child = {
  inject: {
    foo: {
      from: 'bar',
      default: 'foo'
    }
  }
}
//与 prop 的默认值类似，你需要对非原始值使用一个工厂方法：
const Child = {
  inject: {
    foo: {
      from: 'bar',
      default: () => [1, 2, 3]
    }
  }
}
```

- 利用 ES2015 Symbols、函数 provide 和对象 inject
```
const s = Symbol()

const Provider = {
  provide () {
    return {
      [s]: 'foo'
    }
  }
}

const Child = {
  inject: { s },
  // ...
}
```


