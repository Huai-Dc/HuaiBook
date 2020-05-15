https://zhuanlan.zhihu.com/p/92590306 基础题目

## 闭包相关
### 是什么
- 此处提问者想了解的是
    - 为什么其他非闭包的函数没有权限访问另一个函数的内部作用域
    - 为什么闭包有这个权限
    - 什么是函数作用域
- 有权访问其他函数作用域内的变量的一个函数（js高程）
- 由于在js中，变量的作用域属于函数作用域，在函数执行后作用域会被清理，内存也被回收，但是闭包是建立在一个函数内部的子函数，由于其可以访问上级作用域，即使上级执行完毕，作用域也不会被销毁，这时候的子函数就是闭包。

### 闭包解决了什么
- 由于闭包可以缓存上级作用域，那么就能使函数外部打破“函数作用域的束缚”，可以访问函数内部变量。平时写的Ajax回调就是典型的闭包，由于闭包的特性，回调就有访问与操作整个上级作用域的能力，开发者就不用写钩子操作上级作用域内部变量了


### 闭包应用场景
- 之前的数字序列生成器就是闭包的应用场景之一
- ajax的请求回调，事件绑定的回调，setTimeout的延时回调等都是闭包

### 优缺点
- 优点
    - 减少全局变量
    - 减少传递函数的参数量
    - 封装

- 缺点
    - 闭包会占有内存资源，过多使用会导致内存溢出（所以不需要用到的变量，要置为null或undefined）

## 原型与原型链
### 什么是原型
- js中所有东西都可以看作对象，但是对象还分普通对象与函数对象

### _proto_与prototype
- 每个对象都有一个 `__proto__`属性，并且指向 `prototype` 原型对象
- 每个构造函数都有一个`prototype`原型对象
    - `prototype`原型对象的的`constructor` 指向构造函数
    - 实例对象的`__proto__`指向构造函数的`prototype`从而实现继承
    - `prototype`对象相当于特定类型所有实例对象都可以访问的公共容器

```js
function Person(nick, age){
    this.nick = nick;
    this.age = age;
}
Person.prototype.sayName = function(){
    console.log(this.nick);
}

var p1 = new Person('Byron', 20);

var p2 = new Person('Casper', 25);

p1.sayName()  // Byron

p2.sayName()  // Casper

p1.__proto__ === Person.prototype       //true

p2.__proto__ === Person.prototype   //true

p1.__proto__ === p2.__proto__           //true

Person.prototype.constructor === Person  //true
```

- PS
    - 建议少用 `Object.prototype.__proto__`, 只使用 `Object.getPrototypeOf()`
    - Object.create(null)新建的对象时没有 `__proto__`属性的

### 原型链
- demo

```js
var arr = [1,2,3]

arr.valueOf()  //  [1, 2, 3]
```

- 通过浏览器输出 arr对象，在其 `__proto__` 中是找不到 `valueOf`方法的
- 但是我们能在 `Array.prototype.__proto__`中找到 `valueOf`
- 查找过程
    - 当前实例对象obj，查找obj属性或方法
    - 没有找到，通过`obj.__proto__` 查找，找到obj构造函数的prototype并查找对应的属性或方法，找到则返回
    - 没有找到把`Array.prototype`当做obj重复以上步骤 
    - 最后查到`Object.prototype`时， `Object.proptotype.__proto__ === null` 查找结束

```js
arr.__proto__ === Array.prototype
// true
Array.prototype.__proto__ === Object.prototype
// true
arr.__proto__.__proto__ === Object.prototype
// true

// 原型链的终点
Object.prototype.__proto__ === null
// true
```

- 原型链如下
```js
arr ---> Array.prototype ---> Object.prototype ---> null
```
- 这就是传说中的原型链，层层向上查找，最后还没有就返回undefined

## 继承
### 什么是继承
- 一个对象直接使用另一个对象的属性与方法

### 属性如何继承
- 先创建一个Person 类

```js
function Person (name, age) {
    this.name = name
    this.age = age
}

// 方法定义在构造函数的原型上
Person.prototype.getName = function () { console.log(this.name)}
```

- 此时我们想创建Teacher类并希望他可以继承Person 类

```js
function Teacher (name, age, subject) {
    Person.call(this, name, age)
    this.subject = subject
}
```
- 属性的继承就是通过在一个类内执行另一个构造函数，并通过call指定this为当前执行环境，这样就能得到另一个类的所有属性

### 继承方法
- 我们都知道方法定义在`prototype`中，那么我们只需要将Person的prototype的备份赋值给Teacher的prototype

```js
Teacher.prototype = Object.create(Person.prototype)
```
- 不能直接赋值是因为直接赋值就是引用关系，修改Teacher的prototype也会影响到Person的prototype
- 给Teacher添加方法应该放在修改prototype后否则会被覆盖，因为赋值前后对象不一样了
- 还有我们知道 prototype 的 constructor 指向构造函数本身，赋值后Teacher的构造变成了Person，指向错误需要更正

```js
Teacher.prototype.constructor = Teacher
```

```js
Teacher.prototype = Object.create(Person.prototype)

Teacher.prototype.constructor
// ƒ Person(name, age) {
//     this.name = name
//     this.age = age
// }

---

Teacher.prototype.constructor = Teacher
// ƒ Teacher(name, age, subject) {
//     Person.call(this, name, age)
//     this.subject = subject
// }
```

- 最终方案

```js
Teacher.prototype = Object.create(Person.prototype)
Teacher.prototype.constructor = Teacher
```

### hasOwnProperty
- 在原型链上查询属性比较耗时，对性能有影响，视图访问不存在的属性时会遍历整个原型链，需要检查是否具有自己定义的属性，而不是原型链上的属性，必须用 hasOwnProperty
- hasOwnProperty 是唯一处理属性不会遍历原型链的方法

### 总结
#### prototype 和 __proto__

- 每个对象都有一个__proto__属性，并且指向它的prototype原型对象
- 每个构造函数都有一个prototype原型对象
    - prototype原型对象里的constructor指向构造函数本身

#### 原型链
- 每个对象都有一个__proto__，它指向它的prototype原型对象，而prototype原型对象又具有一个自己的prototype原型对象，就这样层层往上直到一个对象的原型prototype为null

- 这个查询的路径就是原型链