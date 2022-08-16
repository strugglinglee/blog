---
title: vue-状态管理机制（eventBus vs VUEX）
date: 2020-06-14 20:30:06
tags:
- vuex
- eventBus
categories:
- vue
---

> 最初了解到eventBus和vuex并不清楚其原理，只知道无脑用，最近比较好奇这两者的区别，特以此文梳理

### eventBus
#### 定义
- 又称事件总线，作为组件共同的事件中心
- 组件都可以上下平行的通知其他组件

#### 工作原理
- 发布订阅模式
- 创建一个vue实例，通过一个空的vue实例作为桥梁实现vue组件间的通信

#### 缺点
- 不太方便，若不慎使用会造成难以维护的灾难，所以才需要更完善的vuex作为状态管理中心，将通知的概念上升到共享状态层次

#### 使用
- 初始化
    - 方法一
    ```
    // event-bus.js
    import Vue from 'vue'
    export const EventBus = new Vue()
    ```
    - 方法二
    > 创建一个全局事件总线
    ```
    // main.js
    Vue.prototype.$EventBus = new Vue()
    ```
- 可以维护一份公用数据或方法
```
export default new Vue({
    data() {
        return {
            searchShow: true,
            showBtn: true
        }
    },
    methods: {
        searchShowChange(value) {
            if (typeof value === 'boolean') {
                this.searchShow = false
            } else {
                this.searchShow = !this.searchShow
            }
            this.$emit('searchShowChange')
        }
    }
})
```


- `$on`/`$emit`/`$off`
    - $emit发送事件
    - $on接收事件
    - `$off移除监听者`
        - 十分重要，如果不手动清除，会一直存在，反复进入到接受组件内操作获取数据原本只有一次的操作将会有多次
        - 移除单个事件EventBus.$off('aMsg')
        - 移除所有事件EventBus.$off()
    ```
     beforeDestroy() {
        //组件销毁前需要解绑事件。否则会出现重复触发事件的问题
        this.bus.$off(this.$route.path);
     }
    ```


### VUEX
#### 产生背景

> 当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏:
    1.多个视图依赖于同一状态。
    2.来自不同视图的行为需要变更同一状态。

> 对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

#### 工作原理
- 运用到了js设计模式中的单例模式，单例模式想要做到的是，不管我们尝试去创建多少次，它都只给你返回第一次所创建的那唯一的一个实例。
- 在这种模式下，我们的组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为！
- 通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。

#### 定义
> 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

#### 使用
##### state
- 在计算属性中返回某个状态
> 缺点：需要频繁地导入，并且在测试组件时需要模拟状态
```
computed: {
    count () {
      return store.state.count
    }
}
```
- 通过 store 选项从根组件“注入”到每一个子组件中（需调用 Vue.use(Vuex)）
> 子组件能通过 this.$store 访问到
```
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})

const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```
- mapState 辅助函数
```
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```
- 给 mapState 传一个字符串数组
```
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```

- 对象展开运算符与局部计算属性混合使用
```
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```

##### Getter
- 使用情景
    - 从 store 中的 state 中派生出一些状态，例如对列表进行过滤并计数
- 以属性的形式访问
```
store.getters.doneTodos
```
- 接收两个参数：state/getters
```
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```
- 通过方法访问
> 注意，getter 在通过方法访问时，每次都会去进行调用，而不会缓存结果。
```
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```
- mapGetters 辅助函数
> mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性
```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}

//如果你想将一个 getter 属性另取一个名字，使用对象形式
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

##### Mutations
- 向 store.commit 传入额外的参数，即 mutation 的 载荷（payload）
> 在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读
```
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

store.commit('increment', {
  amount: 10
})

//对象风格的提交方式
store.commit({
  type: 'increment',
  amount: 10
})
```

- 在组件中提交 Mutation
    - this.$store.commit('xxx')
    - 使用 `mapMutations` 辅助函数将组件中的 methods 映射为 store.commit 调用
        ```
        import { mapMutations } from 'vuex'

        export default {
          // ...
          methods: {
            ...mapMutations([
              'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

              // `mapMutations` 也支持载荷：
              'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
            ]),
            ...mapMutations({
              add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
            })
          }
        }
        ```


- `注意事项`
    - 最好提前在你的 store 中初始化好所有所需属性。
    - 当需要在对象上添加新属性时，你应该使用 `Vue.set(obj, 'newProp', 123)`, 或者以新对象替换老对象。
        > 例如，利用对象展开运算符我们可以这样写:
        ```state.obj = { ...state.obj, newProp: 123 }```

- 使用常量替代 Mutation 事件类型
> 1.常量更容易避免程序出现错误。如果把一个值赋给程序中的一个常量，而该常量已经有一个值，编译器就回报告错误。
2.在其多人协作的时候，方便统一管理，而且在其状态管理当中调用方法时一眼就是看出是其mutation里面的方法，有问题直接快速找到。
 ```
 // mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
 ```

##### Actions
- vs Mutations
    - Action 提交的是 mutation，而不是直接变更状态。
    - Action 可以包含任意异步操作
- 基础使用
```
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

- 分发 Action
    ```
    store.dispatch('increment')
    // 以载荷形式分发
    store.dispatch('incrementAsync', {
      amount: 10
    })

    // 以对象形式分发
    store.dispatch({
      type: 'incrementAsync',
      amount: 10
    })
    ```
- 调用异步 API 和分发多重 mutation
    ```
    actions: {
      checkout ({ commit, state }, products) {
        // 把当前购物车的物品备份起来
        const savedCartItems = [...state.cart.added]
        // 发出结账请求，然后乐观地清空购物车
        commit(types.CHECKOUT_REQUEST)
        // 购物 API 接受一个成功回调和一个失败回调
        shop.buyProducts(
          products,
          // 成功操作
          () => commit(types.CHECKOUT_SUCCESS),
          // 失败操作
          () => commit(types.CHECKOUT_FAILURE, savedCartItems)
        )
      }
    }
    ```
 - 在组件中分发 Action
    - 与mutations类似,使用 this.$store.dispatch('xxx') 分发 action
    - 使用 mapActions 辅助函数将组件的 methods 映射为 store.dispatch 调用
    ```
        methods: {
        ...mapActions([
          'increment', // 将 `this.increment()` 映射为 this.$store.dispatch('increment')

          // mapActions 也支持载荷：
          'incrementBy' // 将 `this.incrementBy(amount)` 映射为 this.$store.dispatch('incrementBy', amount)
        ]),
        ...mapActions({
          add: 'increment' // 将 `this.add()` 映射为 this.$store.dispatch('increment')
        })
      }
    ```
 - 组合 Action

```
//利用 async / await，我们可以如下组合 action
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```
