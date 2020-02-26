## 路由跳转传参
#### 方案一
```js
getDescribe(id) {
// 直接调用$router.push 实现携带参数的跳转
    this.$router.push({
      path: `/describe/${id}`,
    })

// 方案一，需要对应路由配置如下：
   {
     path: '/describe/:id',
     name: 'Describe',
     component: Describe
   }
// 很显然，需要在path中添加/:id来对应 $router.push 中path携带的参数。

// 在子组件中可以使用来获取传递的参数值。
this.$route.params.id
```

#### 方案二
```js
// 父组件中：通过路由属性中的name来确定匹配的路由，通过params来传递参数。
this.$router.push({
  name: 'Describe',
  params: {
    id: id
  }
})

// 对应路由配置: 注意这里不能使用:/id来传递参数了，因为父组件中，已经使用params来携带参数了。
{
 path: '/describe',
 name: 'Describe',
 component: Describe
}

//子组件中: 这样来获取参数
this.$route.params.id
```

#### 方案三
```js
// 父组件：使用path来匹配路由，然后通过query来传递参数
// 这种情况下 query传递的参数会显示在url后面?id=？
this.$router.push({
  path: '/describe',
  query: {
    id: id
  }
})

// 对应路由配置：
{
 path: '/describe',
 name: 'Describe',
 component: Describe
}

// 对应子组件: 这样来获取参数
$route.query.id

// 这里要特别注意 在子组件中 获取参数的时候是$route.params 而不是$router 这很重要~~~
```

***


## 组件间传参
### 组件间通信的六种方式
- 1.props/$emit
- 2.$emit/$on
- 3.vuex
- 4.$attrs/$listeners
- 5.provide/inject
- 6.$parent/$children 与 ref

#### 父组件向子组件传值

```js
<!-- 组件使用v-bind传值 -->
<router :msg="msg"></router>

子组件:
<p>子组件 ----- {{msg}}</p> 
props: ["msg"], //props接收
```

#### 子组件传递给父组件
```js
子组件:
<button @click="cyy">按钮</button>
methods: {
    cyy() {
        this.$emit("zifu", "子组件向父组件传值", true);
    }
}

父组件:
<router  v-on:zifu="hehe"></router>
methods: {
   hehe: function(data, data2) {
     console.log(data, data2);
   }
}
```

#### 兄弟组件传值
- 方法一  父组件中转
```html
<div> //爸爸A
    <router></router>       //哥哥A1
    <vuex></vuex>　　　　   //弟弟A2
</div>
```
    - A1要向A2传值 、 可以用$emit传给A、A在使用v-bind传给A2 

    - 使用父组件做中转 这里不举例了只是把上面的子向父，父向子连起来用

- 方法二 Bus中央事件总线
```js
  var bus = new Vue();
  Vue.component("zs", {
    template: '<div>这是张山<button @click="sb">传值</button></div>',
    methods: {
      sb() {
        bus.$emit("tt", {
          name: "我是zs"
        });
      }
    }
  });
  Vue.component("ls", {
    template: "<div>我是ls</div>",
    created() {
      bus.$on("tt", function(data) {
        console.log(data);
      });
    }
  });
```
