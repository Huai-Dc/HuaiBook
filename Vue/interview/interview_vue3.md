## 关于源码
- 1.vue/react 核心的源码比较
- Q: Vue3与Vue2的区别

```$xslt
vue2 采用的是ES5 Object.defineProperty 去劫持属性的get set 但是有个弊端，无法监听到数组内部的数据变化
Vue3 改用Proxy 说明vue 放弃了兼容IE 相比于Object.defineProperty 优势是可以检测到数组内部数据变化
```

2.全家桶相关内容 vuex/redux aixos vue-router
- Q：像vue-router，vuex都是vue的插件，他们是如何在vue中生效的
```$xslt
通过vue的插件系统，用vue.mixin 混入到全局，在每个组件的生命周期的某个阶段注入组件实例
```

3.koa lodash 源码阅读（加分）
- Q：vue的设计架构
```$xslt
vue2 采用的是典型的混入式架构，类似于express和jquery各部分分模块开发，再通过一个mixin去混入到最终暴露到全局的类上
```

## 关于项目
- 类似的问题，在项目中都做了些什么事情

- 错误示范
```$xslt
Q: 请说一下你这个项目中做的事情

A: 这个项目使用vue，利用vue全家桶配合后端调用接口，完成项目开发。
```

- 该怎么回答 首先记住几个名词 ssr 组件库 工程化建设 移动混合方案 性能优化方案
- 正确示范
```$xslt
A: 这个项目主体是一个 vue 项目，但是因为是PC端 为了seo 我特意做了ssr 然后这个项目有一套我和同事一起做的专门的组件库，在移动端我们为了搭配app
也做了移动混合方案，像在首页因为数据比较大采用了一些优化方案，利用本地缓存数据，对小图标进行了base64 转码
```

- PS 这样的回答能充分显示出你在技术上的能力
