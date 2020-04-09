### Reflect
#### 概述
- 与 Proxy 一样为了操作对象新增的API
- 设计目的
    - 将一些明显只属于语言内部的方法放到 Reflect 上比如 `Object.defineProperty` 也就是说可以从 Reflect 上拿到语言内部方法
    - 修改某些Object方法的返回结果，让其更加合理 如`Object.defineProperty(obj, name, desc)` 没有定义属性时会抛出错误 `Reflect.defineProperty(obj, name, desc)` 会返回false
    - 让Object操作变为函数行为
    
    ```js
      // 旧写法
      'assign' in Object // true
      // 新写法
      Reflect.has(Object, "assign")// true 
    ```
    - Reflect 对象与 Proxy 对象的方法一一对应，Proxy有的Reflect 就有  Proxy对象拦截操作，方法内部调用Reflect相应的方法保证原生行为能正常执行
#### 静态方法

```text
- Reflect.apply(target, thisArg, args)
- Reflect.construct(target, args)
- Reflect.get(target, name, receiver)
- Reflect.set(target, name, value, receiver)
- Reflect.defineProperty(target, name, desc)
- Reflect.deleteProperty(target, name)
- Reflect.has(target, name)
- Reflect.ownKeys(target)
- Reflect.isExtensible(target)
- Reflect.preventExtensions(target)
- Reflect.getOwnPropertyDescriptor(target, name)
- Reflect.getPrototypeOf(target)
- Reflect.setPrototypeOf(target, prototype)
```

#### Reflect.get(target, name, receiver)

```js
var myObject = {
  foo: 1,
  bar: 2,
  get baz() {
    return this.foo + this.bar;
  },
};

var myReceiverObject = {
  foo: 4,
  bar: 4,
};
// 对象内部有 getter 则 读取函数的this绑定 receiver
Reflect.get(myObject, 'baz', myReceiverObject) // 8
```

#### Reflect.set(target, name, value, receiver)
```js
var myObject = {
  foo: 4,
  set bar(value) {
    return this.foo = value;
  },
};

var myReceiverObject = {
  foo: 0,
};

Reflect.set(myObject, 'bar', 1, myReceiverObject);
myObject.foo // 4
myReceiverObject.foo // 1
```
- 如果name属性设置了赋值函数，则赋值函数的this绑定receiver。
- 第一个参数为非对象 会报错

#### Reflect.has(obj, name)
- 判断对象中是否有某一属性或方法
- 第一个对象非方法会报错

#### Reflect.deleteProperty(obj, name)
- 等同于 delete obj[name] 用于删除对象属性
- 返回Boolean 删除成功或不存在则返回 true


#### Reflect.construct(target, args)
- 等同于 new target(args)
- 提供了一种不用 new 关键字创建实例的方式

```js
function Person(name){
    this.name = name;
}
// 有关键字 new 的写法
const instance = new Person("张三")

const personA = Reflect.construct(Person, ["小明"])
```

#### Reflect.getPrototypeOf(obj)
