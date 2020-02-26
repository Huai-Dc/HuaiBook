## 虚拟DOM相关
### 什么是虚拟DOM
- 虚拟DOM是对DOM的抽象，本质上试JavaScript对象，是对DOM的描述
### 为什么需要VDOM
- 之前前端的优化就是尽量减少DOM操作，不仅是因为DOM操作消耗大而且频发的DOM操作
会造成浏览器的回流（重排，Reflow）或重绘，大大影响了性能，因此抽象出一层可以保证DOM不会出现DOM性能很差的情况
- 现代前端框架基本要求就是无需操作DOM，一方面因为多人协作可能出现某些人写出性能较低的代码，而且省略手动操作DOM3提高开发效率
- 可以更好的跨平台，比如Node就没有DOM，若想实现SSR(服务端渲染)，就要借助VDOM

### VDOM关键要素
#### VDOM的创建
- VDOM是对真实DOM的抽象，根据不同的需求做出不同的抽象
```js
{
  type, // String，DOM 节点的类型，如 'div'
  data, // Object，包括 props，style等等 DOM 节点的各种属性
  children // Array，子节点
}
```
- 生成函数
```js
// 生成 vnode
// @param  {String} type     类型，如 'div'
// @param  {String} key      key vnode的唯一id
// @param  {Object} data     data，包括属性，事件等等
// @param  {Array} children  子 vnode
// @param  {String} text     文本
// @param  {Element} elm     对应的 dom
// @return {Object}          vnode
function vnode(type, key, data, children, text, elm) {
  const element = {
    __type: VNODE_TYPE,
    type, key, data, children, text, elm
  }

  return element
}
```
#### 创建vdom tree

#### vdom更新
- 根据type生成不同的dom，并把data中的属性设置到DOM上

#### vdom的diff算法
- 目的就是为了比较新旧dom tree的差异并更新
- 双端比较 头头对比 尾尾对比  key对比

#### 优化

***

### 既然vue通过数据劫持可精准探测数据变化,为什么还需要虚拟dom进行diff检测差异
- 前置知识：依赖收集、虚拟DOM、响应式系统
- 现代框架主要有两种方式侦测变化，pull push
- react 是pull的代表，通常在react中会用 setState 来通知框架状态发生了改变。
- vue 的响应式系统则是 push的代表，当vue程序初始化时就会对数据data进行依赖收集，一旦数据发生改变，响应式系统会立刻知道
但是这会产生一个问题，通常绑定一个数据就需要一个 Watcher，一旦绑定的粒度过高会产生大量的Watcher，这会带来内存及依赖追踪的开销
vue选择的是pull+push的操作 vdom diff 是pull 操作

### key 的作用
vue 用v-for 更新已渲染的的元素时，默认用就地复用策略，如果数据项的顺序被改变，vue不会移动DOM来匹配顺序，
而是简单的复用此处每个元素，并确保它在特定索引下显示已被渲染过的每个元素。
key的作用就是为了高效的更新虚拟DOM


