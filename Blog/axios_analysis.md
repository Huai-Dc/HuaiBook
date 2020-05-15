## Axios 源码解析
- axios 是基于Promise的http请求库，

### 名词解释
#### 拦截器
- 起到基于promise的中间件作用
- 分为请求拦截器与响应拦截器
    - 请求拦截器：拦截住每次或指定的http请求，并可修改配置项
    - 响应拦截器：在每次http请求后拦截http请求，并可修改响应返回结果项

#### 数据转换器
- 就是对数据格式的转换，如将对象转为JSON字符串
    - 请求转换器，请求前对数据进行转换
    - 响应转换器，请求后对响应体做数据转换

#### http请求适配器
- 在axios中，http请求适配器主要指两种，XHR,http
    - XHR的核心是浏览器的XMLHttpRequest对象
    - http则是node的http[s].request 对象

### 工具方法
#### bind(fn, context)
- 效果跟Function.prototype.bind一样，给函数绑定this指向

#### forEach
- 遍历数组或对象
```js
var utils = require('./utils');
var forEach = utils.forEach;

// 数组
utils.forEach([], (value, index, array) => {})

// 对象
utils.forEach({}, (value, key, object) => {})
```

#### merge
- 深度合并多个对象为一个对象
- 类似 $.extend 或 Object.assign,但是$.extend 与 Object.assign 不会深度合并

```js
var obj1 = {
  a: 1,
  b: {
    bb: 11,
    bbb: 111,
  }
};
var obj2 = {
  a: 2,
  b: {
    bb: 22,
  }
};
var mergedObj = merge(obj1, obj2); 
mergedObj = {
    a: 2,
    b:{
        bb: 22,
        bbb: 111,
    }
}

// 如果是 $.extend 或Object.assign
var mergedObj_2 = Object.assign(obj1, obj2)
mergedObj_2 = {
    a:2,
    b:{ // 此处的 bbb 属性并没有被合并到最终对象上
        bb:2
    }
}
```

#### extend
- 将一个对象方法与属性拓展到另一个对象上，并指定上下文
```js
var utils = require('./utils');
var extend = utils.extend;

var context = {
  a: 4,
};
var target = {
  k: 'k1',
  fn(){
    console.log(this.a + 1)
  }
};
var source = {
  k: 'k2',
  fn(){
    console.log(this.a - 1)
  }
};
let extendObj = extend(target, source, context);

// {
//   k: 'k2',
//   fn: source.fn.bind(context),
// }

```