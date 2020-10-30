## vue 小技巧
### 1.require.context()
#### 1.场景:如页面需要导入多个组件,原始写法:

```js
import titleCom from '@/components/home/titleCom'
import bannerCom from '@/components/home/bannerCom'
import cellCom from '@/components/home/cellCom'
components:{titleCom,bannerCom,cellCom}
```

#### 2.这样就写了大量重复的代码,利用 require.context 可以写成

```js
const path = require('path')
const files = require.context('@/components/home', false, /\.vue$/)
const modules = {}
files.keys().forEach(key => {
  const name = path.basename(key, '.vue')
  modules[name] = files(key).default || files(key)
})
components: modules
```

3.API 方法

```$xslt
实际上是 webpack 的方法,vue 工程一般基于 webpack,所以可以使用
require.context(directory,useSubdirectories,regExp)
接收三个参数:
directory：说明需要检索的目录
useSubdirectories：是否检索子目录
regExp: 匹配文件的正则表达式,一般是文件名
```

### 2.watch

#### 1.场景:表格初始进来需要调查询接口 getList(),然后input 改变会重新查询

```vue
created(){
    this.getList()
},
watch:{
    inpVal(){
        this.getList()
    }
}
```

#### 优化1 立即执行
- 可以直接利用 watch 的immediate和handler属性简写

```vue
watch: {
  inpVal:{
    handler: 'getList',
    immediate: true
  }
}
```

#### 优化2 深度监听
- watch 的 deep 属性,深度监听,也就是监听复杂数据类型

```vue
watch:{
  inpValObj:{
    handler(newVal,oldVal){
      console.log(newVal)
      console.log(oldVal)
    },
    deep:true
  }
}
```

- 此时发现oldVal和 newVal 值一样;
- 因为它们索引同一个对象/数组,Vue 不会保留修改之前值的副本;
- 所以深度监听虽然可以监听到对象的变化,但是无法监听到具体对象里面那个属性的变化

## 组件通讯

### props
- 这个应该非常常见的属性,就是父传子的属性;
- props 值可以是一个数组或对象;

```vue
// 数组:不建议使用
props:[]

// 对象
props:{
 inpVal:{
  type:Number, //传入值限定类型
  // type 值可为String,Number,Boolean,Array,Object,Date,Function,Symbol
  // type 还可以是一个自定义的构造函数，并且通过 instanceof 来进行检查确认
  required: true, //是否必传
  default:200,  //默认值,对象或数组默认值必须从一个工厂函数获取如 default:()=>[]
  validator:(value) {
    // 这个值必须匹配下列字符串中的一个
    return ['success', 'warning', 'danger'].indexOf(value) !== -1
  }
 }
}
```

### $emit
- 这个也应该非常常见,触发子组件触发父组件给自己绑定的事件,其实就是子传父的方法

```vue
// 父组件
<home @title="title">
// 子组件
this.$emit('title',[{title:'这是title'}])
```

### vuex
- 1.这个也是很常用的,vuex 是一个状态管理器
- 2.是一个独立的插件,适合数据共享多的项目里面,因为如果只是简单的通讯,使用起来会比较重

#### $attrs 与 $listeners

- 1.场景如果父传子有很多值那么在子组件需要定义多个解决attrs获取子传父中未在 props 定义的值

```vue
// 父组件
<home title="这是标题" width="80" height="80" imgUrl="imgUrl"/>

// 子组件
mounted() {
  console.log(this.$attrs) //{title: "这是标题", width: "80", height: "80", imgUrl: "imgUrl"}
},
```

- 相对应的如果子组件定义了 props,打印的值就是剔除定义的属性

```vue
props: {
  width: {
    type: String,
    default: ''
  }
},
mounted() {
  console.log(this.$attrs) //{title: "这是标题", height: "80", imgUrl: "imgUrl"}
},
```

- 2.场景子组件需要调用父组件的方法解决父组件的方法可以通过listeners" 传入内部组件——在创建更高层次的组件时非常有用

```vue
// 父组件
<home @change="change"/>

// 子组件
mounted() {
  console.log(this.$listeners) //即可拿到 change 事件
}
```

- 如果是孙组件要访问父组件的属性和调用方法,直接一级一级传下去就可以

### provide和inject

- provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中;并且这对选项需要一起使用;
- 以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

```vue
//父组件:
provide: { //provide 是一个对象,提供一个属性或方法
  foo: '这是 foo',
  fooMethod:()=>{
    console.log('父组件 fooMethod 被调用')
  }
},

// 子或者孙子组件
inject: ['foo','fooMethod'], //数组或者对象,注入到子组件
mounted() {
  this.fooMethod()
  console.log(this.foo)
}
//在父组件下面所有的子组件都可以利用inject
```

- provide 和 inject 绑定并不是可响应的。这是官方刻意为之的。
- 然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的,对象是因为是引用类型

```vue
//父组件:
provide: { 
  foo: '这是 foo'
},
mounted(){
  this.foo='这是新的 foo'
}

// 子或者孙子组件
inject: ['foo'], 
mounted() {
  console.log(this.foo) //子组件打印的还是'这是 foo'
}
```

### $parent与$children
