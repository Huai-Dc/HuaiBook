## Vue 双向绑定原理
- mvvm 双向绑定，采用数据劫持结合发布者、订阅者模式。通过`Object.defineProperty()`劫持各个属性的`getter`、`setter`,在数据变动时通知订阅者，出发相应的监听回调。

![mvvm](../images/mvvm.png)

### 要点
- 1.实现一个数据监听器 `Observer` 能够对数据对象的所有属性进行监听，若有变动可拿到最新值并通知订阅者
- 2.实现一个指令解析器 `Compile` 对每个元素节点的指令进行扫描解析，根据指令模板替换数据，以及绑定相应的更新函数。
- 3.实现一个 `Watcher` 作为连接 `Observer`与`Compile`的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应回调，更新视图。

### 具体步骤
- 1.需要`Observer`的数据对象进行递归遍历，包含子属性对象的属性，都加上 `getter` `setter`,这样在给对象的属性赋值时就会触发`setter`监听到数据变化。
- 2.`Compile`解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面，并绑定对应节点的更新函数，添加监听数据的订阅者，一旦数据变化，收到通知，更新视图
- 3.`Watcher`主要作用为：
    - 在自身实例化时往属性订阅器（Dep）添加自己
    - 自身必须有一个 update 方法
    - 待属性变动 `dep.notice()` 通知时调用自身的update方法，并触发`Compile`中绑定的回调。
- 4.MVVM作为数据双向绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过Observer来监听自己的 model 数据变化，通过 Compile 来解析编译模板指令，最终利用 Watcher 搭起 Observer 和 Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据 model 变更的双向绑定效果。

***

## Proxy
- Proxy可以理解成在目标对象之前架设一层拦截，当外界访问该对象时，都必须经过拦截，有点类似代理
- Object.defineProperty 在使用数组方法操作或改变数组的下标是不能重新触发Object.defineProperty的set方法，所以做不到响应
- Object.defineProperty 必须遍历每个对象的属性，如果对象嵌套较深需要递归调用

### 基本语法
```js
const obj = new Proxy(target, handler);
// target 被代理对象
// handler 处理对象，声明了代理target的一些操作
// obj 代理后返回的对象
```

- 当外界操作obj时， 会执行handler上的方法
```js
get(target, propKey, receiver) // 目标对象  目标对象属性  该参数上下文 this
set(target, propKey, value, receiver) 
has(target, propKey)  // 判断是否有某一属性名
construct(target, args, newTarget)  // 目标对象  构造函数参数  实例对象   用于拦截 new 命令作用是拦截对象属性
apply(target, object, args)
```

```js
const target = {
  name: 'kongzhi'
};

const handler = {
  construct: function(target, args, newTarget) {
    /*
     输出： function A(name) {
              this.name = name;
           }
    */
    console.log(target); 
    // 输出： ['kongzhi', {age: 30}]
    console.log(args); 
    return args
  },
  get: function(target, key) {
    console.log(`${key} 被读取`);
    // 使用 Reflect来判断该目标对象是否有该属性
    if (Reflect.has(target, propKey)) {
      // 使用Reflect 来读取该对象的属性
      return Reflect.get(target, propKey);
    } else {
      throw new ReferenceError('该目标对象没有该属性');
    }
  },
  set: function(target, key, value) {
    console.log(`${key} 被设置为 ${value}`);
    target[key] = value;
  }
};

const testObj = new Proxy(target, handler);

/*
  获取testObj中name属性值
  会自动执行 get函数后 打印信息：name 被读取 及输出名字 kongzhi
*/
console.log(testObj.name);
/*
 改变target中的name属性值
 打印信息如下： name 被设置为 111 
*/
testObj.name = 111;

console.log(target.name); // 输出 111
```

### Proxy的数据双向绑定简单实现
- Vue类
```js
class Vue{
  constructor(options) {
    this.$el = document.querySelector(options.el);
    this.$methods = options.methods;
    this._binding = {}; // 保存所有指令及对应函数，数据变动时，通知订阅者
    this._observer(options.data);
    this._compile(this.$el);
  }
  _pushWatcher(watcher) {
      if (!this._binding[watcher.key]) { // 判断_binding 中有无 v-model的key，无则设置为空
          this._binding[watcher.key] = [];
      }
      this._binding[watcher.key].push(watcher);
  }
}
```
- 实现Observer对所有数据进行监听
```js
_Observer(datas){
    const scope = this;
    const handler = {
        set(target, key, value){
            const rets = Reflect.set(target, key, value);
            scope._binding[key].map(item => {
                item.update();
            })
            return rets;
        }
    }
    this.$data = new Proxy(datas, handler);
}
```

- Watcher类 连接Observer及Compile
```js
class Watcher{
    constructor(node, attr, data, key) {
        this.node = node;
        this.attr = attr;
        this.data = data;
        this.key = key;
    }
    updata(){
        this.node[this.attr] = this.data[this.key];
    }
}
```

- Compile 对页面中每个元素节点的指令进行扫描根据指令模板 替换数据，以及更新绑定函数
```js
_Compile(root){
    const nodes = Array.prototype.slice.call(root.children);
    const data = this.$data;
    // 扫描所有节点
    nodes.map(node => {
      if (node.children && node.children.length) {
        this._compile(node.children); // 递归
      }
      const $input = node.tagName.toLocaleUpperCase() === "INPUT";
      const $textarea = node.tagName.toLocaleUpperCase() === "TEXTAREA";
      const $vmodel = node.hasAttribute('v-model');
      // 如果是input框 或 textarea 的话，并且带有 v-model 属性的
      if (($vmodel && $input) || ($vmodel && $textarea)) {
        const key = node.getAttribute('v-model');
        this._pushWatcher(new Watcher(node, 'value', data, key));
        node.addEventListener('input', () => {
          data[key] = node.value; // 初始化
        });
      }
      // 单向绑定
      if (node.hasAttribute('v-bind')) {
        const key = node.getAttribute('v-bind');
        this._pushWatcher(new Watcher(node, 'innerHTML', data, key));
      }
      // 处理绑定函数
      if (node.hasAttribute('@click')) {
        const methodName = node.getAttribute('@click');
        // 取出函数
        const method = this.$methods[methodName].bind(data);
        node.addEventListener('click', method);
      }
    });
}
```
