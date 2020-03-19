## vue 性能优化
### template
- v-if 与 v-show 区分场景使用，v-if 是真正的条件渲染， v-show 只是切换 css
- 别在模板中写过多的判断，可以通过method、computed 封装成方法
- 循环组件添加key值
- v-for 必须加上 key，且避免同时使用 v-if
    - key能提高 diff算法的效率
    - v-for的优先级比v-if高，会影响速度，可以先通过 computed 筛选后遍历

### style
- 单独的模块上要加 scoped 防止与其他人员样式代码冲突
- 尽量少用浮动和定位， 能用flex处理的尽量用flex

### script
- methods中的方法尽量简单只做单一事情，尽量封装可复用的简短方法，参数不易过多， lodash 要按需引入 否则体积会过大
- computed 和 watch 要区分使用场景
    - computed 计算属性，依赖于其他属性值，计算后的值会被缓存，只有依赖值改变后才会重新计算，（目的快速取得结果值）
    - watch 数据的监听回调，当需要在数据变化时执行异步或者开销较大的操作时，使用watch，watch允许我们执行异步操作，限制我们执行改操作的频率，并且能在得到结果时设置中间状态，这些都是 computed 无法做到的。
    ```vue
      <template>
        <div>
          <div>child:</div>
          <div>修改前：{{oldUser}} 修改后：{{user}}</div>
        </div>
      </template>
      <script>
      export default {
        name: 'child',
        props: ['user'],
        data () {
          return {
            oldUser: {}
          }
        },
        watch: {
          user: {
            handler (val, oldVal) {
              this.oldUser = oldVal || val
            },
            deep: true,
            immediate: true
          }
        }
      }
      </script>
    ```
    - 因为watch 没有做缓存 所以修改前后的值是一样的同一个对象,可通过computed 具有缓存的特性来实现比较新旧值得功能
    ```vue
    <template>
      <div>
        <div>child:</div>
        <div>修改前：{{oldUser}} 修改后：{{user}}</div>
      </div>
    </template>
    <script>
    export default {
      name: 'child',
      props: ['user'],
      data () {
        return {
          oldUser: {}
        }
      },
      // 缓存 userInfo   
      computed: {
        userInfo () {
          return { ...this.user }
        }
      },
      watch: {
        userInfo: {
          handler (val, oldVal) {
            this.oldUser = oldVal || val
          },
          deep: true,
          immediate: true
        }
      }
    }
    </script>
    ```
    - 减少watch 的数据，watch 数据小性能消耗不明显，当watch的大系统会卡顿，其他状态的双向绑定可以通过 evenbus 或者vuex
    
### 组件优化
- 当把所有组件都写到一个组件中时， 当数据变化时， 由于代码庞大，vue的数据驱动视图会比较慢造成渲染较慢，所以要把组件细分，如列表组件、按钮组件等
- 组件异步加载 路由中 （路由懒加载）
```vuejs
{
    components: () => import("path")
}
```

### 打包优化
- 打包时不打包 vue vuex axios等 换用国内cdn 浏览器内存最多执行 40 个 进程，采用cdn的方式可以缓解服务器的压力， 原理是将我们服务器的压力分给其他服务器点
- 需要配置externals 才能排除不需要打包的包

### 长列表性能优化
- Vue会通过 Object.definedProperty 对数据进行劫持，来实现双向绑定，如果数据完全不会改变 就能通过 禁止vue劫持数据可以明显减少组件初始化时间，可以通过 Object.freeze冻结一个对象，一旦冻结数据就不能被更改
```vuejs
export default {
    data: () => ({
        users: {},
    }),
    async created(){
        const users = await axios.get("/api/getUser")
        this.users = Object.freeze(users)
    },
}
```
#### 优化无限列表性能
- 采用窗口化的技术优化性能，只需渲染少部分区域内容，减少重新渲染组件和创建dom节点的时间 参考vue-virtual-scroll-list 和 vue-virtual-scroller 
    - 列表数据不需要修改，纯展示，不需要响应式，Object.freeze 可以阻止vue追踪属性的变化，减少性能开销
    - 展示数据大量且经常变化数据集，为减少重绘与回流，列表做绝对定位 脱离文档流 防止页面抖动

### 事件销毁
- Vue 组件销毁时会自动清理他与其他实例的链接，解绑它的全部指令以及事件监听器，但是仅限于组件本身，如果是在函数内部绑定的就不会被销毁
```vuejs
created(){
    addEventListener('click', this.click, false)
},
beforeDestory(){
    removeEventListener('click', this.click, false)
}
```

### 图片资源懒加载
- vue-lazyload插件


### 第三方插件按需引入
- 安装 babel-plugin-component 


### 服务端渲染 SSR or 预渲染
- 服务端渲染优点
    - 更好的seo SPA页面的内容是通过 ajax获取 SSR 直接由服务端渲染好页面（数据已经包含在页面中）所以搜索引擎工具可以抓取渲染好的页面
    - 首屏加载更快 SPA 会等待vue 编译后所有的文件加载完毕才开始渲染页面 SSR 直接由服务端渲染返回无需等待js文件加载
- 缺点
    - 更多开发条件的限制 服务端只支持 beforeCreate 与 create 两个钩子函数，服务端渲染应用程序，需要处于node.js server 运行环境
    - 更多的服务器负载
    
### webpack 层面优化
- 对图片进行压缩  image-webpack-loader
- 减少es6 转 es5的冗余代码  安装 babel-plugin-transform-runtime 解决
- 提取公共代码 
- 模板预编译 
- 提取组件 css 服务端渲染的情况下 组件内的css 会以style的方式 通过js 动态注入，可能导致一段 无样式内容闪烁 将所有css 提取到同一个文件可以避免该问题， 也可以让css 更好的压缩及缓存
- 优化 sourceMap 
    - 生产环境  cheap-module-source-map
    - 开发环境  cheap-module-eval-source-map
    
- 构建结果输出分析 分析工具（webpack-bundle-analyzer）  生成报告（npm run build --report）

### 基础的web 技术优化
- 开启 gzip 压缩（服务端）

```js
npm install compression --save  // 安装

// 逻辑代码
var compression = require('compression');
var app = express();
app.use(compression())
```

- 浏览器缓存

- cdn 加速

- chromePerformance查找性能瓶颈
  