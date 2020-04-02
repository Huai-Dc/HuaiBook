## store的替代方案
### bus 中央事件总线
```js
// vue-bus.js
// Vue.use(对象) 时 会自动执行 install 方法
const install = (Vue) => {
    const Bus = new Vue({
        methods: {
            emit(event, ...args){
                this.$emit(event, ...args)
            },
            on(event, callback){
                this.$on(event, callback)
            },
            off(event, callback){
                this.$off(event, callback)
            },
        }   
    })
    Vue.prototype.$bus = Bus;
}
export default install;
```

```js
// mian.js
import Bus from '../vue-bus.js'

Vue.use(Bus);

// 使用  组件1
this.$bus.emit("add", params);

// 组件2、
this.bus.on("add", params => {
    // code
})
```
#### PS 注意事项
- $bus.on 需要在created 阶段使用 在 mounted 可能接受不到其他组件来自 created 发出的事件
- 需要手动解绑 在 beforeDestroy 中解除绑定

### observable
#### 用法
让一个对象可响应，返回的对象可以直接用于 渲染函数与计算属性
```js
// store.js
let store = Vue.observable({num: 1, name: "法外狂徒张三"})
let mutations = {
    changeNum(num){
        store.num = num
    },
    changeName(name){
        store.name = name
    }
}
```

```js
import {store，mutations} from './store'
{   // 取值
    computed:{
        num(){
            return store.num
        }
    },
    // 修改
    methods:{
        changeNum(num){
            mutations.changeNum(num)
        }
    }
}
```



***


## Vuex
- vuex 数据流图
![vuex](../images/vuex.png)

### 核心概念
### state
- 使用单一状态树，整个应用只有一个唯一的store
#### 在组件中获取Vuex状态
- 最简单的方法，在计算属性中返回
```js
{
    computed:{
        count() {
            return store.state.count;
        }
    }
}
```

- PS Vuex 的状态存储是响应式的，当count变化时都会重新计算，并触发关联的DOM更新
- 通过注入的store访问
```js
return this.$store.state.count
```
#### mapState 辅助函数
```js
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

### Getter
- 当多个组件需要使用同一个属性，Vuex允许定义 getter （可以看成是store的计算属性），getter的返回值
会根据其依赖被缓存，当值变化时才会重新计算
- getter 接受 state作为其第一个参数
#### 通过属性访问
```js
store.getters.count
```

#### 通过方法访问
- 可以让 getter返回一个函数，实现给getter传参
```js
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```
- PS getter 通过方法访问时，每次都会进行调用不会对结果进行缓存

#### mapGetter 辅助函数
- 将store中的getter映射到局部计算属性
```js
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
```

### Mutation
- 改变store中状态的唯一方法是提交 mutation 
- 使用常量代替mutation 事件类型 方便协作者读取
```js
export const SOME_MUTATION = 'SOME_MUTATION'
```
- mutation 必须是同步函数，异步需要写到 action中
- 组件中提交mutation
```js
this.$store.commit("xxxx")
```


### Action
- Action 提交的是 mutation 而不是直接改变状态
- Action 可以包含任意异步操作
- 分发Action， 通过store。dispatch 方法触发
- 可以在一个 action方法中分发多个 mutation
